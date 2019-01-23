## for BigQL Totorial 


* 매뉴얼 및 시나리오 링크 
  * https://accuinsight.cloudz.co.kr/#/manual/bigql

* Demo Database 생성
  * ``` CREATE SCHEMA demo; ``` 
* Demo table 생성 
  * ``` CREATE EXTERNAL TABLE `demo.play_log`(
`ts` string, 
`id` string, 
`level` int, 
`level_up_yn` string, 
`character_type` string, 
`money_variation` int, 
`stage_name` string, 
`stage_no` int, 
`stage_clear_yn` string, 
`posx` bigint, 
`posy` bigint, 
`money_level` string, 
`join_by` string)
COMMENT ''
ROW FORMAT SERDE 
'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe' 
WITH SERDEPROPERTIES ( 
'field.delim'=',', 
'serialization.format'=',') 
STORED AS INPUTFORMAT 
'org.apache.hadoop.mapred.TextInputFormat' 
OUTPUTFORMAT 
'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION
's3a://gstar/gstar_test/csv'
TBLPROPERTIES (
'transient_lastDdlTime'='1542069144'); ``` 

* 쿼리 수행
  * 미리 보기
  * ``` SELECT * FROM demo.play_log limit 10; ```

  * 유저별 스테이지 클리어까지 소요되는 시간 
  * ``` SELECT b.id, b.stage_no, MAX(from_iso8601_timestamp(ts)) - MIN(from_iso8601_timestamp(ts)) as elapsed_time FROM (SELECT id, stage_no FROM demo.play_log WHERE stage_clear_yn = 'true' GROUP BY id, stage_no ) a, demo.play_log b WHERE a.id = b.id AND a.stage_no = b.stage_no GROUP BY b.id, b.stage_no ORDER BY stage_no DESC, elapsed_time DESC ```
