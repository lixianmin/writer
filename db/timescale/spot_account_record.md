

```mysql
-- 安装timescaledb CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
-- 更改目录所有者 chown postgres:postgres /data/disk1/db
-- 更改目录权限 chmod 755 /data/disk1/db
-- chown postgres:postgres /data/disk2/db
-- chmod 755 /data/disk2/db
-- chown postgres:postgres /data/disk3/db
-- chmod 755 /data/disk3/db
-- chown postgres:postgres /data/disk4/db
-- chmod 755 /data/disk4/db
-- 创建tablespace CREATE TABLESPACE disk1 LOCATION '/data/disk1/db';
-- CREATE TABLESPACE disk1 LOCATION '/data/disk1';
-- CREATE TABLESPACE disk2 LOCATION '/data/disk2';
-- CREATE TABLESPACE disk3 LOCATION '/data/disk3';
-- CREATE TABLESPACE disk4 LOCATION '/data/disk4';
-- CREATE TABLESPACE disk5 LOCATION '/data/disk5';
-- CREATE TABLESPACE disk6 LOCATION '/data/disk6';
-- CREATE TABLESPACE disk7 LOCATION '/data/disk7';
-- CREATE TABLESPACE disk8 LOCATION '/data/disk8';
-- CREATE TABLESPACE disk9 LOCATION '/data/disk9';
-- CREATE TABLESPACE disk10 LOCATION '/data/disk10';


-- drop table rc.spot_account_record ;
CREATE TABLE rc.spot_account_record (
    id bigint,
    no bigint,
    user_id integer,
    bank_id integer,
    type integer,
    asset_id integer,
    asset character varying(20) DEFAULT ''''''::character varying,
    biz_id bigint,
    biz_type integer,
    biz_sub_type integer,
    total_balance_before numeric(54,18),
    total_balance_after numeric(54,18),
    frozen_balance_before numeric(54,18),
    frozen_balance_after numeric(54,18),
    create_time timestamp without time zone NOT NULL,
    ext1 character varying(50),
    ext2 character varying(50),
    ext3 integer DEFAULT 0,
    ext4 integer DEFAULT 0,
    sync_time timestamp with time zone NOT NULL DEFAULT now()
)WITH (OIDS = FALSE) TABLESPACE pg_default;
ALTER TABLE rc.spot_account_record OWNER to postgres;

SELECT create_hypertable('rc.spot_account_record', 'create_time', chunk_time_interval => interval '2 weeks');
SELECT add_dimension('rc.spot_account_record', 'user_id', number_partitions => 400);

SELECT attach_tablespace('disk1', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk2', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk3', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk4', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk5', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk6', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk7', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk8', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk9', 'rc.spot_account_record', if_not_attached => true);
SELECT attach_tablespace('disk10', 'rc.spot_account_record', if_not_attached => true);



COMMENT ON TABLE rc.spot_account_record IS '用户账户变更明细表';
COMMENT ON COLUMN rc.spot_account_record.no IS '交易流水号';
COMMENT ON COLUMN rc.spot_account_record.user_id IS '用户id';
COMMENT ON COLUMN rc.spot_account_record.bank_id IS '交易所id';
COMMENT ON COLUMN rc.spot_account_record.type IS '类型 1现货2期货 3余币宝';
COMMENT ON COLUMN rc.spot_account_record.asset_id IS '资产id';
COMMENT ON COLUMN rc.spot_account_record.asset IS '资产名称';
COMMENT ON COLUMN rc.spot_account_record.biz_id IS '业务id';
COMMENT ON COLUMN rc.spot_account_record.biz_type IS '业务类型';
COMMENT ON COLUMN rc.spot_account_record.biz_sub_type IS '业务子类型';
COMMENT ON COLUMN rc.spot_account_record.total_balance_before IS '变更前账户余额';
COMMENT ON COLUMN rc.spot_account_record.total_balance_after IS '变更后账户余额';
COMMENT ON COLUMN rc.spot_account_record.frozen_balance_before IS '变更前冻结账户余额';
COMMENT ON COLUMN rc.spot_account_record.frozen_balance_after IS '变更后冻结账户余额';
COMMENT ON COLUMN rc.spot_account_record.create_time IS '创建时间';
COMMENT ON COLUMN rc.spot_account_record.ext1 IS '预留字段1';
COMMENT ON COLUMN rc.spot_account_record.ext2 IS '预留字段2';
COMMENT ON COLUMN rc.spot_account_record.ext3 IS '预留字段3';
COMMENT ON COLUMN rc.spot_account_record.ext4 IS '预留字段4';
COMMENT ON COLUMN rc.spot_account_record.sync_time IS '同步时间';


-- 需要之前的执行成功后，再单独执行
-- CREATE INDEX ON rc.spot_account_record(user_id, asset_id) WITH (timescaledb.transaction_per_chunk);


psql -U postgres -h localhost -d rc -c "\COPY rc.spot_account_record FROM sar19a.csv CSV"


```

