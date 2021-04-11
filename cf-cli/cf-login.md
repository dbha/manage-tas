

##### cf login in MAC OS
     sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ~/Downloads/root_ca_certificate\ \(6\)
    
    Password:
    dbha ~/IdeaProjects/vault-service-broker/vault-broker   master 

     cf login -a https://api.sys.tas.haas-218.pez.pivotal.io
    API 엔드포인트: https://api.sys.tas.haas-218.pez.pivotal.io
    
    Email: admin
    
    비밀번호:
    인증 중...
    확인
    
    Select an org:
    1. develop
    2. system
    
    조직 (enter to skip): 1
    Targeted org develop
    
    Targeted space devops1
       
    API 엔드포인트:   https://api.sys.tas.haas-218.pez.pivotal.io (API version: 3.85.0)
    사용자:           admin
    조직:             develop
    영역:             devops1
    
##### CA 인증서 등록

    https://docs.cloudfoundry.org/cf-cli/self-signed.html
    
    #Install the Certificate on Mac OS X
    sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain server.crt
    
    #Install the Certificate on Linux
    - For Debian, Ubuntu, or Gentoo, run:
    cat server.crt >> /etc/ssl/certs/ca-certificates.crt
    
    - For Fedora or RHEL, run:
    cat server.crt >> /etc/pki/tls/certs/ca-bundle.crt    