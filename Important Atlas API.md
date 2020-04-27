This document is a collection of all the important Atlas API needed to troubleshoot Atlas.

Note: this document is under progress.

#### Fetch Atlas metrics
```
/api/atlas/admin/metrics
```
Example:
```bash
curl -ikv --negotiate -u admin http://`hostname -f`:21000/api/atlas/admin/metrics
```
