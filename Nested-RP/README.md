
##### Nested Resource Pool creation in Vsphere

##### 01. RP creation in vSphere.
![ex_screenshot](./img/nested-rp.png)

    - PKS-RP
      - PKS-RP-AZ01
      - PKS-PR-AZ02
      - PKS-PR-AZ03
            
##### 02. Create AZ on Opsmanager
![ex_screenshot](./img/bosh-create-az.png)

    # create AZ
    opsman UI > bosh > Create Availability Zones > add
    
    Name: PKS-AZ-01
    IaaS Configuration: default
    Resource Pool: PKS-AZ-01
    
    Name: PKS-AZ-02
    IaaS Configuration: default
    Resource Pool: PKS-AZ-01
        
    Name: PKS-AZ-03
    IaaS Configuration: default
    Resource Pool: PKS-AZ-01
    
    # if save, Error
    "AvailabilityZonesVerifier" error is happened
    
##### 03. Create Networks

    # opsman UI > bosh > Create Networks 
    add Availability Zones PKS-AZ-01, PKS-AZ-02, PKS-AZ-03
    
##### 04. To avoid AvailabilityZonesVerifier During apply changes, we can change AvailabilityZonesVerifier value to false.(but still error during save)
##### https://docs.pivotal.io/pivotalcf/2-6/opsman-api/#retrieving-a-list-of-install-time-verifiers-for-the-director

    # opsman uaa connect / get token
    opsman uaac 
    
    ubuntu@ops-manager:~$ uaac token owner get
    Client ID:  opsman
    Client secret:
    User name:  admin
    Password:  *********
    Unknown key: Max-Age = 172800
    
    Successfully fetched token via owner password grant.
    Target: https://opsman.haas-210.pez.pivotal.io/uaa
    Context: admin, from client opsman
    
    ubuntu@ops-manager:~$ uaac targets
    0 * https://opsman.haas-210.pez.pivotal.io/uaa
    1   https://uaa.sys.haas-210.pez.pivotal.io
    2   https://api.tkgi.haas-210.pez.pivotal.io:8443

    ubuntu@ops-manager:~$ uaac contexts
    
    [0]*[https://opsman.haas-210.pez.pivotal.io/uaa]
      skip_ssl_validation: true
    
      [0]*[admin]
          user_id: bb840774-f516-46c8-9282-460cc186582c
          client_id: opsman
          access_token: xxxxMsVLEEhKJZBcat0nXfTy0ZvrwLJoIdMonXdQ2HUJvS64kpvuYsxkC7GRvY6Slcw
          token_type: bearer
          refresh_token: xxxxHGIgG387J4zdTcSR080D_lw
          expires_in: 43199
          scope: opsman.admin scim.me uaa.admin clients.admin
          jti: cb621abbf5934bcbb80f5a14575a5f15
    
    [1] [https://uaa.sys.haas-210.pez.pivotal.io]
      skip_ssl_validation: true
    
      [0] [admin]
          client_id: admin
          access_token: xxxx
          token_type: bearer
          expires_in: 43199
          scope: clients.read password.write clients.secret clients.write uaa.admin scim.write scim.read
          jti: d2430fd6918849238505d925f870779d
    
    [2] [https://api.tkgi.haas-210.pez.pivotal.io:8443]
      skip_ssl_validation: true
    
      [0] [admin]
          client_id: admin
          access_token: eyJhbGciOiJSUxxxx
          token_type: bearer
          expires_in: 43199
          scope: clients.read pks.clusters.admin.read clients.secret pks.clusters.manage clients.write uaa.admin clients.admin scim.write pks.clusters.admin scim.read
          jti: 2c92fab8ee234ae8a766b790022f3046
          
    ubuntu@ops-manager:~$ export TOKEN=xxxxMsVLEEhKJZBcat0nXfTy0ZvrwLJoIdMonXdQ2HUJvS64kpvuYsxkC7GRvY6Slcw
    
##### 05. Change AvailabilityZonesVerifier

    # ubuntu@ops-manager:~$ curl "https://localhost/api/v0/staged/director/verifiers/install_time" -X GET -H "Authorization: Bearer $TOKEN" -k  | jq
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   504    0   504    0     0   1203      0 --:--:-- --:--:-- --:--:--  1205
    {
      "verifiers": [
        {
          "type": "AllocatedIpAddressVerifier",
          "enabled": true
        },
        {
          "type": "AvailabilityZonesVerifier",
          "enabled": true
        },
        {
          "type": "BlobstoreVerifier",
          "enabled": true
        },
        {
          "type": "IaasConfigurationVerifier",
          "enabled": true
        },
        {
          "type": "MysqlDatabaseVerifier",
          "enabled": true
        },
        {
          "type": "NetworksExistenceVerifier",
          "enabled": true
        },
        {
          "type": "NetworksPingableVerifier",
          "enabled": true
        },
        {
          "type": "NtpVerifier",
          "enabled": true
        },
        {
          "type": "PrivilegeVerifier",
          "enabled": true
        },
        {
          "type": "VsphereConfigurationVerifier",
          "enabled": true
        }
      ]
    }
    
    # update 
    ubuntu@ops-manager:~$ curl "https://localhost/api/v0/staged/director/verifiers/install_time/AvailabilityZonesVerifier" -X PUT -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" -d '{ "enabled": false }' -k
    {"type":"AvailabilityZonesVerifier","enabled":false}
    
    # check
    ubuntu@ops-manager:~$ curl "https://localhost/api/v0/staged/director/verifiers/install_time" -X GET -H "Authorization: Bearer $TOKEN" -k  | jq
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   505    0   505    0     0    228      0 --:--:--  0:00:02 --:--:--   228
    {
      "verifiers": [
        {
          "type": "AllocatedIpAddressVerifier",
          "enabled": true
        },
        {
          "type": "AvailabilityZonesVerifier",
          "enabled": false
        },
        {
          "type": "BlobstoreVerifier",
          "enabled": true
        },
        
##### 06. change Advanced Mode in Opsmanager
##### 
    ubuntu@ops-manager:~$ uaac curl https://opsman.haas-210.pez.pivotal.io/api/v0/staged/infrastructure/locked -X PUT --data '{"locked" : "false"}' -H "Content-Type: application/json" -k
    PUT https://opsman.haas-210.pez.pivotal.io/api/v0/staged/infrastructure/locked
    REQUEST BODY: "{"locked" : "false"}"
    REQUEST HEADERS:
      Content-Type: application/json
    
    200 OK
    RESPONSE HEADERS:
      Date: Sun, 20 Dec 2020 07:48:00 GMT
      Content-Type: application/json; charset=utf-8
      Transfer-Encoding: chunked
      Connection: close
      X-Frame-Options: SAMEORIGIN
      X-Xss-Protection: 1; mode=block
      X-Content-Type-Options: nosniff
      X-Download-Options: noopen
      X-Permitted-Cross-Domain-Policies: none
      Referrer-Policy: strict-origin-when-cross-origin
      Cache-Control: no-cache, no-store
      Pragma: no-cache
      Expires: Fri, 01 Jan 1990 00:00:00 GMT
      Etag: W/"99fb24b49424af106fc1177a590d5011"
      X-Request-Id: 1bebf819-06e8-403d-a7cd-eea45f6d3460
      X-Runtime: 0.997386
      Strict-Transport-Security: max-age=31536000; includeSubDomains
      Server: Ops Manager
    RESPONSE BODY:
    {
      "locked": false
    }
    
##### 07. change configuration in TKGI
    # add Assign AZs and Networks
    opsman UI > TKGI > Assign AZs and Networks > Balance other jobs in
    - check PKS-AZ-01, PKS-AZ-02, PKS-AZ-03
    
    # change plan2(sample)
    opsman UI > TKGI > plan2 > Worker Availability Zones 
    - check PKS-AZ-01, PKS-AZ-02, PKS-AZ-03
    

##### 08. apply changes


##### 09. Create TKGI cluster
    ubuntu@ubuntu-210:~$ tkgi create-cluster tkgi-cluster -e tkgi-cluster.haas-210.pez.pivotal.io -p PKS-RP-small
    
    PKS Version:              1.9.1-build.4
    Name:                     tkgi-cluster
    K8s Version:              1.18.8
    Plan Name:                PKS-RP-small
    UUID:                     c69b4c2f-699e-4f13-9e11-5b83a9cf9841
    Last Action:              CREATE
    Last Action State:        in progress
    Last Action Description:  Creating cluster
    Kubernetes Master Host:   tkgi-cluster.haas-210.pez.pivotal.io
    Kubernetes Master Port:   8443
    Worker Nodes:             3
    Kubernetes Master IP(s):  In Progress
    Network Profile Name:
    Kubernetes Profile Name:
    Compute Profile Name:
    Tags:
    
    Use 'pks cluster tkgi-cluster' to monitor the state of your cluster
    
![ex_screenshot](./img/after-rp-tkgi-cluster.png)