# Important API/commands to fetch information and/or troubleshoot Atlas

#### 1. Fetch Atlas metrics
```bash
curl -u admin -X GET -H 'Content-Type: application/json' -H 'Cache-Control: no-cache' http://$(hostname -f):21000/api/atlas/admin/metrics
```

> Example
```bash
curl -u admin -X GET -H 'Content-Type: application/json' -H 'Cache-Control: no-cache' http://c1232-node3.coelab.cloudera.com:21000/api/atlas/admin/metrics
```

#### 2. Basic search with entity type
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?&typeName=<entity-type>" | python -m json.tool
```
> Example
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?&typeName=hive_table" | python -m json.tool
```

#### 3. Basic search type + tag
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?classification=<tag>&typeName=<entity-type>"
```

> Example
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?classification=NewTag&typeName=hive_column"
```

#### 4. Fetch Solr collection status
```bash
kinit -kt /etc/security/keytabs/ambari-infra-solr.service.keytab $(klist -kte /etc/security/keytabs/ambari-infra-solr.service.keytab | awk 'NR==4{print $4}')
curl -ivk --negotiate -u : "http://$(hostname -f):8886/solr/admin/collections?action=CLUSTERSTATUS&wt=json&indent=on"
```

#### 5. Atlas-Kafka commands
##### a. Describe ATLAS_HOOK topic
```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper `hostname -f`:2181 --topic ATLAS_HOOK
```
> Expected result
```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper `hostname -f`:2181 --topic ATLAS_HOOK
Topic:ATLAS_HOOK	PartitionCount:1	ReplicationFactor:1	Configs:
	Topic: ATLAS_HOOK	Partition: 0	Leader: 1001	Replicas: 1001	Isr: 1001
```
##### b. Describe ATLAS_ENTITIES topic
```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper `hostname -f`:2181 --topic ATLAS_ENTITIES
```
> Expected result
```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper `hostname -f`:2181 --topic ATLAS_ENTITIES
Topic:ATLAS_ENTITIES	PartitionCount:1	ReplicationFactor:1	Configs:
	Topic: ATLAS_ENTITIES	Partition: 0	Leader: 1001	Replicas: 1001	Isr: 1001
```
##### c. Describe _atlas_ consumer group (to check current consumer lag)
* Without Kerberos
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas
```
* With Kerberos (HDP-2.6.x)
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --security-protocol <kafka-security-protocol>
```
> Example
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --security-protocol SASL_PLAINTEXT
```
* With Kerberos (HDP-3.x)
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --command-config cluster.config

# Contents of cluster.config file
cat cluster.config
security.protocol=SASL_PLAINTEXT
```
> Expected result
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --command-config cluster.config

TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID                                     HOST            CLIENT-ID
ATLAS_HOOK      0          12              12              0               consumer-1-367dc678-f9cd-4e8b-a0c7-0cc154a13b72 /172.25.36.16   consumer-1
[2020-05-03 12:06:32,999] WARN [Principal=null]: TGT renewal thread has been interrupted and will exit. (org.apache.kafka.common.security.kerberos.KerberosLogin)
```
##### d. Dump messages from ATLAS_HOOK Kafka topic to standard output/text file
```bash
/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server `hostname -f`:6667 --topic ATLAS_HOOK --consumer.config config.properties --from-beginning > /tmp/atlas_hook_kafka_dump.txt

# Contents of config.properties file
cat config.properties
security.protocol=SASL_PLAINTEXT
```

#### 6. Check Atlas version
```bash
curl -v -u admin http://localhost:21000/api/atlas/admin/version
```
> Expected result:
```bash
curl -v -u admin http://localhost:21000/api/atlas/admin/version
{"Description":"Metadata Management and Data Governance Platform over Hadoop","Revision":"9b84b9688b91afe3fc58b1a16ecdaa0c190910fa","Version":"1.1.0.3.1.4.0-315","Name":"apache-atlas"}
```

#### 7. Atlas Export API
This API exports a Hive database in json format.
```bash
curl -X POST -u admin:hadoop12345! -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{
    "itemsToExport": [
       { "typeName": "hive_db", "uniqueAttributes": { "qualifiedName": "<db-qualified>" } }
    ]
}' "https://<atlas-server>:21443/api/atlas/admin/export" > export.zip
```
