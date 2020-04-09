# How to backup Atlas Metadata
Atlas metadata is stored in the form of HBase tables (_atlas_janus_ or _atlas_titan_[1] and _ATLAS_ENTITY_AUDIT_EVENTS_) and Solr collections (_vertex_index_, _fulltext_index_, and _edge_index_).

## Atlas backup:

1. Backup Hbase tables:
- Create a folder in HDFS and set hbase as its owner.
- Run the below commands as 'hbase' user with TGT (if Kerberos) to export HBase tables into HDFS directory from step 1.

```bash
  # hbase org.apache.hadoop.hbase.mapreduce.Export "atlas_janus" "/<folder>/atlas_janus"
  # hbase org.apache.hadoop.hbase.mapreduce.Export "ATLAS_ENTITY_AUDIT_EVENTS" "/<folder>/ATLAS_ENTITY_AUDIT_EVENTS"
```

Note: Above commands will backup the data from HBase table into HDFS.

2. Backup Solr collections from Ambari Infra Solr:
- In addition to HBase tables, Atlas data is stored in 3 Solr collections: _vertex_index_, _fulltext_index_, and _edge_index_. These need to be backed up from Ambari Infra Solr.
- The backup command will backup Solr indexes and configurations for a specified collection. The backup command takes one copy from each shard for the indexes. For configurations, it backs up the configSet that was associated with the collection and metadata.

Use following syntax to run Solr backup API using curl command:
```bash
# http://<infra-solr-host:port>/solr/admin/collections?action=BACKUP&name=myBackupName&collection=<myCollectionName>&location=/path/to/my/shared/drive
```
For Example:
```bash
# curl -ivk "https://host1.example.com:8886/solr/admin/collections?action=BACKUP&name=vertex_index_bkp&collection=vertex_index&location=/opt/vertex_index_backup"
# curl -ivk "https://host1.example.com:8886/solr/admin/collections?action=BACKUP&name=edge_index_bkp&collection=edge_index&location=/opt/edge_index_backup"
# curl -ivk "https://host1.example.com:8886/solr/admin/collections?action=BACKUP&name=fulltext_index_bkp&collection=fulltext_index&location=/opt/fulltext_index_backup"
```

Note:
- If the cluster is kerberized, then run kinit against Solr keytab first and add "--negotiate -u :" after -ivk flag in the above curl commands.
Example: `curl -ivk --negotiate -u : https://host1.example.com:8886`
- If you have multiple Solr instances, ensure you crete the Solr backup directories on all the instances. Else the backup will fail complaining that there should be a shared storage used for backup.

3. Take Atlas configuration backup: Backup all the files under /etc/atlas/conf folder in your Atlas Metadata server. recently compiled the steps to backup and restore HBase and Solr data.

>[1] In Atlas versions earlier than 1.0, the HBase table is _atlas_titan_. In Atlas versions including and later than 1.0, the HBase table is _atlas_janus_. In HDP 2.x, the table would be _atlas_titan_, whereas in HDP 3.x, it would be _atlas_janus_.
