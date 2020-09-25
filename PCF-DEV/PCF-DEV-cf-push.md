##### https://docs.pivotal.io/pcf-dev/   

##### 01. MAC Install   
    https://docs.pivotal.io/pcf-dev/install-osx.html
    
##### 02. cf login
    cf login -a https://api.dev.cfdev.sh --skip-ssl-validation
    API 엔드포인트: https://api.dev.cfdev.sh   
    Email: 
    
    Select an org:
    1. cfdev-org
    2. system
    
    조직 (enter to skip): 1
    Targeted org cfdev-org
    
    Targeted space cfdev-space    
    
##### 02. cf push
    cd hello-cf
    
    # maven build
    > ./mvnw clean package -DskipTests=true -Dspring.profiles.active=prod -Dspring-boot.run.jvmArguments="-Xms512m -Xmx1024m"
    .....
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  2.863 s
    [INFO] Finished at: 2020-09-25T18:35:03+09:00
    [INFO] ------------------------------------------------------------------------ 
    
    # cf push
    > cf push hello -p target/hello-cf-0.0.1-SNAPSHOT.jar
    
    # cf apps
    > cf apps
    admin(으)로 cfdev-org 조직/cfdev-space 영역의 앱 가져오는 중...
    확인
    
    이름      요청된 상태   인스턴스   메모리   디스크   URL
    hello     started       1/1        768M     1G       hello.dev.cfdev.sh
    
    # 확인
    > curl hello.dev.cfdev.sh/actuator/health
    {"status":"UP"}
    