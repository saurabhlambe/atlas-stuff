# Atlas utility API


#### 1. Bulk API to associate a tag/classification to multiple entities
```bash
curl -v -u admin -X POST --header 'Content-Type: application/json;charset=UTF-8' --header 'Accept: application/json' -d @payload.json 'http://localhost:21000/api/atlas/v2/entity/bulk/classification'
```
    Here is the payload [file](../master/payload.json). _unique_ is the classification name, and the _entitiGuids_ represent entities to be tagged.
