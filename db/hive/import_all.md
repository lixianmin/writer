```shell
#!/bin/bash

function import_small_table() {
  args=(${1//|/ })
  table=${args[0]}
  split_by=${args[1]}

  if [[ ${split_by} != '' ]] ; then
    split_by="--split-by ${split_by}"
  fi

  hive -e "USE $database; DROP TABLE IF EXISTS ${table}_" -S || error_exit "清除临时表 ${table}_ 失败"
  sqoop import \
    -Dorg.apache.sqoop.splitter.allow_text_splitter=true \
    --connect $jdbcConn \
    --username $mysqlUsername \
    --password-file /coinbene_exchange-password-file \
    --table ${table} \
    --hcatalog-database $database \
    --hcatalog-table ${table}_ --create-hcatalog-table \
    --hcatalog-storage-stanza 'stored as orc' \
    -m4 ${split_by} \
    || error_exit "导入 ${table}_ 失败"

  hive -e "USE $database; DROP TABLE IF EXISTS $table; ALTER TABLE ${table}_ RENAME TO $table;" || error_exit "临时表 ${table}_ 改名失败"
  echo "导入 $table 成功"
}

function import_big_table_by_time {
  table=$1
  split_by=$2

  # 在某些条件下跳出循环
  old_last_time=''
  break_counter=1

  while :
  do
    echo "开始导入 ${table}"

    # 查询max_dt，不能直接将max_dt作为cet或子查询的形式，因为这本质上会变成join，然而hive并不会提前知道查询结果，所以仍然会去每一个分区查询一遍，导致查一遍需要10分钟
    max_dt=$(echo $(hive -e "USE ${database}; select max(dt) max_dt from ${table};") | awk '{print $2}') || error_exit "从${table}选取max_dt失败"

    # hive存储时间用的是string，注意精度到到 hh:mm:ss.0，字符串比较时要注意最后一位
    # 只取最大分区的那个最大时间就可以了
    last_time=$(echo $(hive -e "USE ${database}; select nvl(max(${split_by}),'empty_table') as t from ${table} where dt=${max_dt};") | awk '{print $2, $3}') \
    || error_exit "从${table}选取最大${split_by}失败"

    echo "query_last_time: table=|${table}|, max_dt=|${max_dt}|, last_time=|${last_time}|, old_last_time=|${old_last_time}|, break_counter=|${break_counter}|, split_by=|${split_by}|"

    if [[ ${old_last_time} == ${last_time} ]] || [[ ${break_counter} -gt 20 ]]; then
      break
    fi

    # 如果表不存在，则last_time会是一个单空格字符串(原因是awk中组合了$2, $3)，包括empty_table后面都会带一个空格
    create_hcatalog_table=''
    if [[ ${last_time} == ' ' ]] || [[ ${last_time} == 'empty_table ' ]] ; then
      # 如果是没有表，则加入创建表的参数；如果只是empty_table，则需要跳过这一步
      if [[ ${last_time} == ' ' ]] ; then
        create_hcatalog_table='--create-hcatalog-table';
      fi

      # 这个时间需要微调1s，因为区间是左开右闭，不微调会丢失第一交易的数据
      # select的结果必须要as一把，否则awk部分需要调整
      last_time=$(echo $(mysql -u"$mysqlUsername" -p"$mysqlPassword" -h$mysqlHost $database -e "select date_sub(min(${split_by}), interval 1 microsecond) as t from ${table};")|awk '{print $2, $3}')

      echo "set_initial_last_time of |${table}| = |${last_time}|"
    fi

    # next_time = min (last_time + 1day, big_table_select_upper_limit)
    next_time=$(date -d "${last_time} CST +1 day" "+%Y-%m-%d %H:%M:%S")
    if [[ $(date +%s -d "${next_time}") -gt $(date +%s -d "${big_table_select_upper_limit}") ]] ; then
      next_time=${big_table_select_upper_limit}
    fi

    echo "table=|${table}|, last_time=|${last_time}|, next_time=|${next_time}|, big_table_select_upper_limit=|${big_table_select_upper_limit}|, split_by=|${split_by}|"

    sqoop import \
        --connect $jdbcConn \
        --username $mysqlUsername \
        --password-file /coinbene_exchange-password-file \
        --table ${table} \
        --hcatalog-database ${database} \
        --hcatalog-table ${table} ${create_hcatalog_table} \
        --hcatalog-storage-stanza "stored as orc tblproperties ('orc.compress'='SNAPPY')" \
        --hcatalog-partition-keys dt \
        --hcatalog-partition-values ${big_table_partition_time} \
        -m4 --split-by ${split_by} \
        --where "${split_by} > '${last_time}' and ${split_by} <= '${next_time}'" \
        || error_exit "导入 ${table} 失败"

    echo "导入 $table 成功"

    # delta_time单位秒，如果小于1day，则跳出
    delta_time=$(($(date +%s -d "${big_table_select_upper_limit}") - $(date +%s -d "${last_time}")));
    if [[ ${delta_time} -lt $((24*60*60)) ]] ; then
      break
    fi

    old_last_time=${last_time}
    break_counter=$((${break_counter} + 1))
    echo "continue_import |${table}|, delta_time=|${delta_time}|, big_table_select_upper_limit=|${big_table_select_upper_limit}|, last_time=|${last_time}|, old_last_time=|${old_last_time}|, break_counter=|${break_counter}|"
  done
}

```

