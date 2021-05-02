

##### Creating and Managing Users with the UAA CLI (UAAC)
##### https://docs.pivotal.io/application-service/2-11/uaa/uaa-user-management.html
##### https://docs.pivotal.io/p-identity/1-11/manage-clients-api.html


##### Create an Admin User & group

    #uaac target check
    ubuntu@opsmanager:~$ uaac targets
    0 * https://opsman.haas-218.pez.pivotal.io/uaa
    
    ubuntu@opsmanager:~$ SYSTEM_DOMAIN=sys.tas.haas-218.pez.pivotal.io
    ubuntu@opsmanager:~$ ADMIN_CLIENT_SECRET=xxxxxxx (via opsman UI > TAS > Credential > Uaa > Admin Client Credentials)
    ubuntu@opsmanager:~$ uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
    Unknown key: Max-Age = 86400
    
    Target: https://uaa.sys.tas.haas-218.pez.pivotal.io
    
    ubuntu@opsmanager:~$ uaac targets
    0   https://opsman.haas-218.pez.pivotal.io/uaa
    1 * https://uaa.sys.tas.haas-218.pez.pivotal.io
    
    ubuntu@opsmanager:~$ uaac token client get admin -s ${ADMIN_CLIENT_SECRET}
    Unknown key: Max-Age = 86400
    
    Successfully fetched token via client credentials grant.
    Target: https://uaa.sys.tas.haas-218.pez.pivotal.io
    Context: admin, from client admin
    
    # add user 
    ubuntu@opsmanager:~$ uaac user add testuser -p xxxx --emails testuser@example.com
    
    ubuntu@opsmanager:~$ uaac user get
    User name:  ^C
    Interrupt:
    
    ubuntu@opsmanager:~$ uaac user get testuser
      id: d8358dd5-ae9f-4e14-8477-551fbdb7c263
      meta
        version: 0
        created: 2021-05-02T05:13:49.000Z
        lastmodified: 2021-05-02T05:13:49.000Z
      name
      emails:
      -
        value: testuser@example.com
        primary: false
      groups:
      -
        value: b4be53ac-dee2-4527-83cc-db23c78d8792
        display: profile
        type: DIRECT
      -
        value: 7fe46710-68a7-41e7-98b9-2ebc162724e3
        display: scim.me
        type: DIRECT
      ....
        -
          value: a6c4d562-509d-45be-a189-c2a286beb767
          display: cloud_controller_service_permissions.read
          type: DIRECT
        -
          value: 1de406f7-b462-478f-94e8-02dfefe1f02c
          display: oauth.approvals
          type: DIRECT
        -
          value: d1623058-990c-4f76-a724-0f61ea5834fc
          display: password.write
          type: DIRECT
        -
          value: 458a3069-b1c2-4c45-ad26-39a64a153e90
          display: uaa.offline_token
          type: DIRECT
        -
          value: 8e6e7462-fc04-4e98-acc6-8e0ab280ea6a
          display: uaa.user
          type: DIRECT
        approvals:
        active: true
        verified: true
        origin: uaa
        schemas: urn:scim:schemas:core:1.0
        username: dbha
        zoneid: uaa
        passwordlastmodified: 2021-05-02T05:13:49.000Z
 
    ubuntu@opsmanager:~$ uaac member add cloud_controller.admin testuser
    success
    ubuntu@opsmanager:~$
    ubuntu@opsmanager:~$
    
##### cf 

    ubuntu@ubuntu-218:~/tas$ cf login -a api.sys.tas.haas-218.pez.pivotal.io --skip-ssl-validation
    API endpoint: api.sys.tas.haas-218.pez.pivotal.io
    
    Email: testuser
    
    Password:
    Authenticating...
    OK
    
    Select an org:
    1. develop
    2. devops1
    3. iso-org
    4. MyPJ
    5. system

    Org (enter to skip): 2
    Targeted org devops1

    Targeted space web-app

    API endpoint:   https://api.sys.tas.haas-218.pez.pivotal.io (API version: 2.150.0)
    User:           dbha
    Org:            devops1
    Space:          web-app 
    

##### Add uaac client

    ubuntu@opsmanager:~$ REDIRECT_URLS=http://localhost:8080/login/oauth2/code/uaa
    ubuntu@opsmanager:~$ uaac client add log_cache_ui \
       --secret CHANGEME \
       --authorized_grant_types refresh_token,authorization_code \
       --scope openid,doppler.firehose,logs.admin \
       --access_token_validity 43200 \
       --refresh_token_validity 259200 \
       --redirect_uri ${REDIRECT_URLS}   
      scope: logs.admin openid doppler.firehose
      client_id: log_cache_ui
      resource_ids: none
      authorized_grant_types: refresh_token authorization_code
      redirect_uri: http://localhost:8080/login/oauth2/code/uaa
      autoapprove:
      access_token_validity: 43200
      refresh_token_validity: 259200
      authorities: uaa.none
      name: log_cache_ui
      required_user_groups:
      lastmodified: 1619931745000
      id: log_cache_ui
    
    ubuntu@opsmanager:~$ uaac clients
    ...
      log_cache_ui
        scope: logs.admin openid doppler.firehose
        resource_ids: none
        authorized_grant_types: refresh_token authorization_code
        redirect_uri: http://localhost:8080/login/oauth2/code/uaa
        autoapprove:
        access_token_validity: 43200
        refresh_token_validity: 259200
        authorities: uaa.none
        name: log_cache_ui
        lastmodified: 1619931745000
      loggregator
        scope: uaa.none
        resource_ids: none
        authorized_grant_types: client_credentials
        autoapprove: true
        authorities: cloud_controller.admin_read_only
        lastmodified: 1617634585000
    ....