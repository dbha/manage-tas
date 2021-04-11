
##### how to find route information when we are going to deploy AI,

    - route 정보 확인 : gorouter 에서 라우팅 테이블 확인 방법
    https://github.com/cloudfoundry/gorouter#the-routing-table
    
    #bosh CLI를 통해 goroute 로 ssh 접근
    
    #gorouter VM에서 접속정보 확인 및 curl tngod
    curl "http://someuser:somepass@10.0.32.15:8080/routes" -> router/607ff4e1-6944-43de-b2eb-8d46ee739b39:/var/vcap/jobs/gorouter/config# cat gorouter.yml 
    에서 someuser 및 somepass 확인
    
    router/607ff4e1-6944-43de-b2eb-8d46ee739b39:/var/vcap/jobs/gorouter/config# curl "http://router_status:xxxxx@localhost:8080/routes"
    
        {
          "address": "10.193.249.60:8081",
          "tls": true,
          "ttl": 120,
          "tags": null,
          "private_instance_id": "2822ed95-3fdc-4133-706a-08e528a97b01",
          "server_cert_domain_san": "doppler.service.cf.internal"
        },
        {
          "address": "10.193.249.62:8081",
          "tls": true,
          "ttl": 120,
          "tags": null,
          "private_instance_id": "c8fb5e40-0ff0-4d57-7d08-62c63c168991",
          "server_cert_domain_san": "doppler.service.cf.internal"
        }
      ],
      "hello.cfapps.haas-210.pez.pivotal.io": [
        {
          "address": "10.193.249.57:61014",
          "tls": true,
          "ttl": 120,
          "tags": {
            "app_id": "c1fd2e73-8589-4926-ab16-c9fe57ec4465",
            "app_name": "hello",
            "component": "route-emitter",
            "instance_id": "0",
            "organization_id": "a69aed44-fc03-4b43-8909-e081a20002b5",
            "organization_name": "dbha-org",
            "process_id": "c1fd2e73-8589-4926-ab16-c9fe57ec4465",
            "process_instance_id": "3006b6bc-a264-4eac-5c74-3912",
            "process_type": "web",
            "source_id": "c1fd2e73-8589-4926-ab16-c9fe57ec4465",
            "space_id": "356dc0ea-df5e-466a-82cb-ec303754750a",
            "space_name": "my-space"
          },
          "private_instance_id": "3006b6bc-a264-4eac-5c74-3912",
          "server_cert_domain_san": "3006b6bc-a264-4eac-5c74-3912"
        }
      ],
    
    
