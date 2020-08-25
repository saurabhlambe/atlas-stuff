# Atlas utility API


#### 1. Bulk API to associate a tag/classification to multiple entities
```bash
curl -vv -u admin:hadoop12345! -X POST --header 'Content-Type: application/json;charset=UTF-8' --header 'Accept: application/json' -d @payload.json 'http://localhost:21000/api/atlas/v2/entity/bulk/classification'
```
