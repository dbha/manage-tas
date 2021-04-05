
##### upgrade product
    #env.yml
    ubuntu@ubuntu-218:~/tas$ cat env.yml
    ---
    target: xxxxx
    connect-timeout: 30
    request-timeout: 3600
    skip-ssl-validation: true
    username: admin
    password: 'xxxxx
    decryption-passphrase: 'xxxxx'

    
    #download pivnet-cli and get token in network.pivotal.io portal
    wget https://github.com/pivotal-cf/pivnet-cli
    
    #login
    ubuntu@ubuntu-218:~/tas$ pivnet login --api-token $TOKEN
    Warning: The use of static Pivnet API tokens is deprecated and will be removed. Please see https://network.tanzu.vmware.com/docs/api#how-to-authenticate for details on the new UAA API Token mechanism.
    Logged-in successfully
    
    pivnet download-product-files --product-slug='elastic-runtime' --release-version='2.10.0' --product-file-id=739020

    ubuntu@ubuntu-218:~/tas$ om -e env.yml upload-product -p ./cf-2.10.0-build.208.pivotal
    processing product
    beginning product upload to Ops Manager
     8.83 GiB / 10.11 GiB [===================================>-----]  87.35% 00m15s