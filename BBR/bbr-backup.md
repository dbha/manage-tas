##### bbr backup 
##### https://docs.pivotal.io/ops-manager/2-10/install/backup-restore/backup-pcf-bbr.html

    1) bos-env 확인
    
    ubuntu@opsmanager:~/bbr$ cat bosh-env.sh
    export BOSH_CLIENT_SECRET=xxxxxxxx
    export BOSH_ENVIRONMENT=bosh.haas-218.pez.pivotal.io
    export BOSH_CA_CERT='-----BEGIN CERTIFICATE-----
    
    -----END CERTIFICATE-----
    '
    export CREDHUB_CLIENT=ops_manager
    export CREDHUB_SECRET=xxxxxxx
    export CREDHUB_SERVER=https://bosh.haas-218.pez.pivotal.io:8844
    export CREDHUB_CA_CERT='-----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
    '
    export BOSH_CLIENT=ops_manager

    2) bbr_key.pem 확인 (Bbr Ssh Credentials)
    
    bosh > Credentials > Bbr Ssh Credentials
    printf --  "-----BEGIN KEY----- MIIEkeycontents ----END  KEY-----" > bbr_key.pem

    3) 확인
    
    ubuntu@opsmanager:~/bbr$ bosh -e bosh.haas-218.pez.pivotal.io \
    > --ca-cert /var/tempest/workspaces/default/root_ca_certificate log-in
    Successfully authenticated with UAA
    
    Succeeded

    ubuntu@opsmanager:~/bbr$ bosh clean-up --all
    Using environment 'bosh.haas-218.pez.pivotal.io' as client 'ops_manager'
    
    Continue? [yN]: y
    
    Task 4796
    
    Task 4796 | 07:16:22 | Deleting releases: exporter-smoke-test/2.1.0-build.43
    Task 4796 | 07:16:22 | Deleting releases: opsman-cert-expiration-exporter/2.1.0-build.43
    Task 4796 | 07:16:22 | Deleting releases: loggregator-agent/6.2.0
    Task 4796 | 07:16:22 | Deleting releases: pks-cluster-discovery/2.1.0-build.43
    Task 4796 | 07:16:22 | Deleting releases: backup-and-restore-sdk/1.18.3
    Task 4796 | 07:16:22 | Deleting releases: healthwatch-smoke-test/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: prometheus-nginx/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: pxc/0.33.0
    Task 4796 | 07:16:23 | Deleting releases: grafana-auth-proxy/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: svm-forwarder/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting packages: opsman-cert-expiration-exporter/6fbca14ae2d13de88a58e947500022fadb8ddb0d3a700c61bb7071b732f11829
    Task 4796 | 07:16:23 | Deleting releases: pas-sli-exporter/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: bosh-deployments-exporter/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: prometheus/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: pas-exporter/2.1.0-build.43
    Task 4796 | 07:16:23 | Deleting releases: grafana/2.1.0-build.43
    

    4) 백업체크
    bbr director \
    --private-key-path bbr_key.pem \
    --username bbr \
    --host bosh.haas-218.pez.pivotal.io \
    pre-backup-check
    
    ubuntu@opsmanager:~/bbr$ bbr director \
    > --private-key-path bbr_key.pem \
    > --username bbr \
    > --host bosh.haas-218.pez.pivotal.io \
    > pre-backup-check
    [bbr] 2021/04/18 07:26:12 INFO - Looking for scripts
    [bbr] 2021/04/18 07:26:13 INFO - Detected order: credhub should be locked before uaa during backup
    [bbr] 2021/04/18 07:26:13 INFO - Detected order: credhub should be locked before uaa during restore
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/bbr-credhubdb/backup
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/bbr-credhubdb/restore
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/bbr-uaadb/backup
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/bbr-uaadb/restore
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/blobstore/backup
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/blobstore/restore
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/credhub/metadata
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/credhub/post-backup-unlock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/credhub/post-restore-unlock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/credhub/pre-backup-lock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/credhub/pre-restore-lock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/director/backup
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/director/restore
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/uaa/post-backup-unlock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/uaa/post-restore-unlock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/uaa/pre-backup-lock
    [bbr] 2021/04/18 07:26:13 INFO - bosh/0/uaa/pre-restore-lock
    [bbr] 2021/04/18 07:26:13 INFO - Running pre-checks for backup of bosh.haas-218.pez.pivotal.io...
    Director can be backed up.

    bosh -e bosh.haas-218.pez.pivotal.io --ca-cert /var/tempest/workspaces/default/root_ca_certificate deployments
    
    ubuntu@opsmanager:~/bbr$ bosh -e bosh.haas-218.pez.pivotal.io --ca-cert /var/tempest/workspaces/default/root_ca_certificate deployments
    Using environment 'bosh.haas-218.pez.pivotal.io' as client 'ops_manager'
    
    Name                                      Release(s)                            Stemcell(s)                                       Team(s)
    appMetrics-237e0a1d39a9736a338f           app-metrics/2.0.5-build.6             bosh-vsphere-esxi-ubuntu-xenial-go_agent/621.101  -
                                              bosh-dns/1.27.0
                                              bosh-dns-aliases/0.0.3
                                              bpm/1.1.8
                                              cf-cli/1.28.0
                                              log-store/4.0.14
                                              loggregator-agent/3.21
                                              postgres/41
                                              routing/0.188.0
                                              system-metrics/2.0.11
    bosh-health                               bosh-dns/1.27.0                       bosh-vsphere-esxi-ubuntu-xenial-go_agent/621.101  p-healthwatch2-pas-exporter-7f11c1c53a6c9fd16a0b
                                              bosh-dns-aliases/0.0.3
                                              bosh-health-exporter/2.1.0-build.43
                                              system-metrics/2.0.11
    cf-2220358aace41f8a2b1d                   backup-and-restore-sdk/1.18.0         bosh-vsphere-esxi-ubuntu-xenial-go_agent/621.101  -
                                              binary-offline-buildpack/1.0.36
                                              bosh-dns/1.27.0
                                              bosh-dns-aliases/0.0.3
                                              bosh-system-metrics-forwarder/0.0.19
                                              bpm/1.1.7
                                              capi/1.95.2
                                              cf-autoscaling/232
                                              cf-cli/1.27.0
                                              cf-networking/2.30.0
                                              cflinuxfs3/0.198.0
                                              credhub/2.6.1
                                              diego/2.47.0
                                              dotnet-core-offline-buildpack/2.3.12
                                              garden-runc/1.19.11
                                              go-offline-buildpack/1.9.14
                                              haproxy/10.0.0
                                              istio/1.3.0
                                              java-offline-buildpack/4.31.1
                                              log-cache/2.7.2
                                              loggregator/106.3.10
                                              loggregator-agent/6.0.2
                                              mapfs/1.2.4
                                              metric-registrar/1.1.1
                                              metrics-discovery/3.0.0
                                              mysql-monitoring/9.12.0
                                              nats/34
                                              nfs-volume/7.0.3
                                              nginx-offline-buildpack/1.1.11
                                              nodejs-offline-buildpack/1.7.24
                                              notifications/61
                                              notifications-ui/37
                                              php-offline-buildpack/4.4.18
                                              push-apps-manager-release/672.0.11
                                              push-usage-service-release/673.0.10
                                              pxc/0.25.0
                                              python-offline-buildpack/1.7.16
                                              r-offline-buildpack/1.1.7
                                              routing/0.203.0
                                              ruby-offline-buildpack/1.8.21
                                              silk/2.30.0
                                              smb-volume/3.0.1
                                              smoke-tests/2.2.0
                                              staticfile-offline-buildpack/1.5.9
                                              statsd-injector/1.11.15
                                              syslog/11.6.1
                                              system-metrics/2.0.11
                                              system-metrics-scraper/2.0.12
                                              uaa/74.5.18
    metric-store-a73e804e8ba57c63322c         bosh-dns/1.27.0                       bosh-vsphere-esxi-ubuntu-xenial-go_agent/621.101  -
                                              bosh-dns-aliases/0.0.3
                                              bpm/1.1.9
                                              metric-store/1.5.1
                                              routing/0.188.0
                                              system-metrics/2.0.11
    p-isolation-segment-24e9c806577ca0bd8202  bosh-dns/1.27.0                       bosh-vsphere-esxi-ubuntu-xenial-go_agent/621.101  -
                                              bosh-dns-aliases/0.0.3
                                              bpm/1.1.7
                                              cf-networking/2.31.0
                                              cflinuxfs3/0.202.0
                                              diego/2.47.0
                                              garden-runc/1.19.14
                                              haproxy/10.0.0
                                              loggregator-agent/6.0.2
                                              mapfs/1.2.4
                                              metrics-discovery/3.0.0
                                              nfs-volume/7.0.3
                                              routing/0.203.0
                                              silk/2.31.0
                                              smb-volume/3.0.1
                                              smoke-tests/2.0.6
                                              syslog/11.6.1
                                              system-metrics/2.0.11
    
    5 deployments

    #Pre-check Test
    bbr deployment \
    --target bosh.haas-218.pez.pivotal.io \
    --username bbr_client \
    --password xxxxx \
    --deployment cf-2220358aace41f8a2b1d \
    --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    pre-backup-check
    
    ubuntu@opsmanager:~/bbr$ bbr deployment \
    > --target bosh.haas-218.pez.pivotal.io \
    > --username bbr_client \
    > --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    > --deployment cf-2220358aace41f8a2b1d \
    > --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    > pre-backup-check
    [bbr] 2021/04/18 07:29:55 INFO - Looking for scripts
    [bbr] 2021/04/18 07:30:02 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/post-backup-unlock
    [bbr] 2021/04/18 07:30:02 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/post-restore-unlock
    [bbr] 2021/04/18 07:30:02 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/pre-backup-lock
    [bbr] 2021/04/18 07:30:02 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/pre-restore-lock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/post-backup-unlock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/post-restore-unlock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/pre-backup-lock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/pre-restore-lock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/post-backup-unlock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/post-restore-unlock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/pre-backup-lock
    [bbr] 2021/04/18 07:30:07 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/pre-restore-lock
    [bbr] 2021/04/18 07:30:09 INFO - nfs_server/4b0dbbb3-bf17-4122-874c-8404645fc622/blobstore/backup
    [bbr] 2021/04/18 07:30:09 INFO - nfs_server/4b0dbbb3-bf17-4122-874c-8404645fc622/blobstore/restore
    [bbr] 2021/04/18 07:30:14 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/post-backup-unlock
    [bbr] 2021/04/18 07:30:14 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/post-restore-unlock
    [bbr] 2021/04/18 07:30:14 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/pre-backup-lock
    [bbr] 2021/04/18 07:30:14 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/pre-restore-lock
    [bbr] 2021/04/18 07:30:18 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/post-backup-unlock
    [bbr] 2021/04/18 07:30:18 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/post-restore-unlock
    [bbr] 2021/04/18 07:30:18 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/pre-backup-lock
    [bbr] 2021/04/18 07:30:18 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/pre-restore-lock
    [bbr] 2021/04/18 07:30:20 INFO - Detected order: credhub should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 07:30:20 INFO - Detected order: credhub should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:20 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/metadata
    [bbr] 2021/04/18 07:30:20 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/post-backup-unlock
    [bbr] 2021/04/18 07:30:20 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/post-restore-unlock
    [bbr] 2021/04/18 07:30:20 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/pre-backup-lock
    [bbr] 2021/04/18 07:30:20 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/pre-restore-lock
    [bbr] 2021/04/18 07:30:26 INFO - diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4/policy-server/post-backup-unlock
    [bbr] 2021/04/18 07:30:26 INFO - diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4/policy-server/pre-backup-lock
    [bbr] 2021/04/18 07:30:27 INFO - diego_database/6b7af71e-32ed-4975-a077-ff490174fb15/policy-server/post-backup-unlock
    [bbr] 2021/04/18 07:30:27 INFO - diego_database/6b7af71e-32ed-4975-a077-ff490174fb15/policy-server/pre-backup-lock
    [bbr] 2021/04/18 07:30:27 INFO - diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571/policy-server/post-backup-unlock
    [bbr] 2021/04/18 07:30:27 INFO - diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571/policy-server/pre-backup-lock
    [bbr] 2021/04/18 07:30:35 INFO - Detected order: routing-api should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/post-backup-unlock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/post-restore-unlock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/pre-backup-lock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/pre-restore-lock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/metadata
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/post-backup-unlock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/post-restore-unlock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/pre-backup-lock
    [bbr] 2021/04/18 07:30:35 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/pre-restore-lock
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: backup-restore-notifications should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: backup-restore-notifications should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: backup-restore-notifications should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: backup-restore-notifications should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-apps-manager should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-apps-manager should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-smbbroker should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-smbbroker should be locked before credhub/credhub during backup
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-smbbroker should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-smbbroker should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-smbbroker should be locked before credhub/credhub during restore
    [bbr] 2021/04/18 07:30:41 INFO - Detected order: bbr-smbbroker should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: bbr-usage-servicedb should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: bbr-usage-servicedb should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: bbr-usage-servicedb should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: bbr-usage-servicedb should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: nfsbroker-bbr-lock should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: nfsbroker-bbr-lock should be locked before credhub/credhub during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: nfsbroker-bbr-lock should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: nfsbroker-bbr-lock should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: nfsbroker-bbr-lock should be locked before credhub/credhub during restore
    [bbr] 2021/04/18 07:30:42 INFO - Detected order: nfsbroker-bbr-lock should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-smbbroker/metadata
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-smbbroker/post-restore-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-routingdb/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-routingdb/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/metadata
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/post-backup-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/post-restore-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/pre-backup-lock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/pre-restore-lock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/nfsbroker-bbr-lock/metadata
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/nfsbroker-bbr-lock/post-restore-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cfnetworkingdb/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cfnetworkingdb/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cloudcontrollerdb/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cloudcontrollerdb/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-credhubdb/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-credhubdb/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/metadata
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/post-backup-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/post-restore-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/pre-backup-lock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/pre-restore-lock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/metadata
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/post-restore-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/pre-restore-lock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-pcf-autoscaling/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-pcf-autoscaling/restore
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-apps-manager/metadata
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-apps-manager/post-restore-unlock
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-uaadb/backup
    [bbr] 2021/04/18 07:30:42 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-uaadb/restore
    [bbr] 2021/04/18 07:30:47 INFO - Running pre-checks for backup of cf-2220358aace41f8a2b1d...
    [07:30:55] Deployment 'cf-2220358aace41f8a2b1d' can be backed up.
    ubuntu@opsmanager:~/bbr$


    5) 백업수행 bosh
    
    bbr director \
    --private-key-path bbr_key.pem \
    --username bbr \
    --host bosh.haas-218.pez.pivotal.io \
    backup
    
    ubuntu@opsmanager:~/bbr$ bbr director \
    > --private-key-path bbr_key.pem \
    > --username bbr \
    > --host bosh.haas-218.pez.pivotal.io \
    > backup
    [bbr] 2021/04/18 07:31:32 INFO - Looking for scripts
    [bbr] 2021/04/18 07:31:32 INFO - Detected order: credhub should be locked before uaa during backup
    [bbr] 2021/04/18 07:31:32 INFO - Detected order: credhub should be locked before uaa during restore
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/director/backup
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/director/restore
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/uaa/post-backup-unlock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/uaa/post-restore-unlock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/uaa/pre-backup-lock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/uaa/pre-restore-lock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/bbr-credhubdb/backup
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/bbr-credhubdb/restore
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/bbr-uaadb/backup
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/bbr-uaadb/restore
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/blobstore/backup
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/blobstore/restore
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/credhub/metadata
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/credhub/post-backup-unlock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/credhub/post-restore-unlock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/credhub/pre-backup-lock
    [bbr] 2021/04/18 07:31:32 INFO - bosh/0/credhub/pre-restore-lock
    [bbr] 2021/04/18 07:31:32 INFO - Running pre-checks for backup of bosh.haas-218.pez.pivotal.io...
    [bbr] 2021/04/18 07:31:32 INFO - Starting backup of bosh.haas-218.pez.pivotal.io...
    [bbr] 2021/04/18 07:31:32 INFO - Running pre-backup-lock scripts...
    [bbr] 2021/04/18 07:31:32 INFO - Locking credhub on bosh/0 for backup...
    [bbr] 2021/04/18 07:31:33 INFO - Finished locking credhub on bosh/0 for backup.
    [bbr] 2021/04/18 07:31:33 INFO - Locking uaa on bosh/0 for backup...
    [bbr] 2021/04/18 07:31:39 INFO - Finished locking uaa on bosh/0 for backup.
    [bbr] 2021/04/18 07:31:39 INFO - Finished running pre-backup-lock scripts.
    [bbr] 2021/04/18 07:31:39 INFO - Running backup scripts...
    [bbr] 2021/04/18 07:31:39 INFO - Backing up director on bosh/0...
    [bbr] 2021/04/18 07:31:39 INFO - Backing up bbr-credhubdb on bosh/0...
    [bbr] 2021/04/18 07:31:39 INFO - Backing up bbr-uaadb on bosh/0...
    [bbr] 2021/04/18 07:31:39 INFO - Backing up blobstore on bosh/0...
    [bbr] 2021/04/18 07:31:39 INFO - Finished backing up bbr-uaadb on bosh/0.
    [bbr] 2021/04/18 07:31:41 INFO - Finished backing up blobstore on bosh/0.
    [bbr] 2021/04/18 07:31:41 INFO - Finished backing up bbr-credhubdb on bosh/0.
    [bbr] 2021/04/18 07:31:42 INFO - Finished backing up director on bosh/0.
    [bbr] 2021/04/18 07:31:42 INFO - Finished running backup scripts.
    [bbr] 2021/04/18 07:31:42 INFO - Running post-backup-unlock scripts...
    [bbr] 2021/04/18 07:31:42 INFO - Unlocking uaa on bosh/0...
    [bbr] 2021/04/18 07:31:48 INFO - Finished unlocking uaa on bosh/0.
    [bbr] 2021/04/18 07:31:48 INFO - Unlocking credhub on bosh/0...
    [bbr] 2021/04/18 07:31:48 INFO - Finished unlocking credhub on bosh/0.
    [bbr] 2021/04/18 07:31:48 INFO - Finished running post-backup-unlock scripts.
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup -- 2.8M uncompressed -- for job director on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup -- 4.0M uncompressed -- for job bbr-credhubdb on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup -- 4.0K uncompressed -- for job bbr-uaadb on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup -- 16G uncompressed -- for job blobstore on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 5% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 5% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 10% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 10% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 15% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 15% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 20% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 25% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 30% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 35% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 20% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-uaadb on bosh/0 -- 100% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 41% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 25% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 30% complete
    [bbr] 2021/04/18 07:31:49 INFO - Finished copying backup -- for job bbr-uaadb on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 46% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 51% complete
    [bbr] 2021/04/18 07:31:49 INFO - Starting validity checks -- for job bbr-uaadb on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 57% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 62% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 67% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 73% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 78% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 83% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 89% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 94% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 99% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job director on bosh/0 -- 100% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 35% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 40% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 45% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 50% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 55% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 60% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 65% complete
    [bbr] 2021/04/18 07:31:49 INFO - Finished copying backup -- for job director on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Starting validity checks -- for job director on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 70% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 75% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 80% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 85% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 90% complete
    [bbr] 2021/04/18 07:31:49 INFO - Copying backup for job bbr-credhubdb on bosh/0 -- 95% complete
    [bbr] 2021/04/18 07:31:49 INFO - Finished copying backup -- for job bbr-credhubdb on bosh/0...
    [bbr] 2021/04/18 07:31:49 INFO - Starting validity checks -- for job bbr-credhubdb on bosh/0...
    [bbr] 2021/04/18 07:31:50 INFO - Finished validity checks -- for job bbr-uaadb on bosh/0...
    [bbr] 2021/04/18 07:31:50 INFO - Finished validity checks -- for job director on bosh/0...
    [bbr] 2021/04/18 07:31:50 INFO - Finished validity checks -- for job bbr-credhubdb on bosh/0...
    [bbr] 2021/04/18 07:32:02 INFO - Copying backup for job blobstore on bosh/0 -- 5% complete
    [bbr] 2021/04/18 07:32:18 INFO - Copying backup for job blobstore on bosh/0 -- 10% complete
    [bbr] 2021/04/18 07:32:28 INFO - Copying backup for job blobstore on bosh/0 -- 15% complete
    [bbr] 2021/04/18 07:32:44 INFO - Copying backup for job blobstore on bosh/0 -- 20% complete
    [bbr] 2021/04/18 07:32:56 INFO - Copying backup for job blobstore on bosh/0 -- 25% complete
    [bbr] 2021/04/18 07:33:07 INFO - Copying backup for job blobstore on bosh/0 -- 30% complete
    [bbr] 2021/04/18 07:33:21 INFO - Copying backup for job blobstore on bosh/0 -- 35% complete
    [bbr] 2021/04/18 07:33:31 INFO - Copying backup for job blobstore on bosh/0 -- 40% complete
    [bbr] 2021/04/18 07:33:47 INFO - Copying backup for job blobstore on bosh/0 -- 45% complete
    [bbr] 2021/04/18 07:34:00 INFO - Copying backup for job blobstore on bosh/0 -- 50% complete
    [bbr] 2021/04/18 07:34:15 INFO - Copying backup for job blobstore on bosh/0 -- 55% complete
    [bbr] 2021/04/18 07:34:24 INFO - Copying backup for job blobstore on bosh/0 -- 60% complete
    [bbr] 2021/04/18 07:34:36 INFO - Copying backup for job blobstore on bosh/0 -- 65% complete
    [bbr] 2021/04/18 07:34:50 INFO - Copying backup for job blobstore on bosh/0 -- 70% complete
    [bbr] 2021/04/18 07:35:00 INFO - Copying backup for job blobstore on bosh/0 -- 75% complete
    [bbr] 2021/04/18 07:35:19 INFO - Copying backup for job blobstore on bosh/0 -- 80% complete
    [bbr] 2021/04/18 07:35:32 INFO - Copying backup for job blobstore on bosh/0 -- 85% complete
    [bbr] 2021/04/18 07:35:46 INFO - Copying backup for job blobstore on bosh/0 -- 90% complete
    [bbr] 2021/04/18 07:35:55 INFO - Copying backup for job blobstore on bosh/0 -- 95% complete
    [bbr] 2021/04/18 07:36:13 INFO - Finished copying backup -- for job blobstore on bosh/0...
    [bbr] 2021/04/18 07:36:13 INFO - Starting validity checks -- for job blobstore on bosh/0...
    
    [bbr] 2021/04/18 07:43:46 INFO - Finished validity checks -- for job blobstore on bosh/0...
    [bbr] 2021/04/18 07:43:46 INFO - Backup created of bosh.haas-218.pez.pivotal.io on 2021-04-18 07:43:46.340713094 +0000 UTC m=+734.153164830
    [bbr] 2021/04/18 07:43:46 INFO - Cleaning up...
    
    
    ubuntu@opsmanager:~/bbr$ ls -rlt
    total 12
    -rw-rw-r-- 1 ubuntu ubuntu 2761 Apr 18 07:10 bosh-env.sh
    -rw-rw-r-- 1 ubuntu ubuntu 2146 Apr 18 07:23 bbr_key.pem
    drwx------ 2 ubuntu ubuntu 4096 Apr 18 07:31 bosh.haas-218.pez.pivotal.io_20210418T073132Z
    
    #Cleanup
    bbr director \
    --private-key-path bbr_key.pem \
    --username bbr \
    --host bosh.haas-218.pez.pivotal.io \
    backup-cleanup

    ubuntu@opsmanager:~/bbr$ bbr director \
    > --private-key-path bbr_key.pem \
    > --username bbr \
    > --host bosh.haas-218.pez.pivotal.io \
    > backup-cleanup
    [bbr] 2021/04/18 07:44:49 INFO - Looking for scripts
    [bbr] 2021/04/18 07:44:49 INFO - Detected order: credhub should be locked before uaa during backup
    [bbr] 2021/04/18 07:44:49 INFO - Detected order: credhub should be locked before uaa during restore
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/bbr-credhubdb/backup
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/bbr-credhubdb/restore
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/bbr-uaadb/backup
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/bbr-uaadb/restore
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/blobstore/backup
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/blobstore/restore
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/credhub/metadata
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/credhub/post-backup-unlock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/credhub/post-restore-unlock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/credhub/pre-backup-lock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/credhub/pre-restore-lock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/director/backup
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/director/restore
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/uaa/post-backup-unlock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/uaa/post-restore-unlock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/uaa/pre-backup-lock
    [bbr] 2021/04/18 07:44:49 INFO - bosh/0/uaa/pre-restore-lock
    [bbr] 2021/04/18 07:44:49 INFO - Running post-backup-unlock scripts...
    [bbr] 2021/04/18 07:44:49 INFO - Unlocking uaa on bosh/0...
    [bbr] 2021/04/18 07:44:55 INFO - Finished unlocking uaa on bosh/0.
    [bbr] 2021/04/18 07:44:55 INFO - Unlocking credhub on bosh/0...
    [bbr] 2021/04/18 07:44:56 INFO - Finished unlocking credhub on bosh/0.
    [bbr] 2021/04/18 07:44:56 INFO - Finished running post-backup-unlock scripts.
    [bbr] 2021/04/18 07:44:56 INFO - Cleaning up...
    [bbr] 2021/04/18 07:44:57 INFO - 'bosh.haas-218.pez.pivotal.io' cleaned up


    6) 백업수행 TAS
    
    ubuntu@opsmanager:~/bbr$ bbr deployment \
    > --target bosh.haas-218.pez.pivotal.io \
    > --username bbr_client \
    > --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    > --deployment cf-2220358aace41f8a2b1d \
    > --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    > backup-cleanup
    [bbr] 2021/04/18 14:55:24 INFO - Looking for scripts
    [bbr] 2021/04/18 14:55:34 INFO - Detected order: credhub should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:34 INFO - Detected order: credhub should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/metadata
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/post-backup-unlock
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/post-restore-unlock
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/pre-backup-lock
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/pre-restore-lock
    [bbr] 2021/04/18 14:55:38 INFO - diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4/policy-server/post-backup-unlock
    [bbr] 2021/04/18 14:55:38 INFO - diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4/policy-server/pre-backup-lock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/6b7af71e-32ed-4975-a077-ff490174fb15/policy-server/post-backup-unlock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/6b7af71e-32ed-4975-a077-ff490174fb15/policy-server/pre-backup-lock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571/policy-server/post-backup-unlock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571/policy-server/pre-backup-lock
    [bbr] 2021/04/18 14:55:41 INFO - nfs_server/4b0dbbb3-bf17-4122-874c-8404645fc622/blobstore/backup
    [bbr] 2021/04/18 14:55:41 INFO - nfs_server/4b0dbbb3-bf17-4122-874c-8404645fc622/blobstore/restore
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/post-backup-unlock
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/post-restore-unlock
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/pre-backup-lock
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/pre-restore-lock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/post-backup-unlock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/post-restore-unlock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/pre-backup-lock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/pre-restore-lock
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-apps-manager should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-apps-manager should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before credhub/credhub during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before credhub/credhub during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before credhub/credhub during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before credhub/credhub during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/pre-restore-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-smbbroker/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-smbbroker/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/post-backup-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/pre-backup-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/pre-restore-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/post-backup-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/pre-backup-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/pre-restore-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cfnetworkingdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cfnetworkingdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-uaadb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-uaadb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cloudcontrollerdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cloudcontrollerdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-credhubdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-credhubdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-routingdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-routingdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/nfsbroker-bbr-lock/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/nfsbroker-bbr-lock/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-pcf-autoscaling/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-pcf-autoscaling/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-apps-manager/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-apps-manager/post-restore-unlock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/post-backup-unlock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/post-restore-unlock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/pre-backup-lock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/pre-restore-lock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/post-backup-unlock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/post-restore-unlock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/pre-backup-lock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/pre-restore-lock
    [bbr] 2021/04/18 14:56:09 INFO - Detected order: routing-api should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/post-backup-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/post-restore-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/pre-backup-lock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/pre-restore-lock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/metadata
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/post-backup-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/post-restore-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/pre-backup-lock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/pre-restore-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/post-backup-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/post-restore-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/pre-backup-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/pre-restore-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/post-backup-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/post-restore-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/pre-backup-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/pre-restore-lock
    [bbr] 2021/04/18 14:56:11 INFO - Running post-backup-unlock scripts...
    [bbr] 2021/04/18 14:56:11 INFO - Unlocking uaa on uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6...
    [bbr] 2021/04/18 14:56:18 INFO - Finished unlocking uaa on uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6.
    [bbr] 2021/04/18 14:56:18 INFO - Unlocking cloud_controller_ng on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434...
    [bbr] 2021/04/18 14:56:18 INFO - Unlocking credhub on credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b...
    [bbr] 2021/04/18 14:56:18 INFO - Finished unlocking credhub on credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b.
    
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cloud_controller_ng on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434.
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking lock-unlock-pcf-autoscaling on backup_restore/40057770-025d-4bec-9507-09d721a94e83...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking policy-server on diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking policy-server on diego_database/6b7af71e-32ed-4975-a077-ff490174fb15...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking policy-server on diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking cloud_controller_worker on cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking tps on diego_brain/7f005103-6c42-456e-9c08-6b2e61546761...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking tps on diego_brain/500ead94-a127-46d4-b201-c7993b342b67...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking routing-api on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking cc_deployment_updater on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking bbr-usage-servicedb on backup_restore/40057770-025d-4bec-9507-09d721a94e83...
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking policy-server on diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571.
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking cloud_controller_clock on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7...
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking tps on diego_brain/500ead94-a127-46d4-b201-c7993b342b67.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking policy-server on diego_database/6b7af71e-32ed-4975-a077-ff490174fb15.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking tps on diego_brain/7f005103-6c42-456e-9c08-6b2e61546761.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cloud_controller_worker on cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking routing-api on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking policy-server on diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cc_deployment_updater on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cloud_controller_clock on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7.
    [bbr] 2021/04/18 14:56:51 INFO - Finished unlocking lock-unlock-pcf-autoscaling on backup_restore/40057770-025d-4bec-9507-09d721a94e83.
    [bbr] 2021/04/18 14:58:53 INFO - Finished unlocking bbr-usage-servicedb on backup_restore/40057770-025d-4bec-9507-09d721a94e83.
    [bbr] 2021/04/18 14:58:53 INFO - Finished running post-backup-unlock scripts.
    [bbr] 2021/04/18 14:58:53 INFO - 'cf-2220358aace41f8a2b1d' cleaned up


    bbr deployment \
    --target bosh.haas-218.pez.pivotal.io \
    --username bbr_client \
    --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    --deployment cf-2220358aace41f8a2b1d \
    --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    backup-cleanup
    
    ubuntu@opsmanager:~/bbr$ bbr deployment \
    > --target bosh.haas-218.pez.pivotal.io \
    > --username bbr_client \
    > --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    > --deployment cf-2220358aace41f8a2b1d \
    > --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    > backup-cleanup
    [bbr] 2021/04/18 14:55:24 INFO - Looking for scripts
    [bbr] 2021/04/18 14:55:34 INFO - Detected order: credhub should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:34 INFO - Detected order: credhub should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/metadata
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/post-backup-unlock
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/post-restore-unlock
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/pre-backup-lock
    [bbr] 2021/04/18 14:55:34 INFO - credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b/credhub/pre-restore-lock
    [bbr] 2021/04/18 14:55:38 INFO - diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4/policy-server/post-backup-unlock
    [bbr] 2021/04/18 14:55:38 INFO - diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4/policy-server/pre-backup-lock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/6b7af71e-32ed-4975-a077-ff490174fb15/policy-server/post-backup-unlock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/6b7af71e-32ed-4975-a077-ff490174fb15/policy-server/pre-backup-lock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571/policy-server/post-backup-unlock
    [bbr] 2021/04/18 14:55:39 INFO - diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571/policy-server/pre-backup-lock
    [bbr] 2021/04/18 14:55:41 INFO - nfs_server/4b0dbbb3-bf17-4122-874c-8404645fc622/blobstore/backup
    [bbr] 2021/04/18 14:55:41 INFO - nfs_server/4b0dbbb3-bf17-4122-874c-8404645fc622/blobstore/restore
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/post-backup-unlock
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/post-restore-unlock
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/pre-backup-lock
    [bbr] 2021/04/18 14:55:53 INFO - diego_brain/7f005103-6c42-456e-9c08-6b2e61546761/tps/pre-restore-lock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/post-backup-unlock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/post-restore-unlock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/pre-backup-lock
    [bbr] 2021/04/18 14:55:54 INFO - diego_brain/500ead94-a127-46d4-b201-c7993b342b67/tps/pre-restore-lock
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: backup-restore-notifications should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-apps-manager should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-apps-manager should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before credhub/credhub during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before credhub/credhub during restore
    [bbr] 2021/04/18 14:55:56 INFO - Detected order: bbr-smbbroker should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: bbr-usage-servicedb should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: lock-unlock-pcf-autoscaling should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before uaa/uaa during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before credhub/credhub during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before capi/cloud_controller_ng during backup
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before credhub/credhub during restore
    [bbr] 2021/04/18 14:55:57 INFO - Detected order: nfsbroker-bbr-lock should be locked before capi/cloud_controller_ng during restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/pre-restore-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-notifications/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-smbbroker/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-smbbroker/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/post-backup-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/pre-backup-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/pre-restore-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-usage-servicedb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/post-backup-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/pre-backup-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/lock-unlock-pcf-autoscaling/pre-restore-lock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cfnetworkingdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cfnetworkingdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-uaadb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-uaadb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cloudcontrollerdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-cloudcontrollerdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-credhubdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-credhubdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-routingdb/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-routingdb/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/nfsbroker-bbr-lock/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/nfsbroker-bbr-lock/post-restore-unlock
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-pcf-autoscaling/backup
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/backup-restore-pcf-autoscaling/restore
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-apps-manager/metadata
    [bbr] 2021/04/18 14:55:57 INFO - backup_restore/40057770-025d-4bec-9507-09d721a94e83/bbr-apps-manager/post-restore-unlock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/post-backup-unlock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/post-restore-unlock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/pre-backup-lock
    [bbr] 2021/04/18 14:55:59 INFO - cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38/cloud_controller_worker/pre-restore-lock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/post-backup-unlock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/post-restore-unlock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/pre-backup-lock
    [bbr] 2021/04/18 14:56:07 INFO - uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6/uaa/pre-restore-lock
    [bbr] 2021/04/18 14:56:09 INFO - Detected order: routing-api should be locked before uaa/uaa during restore
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/post-backup-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/post-restore-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/pre-backup-lock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/cloud_controller_ng/pre-restore-lock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/metadata
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/post-backup-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/post-restore-unlock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/pre-backup-lock
    [bbr] 2021/04/18 14:56:09 INFO - cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434/routing-api/pre-restore-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/post-backup-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/post-restore-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/pre-backup-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cc_deployment_updater/pre-restore-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/post-backup-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/post-restore-unlock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/pre-backup-lock
    [bbr] 2021/04/18 14:56:11 INFO - clock_global/29745dc2-5e34-4962-906a-193b8821b3a7/cloud_controller_clock/pre-restore-lock
    [bbr] 2021/04/18 14:56:11 INFO - Running post-backup-unlock scripts...
    [bbr] 2021/04/18 14:56:11 INFO - Unlocking uaa on uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6...
    [bbr] 2021/04/18 14:56:18 INFO - Finished unlocking uaa on uaa/0b9cb783-88f8-42c7-8651-97e90d2bffa6.
    [bbr] 2021/04/18 14:56:18 INFO - Unlocking cloud_controller_ng on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434...
    [bbr] 2021/04/18 14:56:18 INFO - Unlocking credhub on credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b...
    [bbr] 2021/04/18 14:56:18 INFO - Finished unlocking credhub on credhub/db8bc6f7-1f73-4de1-86cf-80b63206000b.
    
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cloud_controller_ng on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434.
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking lock-unlock-pcf-autoscaling on backup_restore/40057770-025d-4bec-9507-09d721a94e83...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking policy-server on diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking policy-server on diego_database/6b7af71e-32ed-4975-a077-ff490174fb15...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking policy-server on diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking cloud_controller_worker on cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking tps on diego_brain/7f005103-6c42-456e-9c08-6b2e61546761...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking tps on diego_brain/500ead94-a127-46d4-b201-c7993b342b67...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking routing-api on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking cc_deployment_updater on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7...
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking bbr-usage-servicedb on backup_restore/40057770-025d-4bec-9507-09d721a94e83...
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking policy-server on diego_database/e9f0111e-5b71-4e33-83bb-90ad88001571.
    [bbr] 2021/04/18 14:56:48 INFO - Unlocking cloud_controller_clock on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7...
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking tps on diego_brain/500ead94-a127-46d4-b201-c7993b342b67.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking policy-server on diego_database/6b7af71e-32ed-4975-a077-ff490174fb15.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking tps on diego_brain/7f005103-6c42-456e-9c08-6b2e61546761.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cloud_controller_worker on cloud_controller_worker/0d3c583b-6207-4e1d-9042-2694d522cb38.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking routing-api on cloud_controller/ad46df23-2c57-4b10-ac7e-133add7ff434.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking policy-server on diego_database/85fd547e-4a00-422c-8589-7d352cb1a8f4.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cc_deployment_updater on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7.
    [bbr] 2021/04/18 14:56:48 INFO - Finished unlocking cloud_controller_clock on clock_global/29745dc2-5e34-4962-906a-193b8821b3a7.
    [bbr] 2021/04/18 14:56:51 INFO - Finished unlocking lock-unlock-pcf-autoscaling on backup_restore/40057770-025d-4bec-9507-09d721a94e83.
    [bbr] 2021/04/18 14:58:53 INFO - Finished unlocking bbr-usage-servicedb on backup_restore/40057770-025d-4bec-9507-09d721a94e83.
    [bbr] 2021/04/18 14:58:53 INFO - Finished running post-backup-unlock scripts.
    [bbr] 2021/04/18 14:58:53 INFO - 'cf-2220358aace41f8a2b1d' cleaned up
    
    
    ubuntu@opsmanager:~/bbr$
    ubuntu@opsmanager:~/bbr$


    7) 백업수행 IS
    
    bbr deployment \
    --target bosh.haas-218.pez.pivotal.io \
    --username bbr_client \
    --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    --deployment p-isolation-segment-24e9c806577ca0bd8202 \
    --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    backup
    
    ubuntu@opsmanager:~/bbr$ bbr deployment \
    > --target bosh.haas-218.pez.pivotal.io \
    > --username bbr_client \
    > --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    > --deployment p-isolation-segment-24e9c806577ca0bd8202 \
    > --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    > backup
    [bbr] 2021/04/18 15:00:24 INFO - Looking for scripts
    [bbr] 2021/04/18 15:00:32 INFO - Running pre-checks for backup of p-isolation-segment-24e9c806577ca0bd8202...
    1 error occurred:
    error 1:
    Deployment 'p-isolation-segment-24e9c806577ca0bd8202' has no backup scripts

    bbr deployment \
    --target bosh.haas-218.pez.pivotal.io \
    --username bbr_client \
    --password YOi5dfba-Qv8ckKzWLe-xmRtfzEwEJ8f \
    --deployment p-isolation-segment-24e9c806577ca0bd8202 \
    --ca-cert /var/tempest/workspaces/default/root_ca_certificate \
    backup-cleanup






