This document is a collection of all the important Atlas API needed to troubleshoot Atlas.

Note: this document is under progress.

#### 1. Fetch Atlas metrics
```
/api/atlas/admin/metrics
```
Example:
```bash
curl -ikv --negotiate -u admin http://`hostname -f`:21000/api/atlas/admin/metrics
```

#### 2. Solr collection status
```bash
curl -ivk --negotiate -u : "http://$(hostname -f):8886/solr/admin/collections?action=CLUSTERSTATUS&wt=json"
```
