# Important API/commands to fetch information and/or troubleshoot

#### 1. Fetch Atlas metrics
```bash
curl -u admin -X GET -H 'Content-Type: application/json' -H 'Cache-Control: no-cache' http://$(hostname -f):21000/api/atlas/admin/metrics
```

> Example
```bash
curl -u admin -X GET -H 'Content-Type: application/json' -H 'Cache-Control: no-cache' http://c1232-node3.coelab.cloudera.com:21000/api/atlas/admin/metrics
```

#### 2. Basic search with entity type:
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?&typeName=<entity-type>" | python -m json.tool
```
> Example:
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?&typeName=hive_table" | python -m json.tool
```

#### 3. Basic search type + tag:
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?classification=<tag>&typeName=<entity-type>"
```

> Example:
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?classification=NewTag&typeName=hive_column"
```

#### 4. Fetch Solr collection status:
```bash
kinit -kt /etc/security/keytabs/ambari-infra-solr.service.keytab $(klist -kte /etc/security/keytabs/ambari-infra-solr.service.keytab | awk 'NR==4{print $4}')
curl -ivk --negotiate -u : "http://$(hostname -f):8886/solr/admin/collections?action=CLUSTERSTATUS&wt=json&indent=on"
```

#### 5. Atlas-Kafka commands
1. Describe ATLAS_HOOK topic:
```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper `hostname -f`:2181 --topic ATLAS_HOOK
```
2. Describe ATLAS_ENTITIES topic:
```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper `hostname -f`:2181 --topic ATLAS_ENTITIES
```
3. Describe _atlas_ consumer group (to check current consumer lag):
* Without Kerberos:
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas
```
* With Kerberos (HDP-2.6.x):
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --security-protocol <kafka-security-protocol>
```
> Example:
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --security-protocol SASL_PLAINTEXT
```
* With Kerberos (HDP-3.x):
```bash
/usr/hdp/current/kafka-broker/bin/kafka-consumer-groups.sh  --bootstrap-server `hostname -f`:6667 --describe --group atlas --command-config cluster.config
cat cluster.config
security.inter.broker.protocol=SASL_PLAINTEXT
```
