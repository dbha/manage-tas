
##### Ckeck Certificate Expire

#####https://docs.pivotal.io/ops-manager/2-10/security/pcf-infrastructure/check-expiration.html
#####https://docs.pivotal.io/ops-manager/2-4/security/pcf-infrastructure/check-expiration.html

##### opsman root_ca 등록
    ubuntu@opsmanager:/etc/ssl/certs$ sudo vi opsman_root.ca
    ubuntu@opsmanager:/etc/ssl/certs$
    ubuntu@opsmanager:/etc/ssl/certs$
    
##### uaa access    
ubuntu@opsmanager:/etc/ssl/certs$ uaac target https://opsman.haas-218.pez.pivotal.io/uaa

    Target: https://opsman.haas-218.pez.pivotal.io/uaa
    Context: admin, from client opsman
    
    ubuntu@opsmanager:/etc/ssl/certs$ uaac token owner get
    Client ID:  opsman
    Client secret:
    User name:  admin
    Password:  ********
    Unknown key: Max-Age = 172800
    
    Successfully fetched token via owner password grant.
    Target: https://opsman.haas-218.pez.pivotal.io/uaa
    Context: admin, from client opsman
    
    ubuntu@opsmanager:/etc/ssl/certs$ uaac target
    
    Target: https://opsman.haas-218.pez.pivotal.io/uaa
    Context: admin, from client opsman
    
    ubuntu@opsmanager:/etc/ssl/certs$ uaac targets
    0 * https://opsman.haas-218.pez.pivotal.io/uaa
    
    ubuntu@opsmanager:~$ uaac contexts
    
    [0]*[https://opsman.haas-218.pez.pivotal.io/uaa]
      skip_ssl_validation: true
    
      [0]*[admin]
          user_id: b7c5db8d-9fa2-4f01-be2c-029fd2385e2f
          client_id: opsman
          access_token: xxxxx
          token_type: bearer
          refresh_token: xxxxx
          expires_in: 43199
          scope: opsman.admin scim.me uaa.admin clients.admin
          jti: 30f2ed83c09548f9a2ab66b4b79b2d7f

##### access_token 사용하여 curl 수행
    ubuntu@opsmanager:~$ curl "https://opsman.haas-218.pez.pivotal.io/api/v0/deployed/certificates"       \
        -X GET -H "Authorization: Bearer xxxxxx"   \   
        -i -k
    HTTP/1.1 200 OK
    Date: Sun, 18 Apr 2021 03:18:12 GMT
    Content-Type: application/json; charset=utf-8
    Transfer-Encoding: chunked
    Connection: keep-alive
    X-Frame-Options: SAMEORIGIN
    X-XSS-Protection: 1; mode=block
    X-Content-Type-Options: nosniff
    X-Download-Options: noopen
    X-Permitted-Cross-Domain-Policies: none
    Referrer-Policy: strict-origin-when-cross-origin
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Expires: Fri, 01 Jan 1990 00:00:00 GMT
    ETag: W/"dc4d4a5fa24db3063112c6946fd92f18"
    X-Request-Id: 19dba054-b15c-4e62-9a17-3b6574257b38
    X-Runtime: 0.805104
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Server: Ops Manager
    
    {"certificates":[{"configurable":false,"is_ca":false,"property_reference":".properties.director_ssl","property_type":"rsa_cert_credentials","product_guid":"p-bosh-f154d42fdc7d8ec523ad","location":"ops_manager","variable_path":null,"issuer":"/C=US/O=Pivotal","valid_from":"2021-04-04T11:55:31Z","valid_until":"2023-04-04T11:55:31Z"},{"configurable":false,"is_ca":false,"property_reference":".properties.uaa_ssl","property_type":"rsa_cert_credentials","product_guid":"p-bosh-f154d42fdc7d8ec523ad","location":"ops_manager","variable_path":null,"issuer":"/C=US/O=Pivotal","valid_from":"2021-04-04T11:55:32Z","valid_until":"2023-04-04T11:55:32Z"},{"configurable":false,"is_ca":false,"property_reference":".properties.blobstore_certificate","property_type":"rsa_cert_credentials","product_guid":"p-bosh-f154d42fdc7d8ec523ad","location":"ops_manager","variable_path":null,"issuer":"/C=US/O=Pivotal","valid_from":"2021-04-04T11:55:33Z","valid_until":"2023-04-04T11:55:33Z"},{"configurable":false,"is_ca":false,"property_reference":".properties.director_agent_ssl","property_type":"rsa_cert_credentials","product_guid":"p-bosh-f154d42fdc7d8ec523ad","location":"ops_manager","variable_path":null,"issuer":"/C=US/O=Pivotal","valid_from":"2021-04-04T11:55:32Z","valid_until":"2023-04-04T11:55:32Z"},{"configurable":false,"is_ca":false,"property_reference":".p
    
    ubuntu@opsmanager:~$ curl "https://opsman.haas-218.pez.pivotal.io/api/v0/deployed/certificates"       -X GET       -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsImprdSI6Imh0dHBzOi8vb3BzbWFuLmhhYXMtMjE4LnBlei5waXZvdGFsLmlvOjQ0My91YWEvdG9rZW5fa2V5cyIsImtpZCI6ImtleS0xIiwidHlwIjoiSldUIn0.eyJqdGkiOiIzMGYyZWQ4M2MwOTU0OGY5YTJhYjY2YjRiNzliMmQ3ZiIsInN1YiI6ImI3YzVkYjhkLTlmYTItNGYwMS1iZTJjLTAyOWZkMjM4NWUyZiIsInNjb3BlIjpbIm9wc21hbi5hZG1pbiIsInNjaW0ubWUiLCJ1YWEuYWRtaW4iLCJjbGllbnRzLmFkbWluIl0sImNsaWVudF9pZCI6Im9wc21hbiIsImNpZCI6Im9wc21hbiIsImF6cCI6Im9wc21hbiIsImdyYW50X3R5cGUiOiJwYXNzd29yZCIsInVzZXJfaWQiOiJiN2M1ZGI4ZC05ZmEyLTRmMDEtYmUyYy0wMjlmZDIzODVlMmYiLCJvcmlnaW4iOiJ1YWEiLCJ1c2VyX25hbWUiOiJhZG1pbiIsImVtYWlsIjoiYWRtaW5AdGVzdC5vcmciLCJhdXRoX3RpbWUiOjE2MTg3MTU3MDEsInJldl9zaWciOiJiNjJlMjEyZiIsImlhdCI6MTYxODcxNTcwMSwiZXhwIjoxNjE4NzU4OTAxLCJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgwODAvdWFhL29hdXRoL3Rva2VuIiwiemlkIjoidWFhIiwiYXVkIjpbInNjaW0iLCJvcHNtYW4iLCJjbGllbnRzIiwidWFhIl19.Fh4tqAaS8ak9LpR4oBtLmrjDJbkT7n1r-lfvJCwQjllac-eF6lz6z3ZVtx6PiA0qBbODKHGCGiz026U-b1c5QLsDvVK1N_K2hT44eMiegE5Uf2ya0sim3V5he6LiyYxw59BH0gj7VLmcuE3_5h4zrAz5z7Ij-MfqUYci0YpaO6gky4YLa8sXi9ffNUIuH5Pp2Irh3vTErZrpOvdsi4Q1TMD8Wd3hnNQ7xq_3bVhmuIX6ceBN1PA4JhSrk-VZxwzz_1nidq8i5XTDwl86KWsoYKpgMgKX7lSXXFR7Wz1i70-UwNp4i4NZL4ZMKQF0q1rpno_0BlqngQuV613WsF8YbA"       -i -k > /tmp/opsman_cert
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100 50163    0 50163    0     0  49669      0 --:--:--  0:00:01 --:--:-- 49764

    ubuntu@opsmanager:~$ vi /tmp/opsman_cert
    => opsman_cert 파일 처음 API 결과에 대한 출력 부분 삭제
    
    ubuntu@opsmanager:~$ cat /tmp/opsman_cert | jq
    {
      "certificates": [
        {
          "configurable": false,
          "is_ca": false,
          "property_reference": ".properties.director_ssl",
          "property_type": "rsa_cert_credentials",
          "product_guid": "p-bosh-f154d42fdc7d8ec523ad",
          "location": "ops_manager",
          "variable_path": null,
          "issuer": "/C=US/O=Pivotal",
          "valid_from": "2021-04-04T11:55:31Z",
          "valid_until": "2023-04-04T11:55:31Z"
        },
    ....
        {
          "is_ca": true,
          "property_reference": null,
          "property_type": null,
          "product_guid": "p-isolation-segment-24e9c806577ca0bd8202",
          "configurable": false,
          "issuer": null,
          "valid_from": null,
          "valid_until": "2025-04-04T14:27:43Z",
          "location": "credhub",
          "variable_path": "/opsmgr/bosh_dns/tls_ca"
        },
        {
          "is_ca": true,
          "property_reference": null,
          "property_type": null,
          "product_guid": "cf-2220358aace41f8a2b1d",
          "configurable": false,
          "issuer": null,
          "valid_from": null,
          "valid_until": "2022-04-05T14:27:31Z",
          "location": "credhub",
          "variable_path": "/services/tls_leaf"
        },
        {
          "is_ca": true,
          "property_reference": null,
          "property_type": null,
          "product_guid": "p-isolation-segment-24e9c806577ca0bd8202",
          "configurable": false,
          "issuer": null,
          "valid_from": null,
          "valid_until": "2022-04-05T14:27:31Z",
          "location": "credhub",
          "variable_path": "/services/tls_leaf"
        },
        {
          "is_ca": true,
          "property_reference": null,
          "property_type": null,
          "product_guid": "cf-2220358aace41f8a2b1d",
          "configurable": false,
          "issuer": null,
          "valid_from": null,
          "valid_until": "2023-04-05T14:27:30Z",
          "location": "credhub",
          "variable_path": "/p-bosh/cf-2220358aace41f8a2b1d/diego-instance-identity-intermediate-ca-2-7"
        },
        {
          "is_ca": true,
          "property_reference": null,
          "property_type": null,
          "product_guid": "cf-2220358aace41f8a2b1d",
          "configurable": false,
          "issuer": null,
          "valid_from": null,
          "valid_until": "2024-04-04T14:27:29Z",
          "location": "credhub",
          "variable_path": "/cf/diego-instance-identity-root-ca-2-6"
        },
        {
          "is_ca": true,
          "property_reference": null,
          "property_type": null,
          "product_guid": "",
          "configurable": false,
          "issuer": null,
          "valid_from": null,
          "valid_until": "2026-04-04T12:19:44Z",
          "location": "credhub",
          "variable_path": "/services/tls_ca"
        }
      ]
    }


##### More options:
    To limit command output to certificates that expire within a given time interval, append ?expires_within=TIME to the endpoint, replacing TIME with an integer-letter code.
    Valid letter codes are d for days, w for weeks, m for months, and y for years. For example, querying to https://OPS-MANAGER-FQDN/api/v0/deployed/certificates?expires_within=6m searches for certificates expiring within six months.
      
    curl "https://opsman.haas-218.pez.pivotal.io/api/v0/deployed/certificates?expires_within=6m" \
        -X GET -H "Authorization: Bearer xxxxx"  -i -k

    # 6개월 이내
    
    ubuntu@opsmanager:~$ curl "https://opsman.haas-218.pez.pivotal.io/api/v0/deployed/certificates?expires_within=6m" -X GET  \
        -H "Authorization: Bearer xxxxx"  -i -k
    HTTP/1.1 200 OK
    Date: Sun, 18 Apr 2021 03:30:31 GMT
    Content-Type: application/json; charset=utf-8
    Transfer-Encoding: chunked
    Connection: keep-alive
    X-Frame-Options: SAMEORIGIN
    X-XSS-Protection: 1; mode=block
    X-Content-Type-Options: nosniff
    X-Download-Options: noopen
    X-Permitted-Cross-Domain-Policies: none
    Referrer-Policy: strict-origin-when-cross-origin
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Expires: Fri, 01 Jan 1990 00:00:00 GMT
    ETag: W/"001ca68166a6cc4e49fffa9c7425585e"
    X-Request-Id: fa1e282f-7b8f-4672-ac69-ee9f57c66336
    X-Runtime: 1.206826
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Server: Ops Manager
    
    {"certificates":[]}ubuntu@opsmanager:~$

    #3년 이내
    ubuntu@opsmanager:~$ curl "https://opsman.haas-218.pez.pivotal.io/api/v0/deployed/certificates?expires_within=3y" -X GET -H "Authorization: Bearer xxxxx"  -i -k
    
    ubuntu@opsmanager:~$ cat /tmp/opsman_cert_3y | jq
    {
      "certificates": [
        {
          "configurable": false,
          "is_ca": false,
          "property_reference": ".properties.director_ssl",
          "property_type": "rsa_cert_credentials",
          "product_guid": "p-bosh-f154d42fdc7d8ec523ad",
          "location": "ops_manager",
          "variable_path": null,
          "issuer": "/C=US/O=Pivotal",
          "valid_from": "2021-04-04T11:55:31Z",
          "valid_until": "2023-04-04T11:55:31Z"
        },
        {
          "configurable": false,
          "is_ca": false,
          "property_reference": ".properties.uaa_ssl",
          "property_type": "rsa_cert_credentials",
          "product_guid": "p-bosh-f154d42fdc7d8ec523ad",
          "location": "ops_manager",
          "variable_path": null,
          "issuer": "/C=US/O=Pivotal",
          "valid_from": "2021-04-04T11:55:32Z",
          "valid_until": "2023-04-04T11:55:32Z"
        },
        {
          "configurable": false,
          "is_ca": false,
          "property_reference": ".properties.blobstore_certificate",
          "property_type": "rsa_cert_credentials",
          "product_guid": "p-bosh-f154d42fdc7d8ec523ad",
          "location": "ops_manager",
          "variable_path": null,
          "issuer": "/C=US/O=Pivotal",
          "valid_from": "2021-04-04T11:55:33Z",
          "valid_until": "2023-04-04T11:55:33Z"
        },
    
    
    
