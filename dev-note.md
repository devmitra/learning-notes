# Dev Note

## Node

### Example: Express middleware with response status

```js
app.use(function(req, res, next) {
  if (res.headersSent) {
    console.log(chalk.green(req.method, req.url, res.statusCode));
  } else {
    res.on('finish', function() {
      console.log(chalk.green(req.method, req.url, res.statusCode));
    })
  }
  next();
});
```

## JS Examples

### Example: Object Destruction (28.7.2021)

- Nested object destruction with default value

```js
const { hcapUserInfo: user, body: { userId, role, regions, sites } = {} } = req;
```

## Postgres SQL: Query for json array element (12.11.2020)

### Example 4 (17.6.2021)

```sql
SELECT * FROM participants WHERE id NOT IN (SELECT id FROM participants p, jsonb_array_elements(p.body->'history') h
WHERE h->>'reason' = 'Reconfirm Interest');
```

### Example 3 (17.6.2021)

```sql
SELECT id as ID, body->'emailAddress' as email FROM participants p, jsonb_array_elements(p.body->'history') h
WHERE h->>'reason' = 'Reconfirm Interest';

SELECT COUNT(*) FROM participants p, jsonb_array_elements(p.body->'history') h
WHERE h->>'reason' = 'Reconfirm Interest';
```

### Example 2 (17.6.2021)

```sql
with to_update as (
      select id, '{telecom,' || ti - 1 || ',value}' as path, LOWER(telecom #>> '{value}') as value
      from patient
             left join lateral jsonb_array_elements(resource -> 'telecom') with ordinality a_arr(telecom, ti) on true
      where telecom #>> '{system}' = 'email'
        and telecom #>> '{value}' <> LOWER(telecom #>> '{value}')
    )
    update patient
    set resource = jsonb_set(resource, path::text[], to_jsonb(value))
    from to_update
    where patient.id = to_update.id
```

### Example1 (12.11.2020)

```sql
EXISTS (
          SELECT
            ext ->> 'valueBoolean'
          FROM
            patient p,
            jsonb_array_elements(p.resource -> 'extension') ext
          WHERE
            ext ->> 'url' = '${EMAIL_VERIFIED_EXT_URL}'
            AND p.id = patient.id
        )
```

## Openshift setup for GH (3.10.2020)

```sh
cd $HOME
curl -LO https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz
tar -xf openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz
cd openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit
chmod +x oc
echo "::set-env name=PATH_TO_OC::$PATH:$(pwd)"
export PATH=$PATH_TO_OC
```

## Patient Search with email id template (Date: 8.9.2020)

```sql
SELECT * FROM patient WHERE resource @> '{"telecom": [{"value": "raj+testing@freshworks.io"}]}'
```

```txt
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJsNGl6YnktdG9vbHMiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlY3JldC5uYW1lIjoiZ2l0aHViLWFjdGlvbi10b2tlbi03bGdxayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJnaXRodWItYWN0aW9uIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiNWM0Y2ZlYTItMDcxNS0xMWViLTljODMtMDA1MDU2ODM0OGNjIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omw0aXpieS10b29sczpnaXRodWItYWN0aW9uIn0.3szbu6Gop3pCmieFHmqoWhCNKXN27_qMEIs-dAnxx613gOWT0JX-p4YfV3D77FsvzBXdnMeaWM5hr4aWbWMNM2f4IhW-qe_N80Vy-LOKObcclNjyuBEY5Z3Z3guTtNGjMljWtCa3DwuBhIF95exNYOv-vAsvyhFDVFXtBzfxkO_FoB25bitLoe3Zj_MJEDRBkSaAJ50QBC4h-9eH27Cam_XiWB-QDqxN_23ncWTk7SDAbDY0djF4XOKQkMzRzbjKLWVbjMLTFHMdtEto1QvjpGUwCzrhanJMQnReCuTBm5uwQhUluzp0Wv4iarWvrEOYmU7ZIXCUtsHaMYr2rvR1ZQ
```
