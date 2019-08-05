



|                                    |                                                       |      |
| ---------------------------------- | ----------------------------------------------------- | ---- |
| --check-column id                  | sqoop以此为依据做增量更新，可以使用create_time或id    |      |
| --last-value '2018-10-26 14:14:31' | 大于此值的将数据将被认为是新的数据，job会自动记录该值 |      |
|                                    |                                                       |      |
|                                    |                                                       |      |
|                                    |                                                       |      |
|                                    |                                                       |      |



```shell
# 将数据导入到hdfs的同时，也导入到hive中
--hive-import	

# 用于控制导出数据到hive时的表名，如果跟原始表名一致，则可以不指定
--hive-table

# 在append模式下，sqoop通过检测check-column所指定的列的值与last-value比较来判断哪些是新数据
--check-column <column>
--last-value v

# 当希望并发导入的时候，可以使用 -m4 这种指定并发度，此时默认情况下使用primary key并发导入数据，然而有时候table并没有主键，此时只能手动指定一个带索引的列以支持并发导入
# 如果一个表既没有primary key也没有手动指定split-by，则会导入失败，需要手动指定 -m1 串行导入
--split-by <column>


# 大表导入
sqoop import \
      --connect $jdbcConn \
      --username $mysqlUsername \
      --password-file /coinbene_exchange-password-file \
      --table ${table} \
      --hive-database ${database} \
      # 指定表所在的数据库，如果不指定，则会放到default库
      --hcatalog-database ${database} \
      --hcatalog-table ${table} ${create_hcatalog_table} \
      --hcatalog-storage-stanza "stored as orc tblproperties ('orc.compress'='SNAPPY')" \
      --hcatalog-partition-keys dt \
      --hcatalog-partition-values ${big_table_partition_time} \
      -m4 --split-by ${split_by} \
      --where "${split_by} > '${last_time}' and ${split_by} <= '${big_table_select_upper_limit}'" \
      || error_exit "导入 ${table} 失败"
```













----

0x09 References

1. [Sqoop User Guide](https://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html)
2. [sqoop 学习](https://www.jianshu.com/p/f0c93c53cd70)
3. 