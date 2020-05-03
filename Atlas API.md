# Some important Atlas API/commands to fetch information and/or troubleshoot

1. Fetch Atlas metrics
```bash
curl -u admin -X GET -H 'Content-Type: application/json' -H 'Cache-Control: no-cache' http://$(hostname -f):21000/api/atlas/admin/metrics
```

Example
```bash
curl -u admin -X GET -H 'Content-Type: application/json' -H 'Cache-Control: no-cache' http://c1232-node3.coelab.cloudera.com:21000/api/atlas/admin/metrics
```

2. Basic search type + tag:
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?classification=<tag>&typeName=<entity-type>"
```

Example:
```bash
curl -u admin -X GET  --header 'Accept: application/json;charset=UTF-8' "http://$(hostname -f):21000/api/atlas/v2/search/basic?classification=NewTag&typeName=hive_column"
```

3.   
