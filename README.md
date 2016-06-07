# demo-env-ansible

Running locally:

- Export the needed variables:
  ```
  export AWS_ACCESS_KEY_ID=<your aws access key id>
  export AWS_SECRET_ACCESS_KEY=<your aws secret access key>
  ```
  - Alternatively, add these to a file (say ~/keynote-demo-env-vars) and
    source it when needed.

- Create a private variables file (say ~/keynote-demo-private-vars.yml) with
  the following contents:
  ```
---
demo_api_port: 443
demo_route_53_zone: <my route53 delegated zone>
demo_route_53_subdomain: "{{ cluster_id }}.{{ env_id }}.{{ demo_route_53_zone }}"

env_id: prod
cluster_id: demo
deployment_type: openshift-enterprise

openshift_master_identity_providers:
- name: htpasswd_auth
  login: true
  challenge: true
  kind: HTPasswdPasswordIdentityProvider
  filename: /etc/origin/master/htpasswd
openshift_master_htpasswd_users:
  demo: <pre-hashed password>

openshift_master_access_token_max_seconds: 2419200
openshift_master_api_port: "{{ demo_api_port }}"
openshift_master_console_port: "{{ demo_api_port }}"
openshift_master_logging_public_url: "https://kibana.apps.{{ demo_route_53_subdomain }}"
openshift_master_metrics_public_url: "https://metrics.apps.{{ demo_route_53_subdomain }}/hawkular/metrics"
osm_cluster_network_cidr: 10.0.0.0/8
osm_host_subnet_length: 16
osm_default_subdomain: "apps.{{ demo_route_53_subdomain }}"
openshift_master_cluster_method: native
openshift_master_cluster_hostname: "openshift.internal.{{ demo_route_53_subdomain }}"
openshift_master_cluster_public_hostname: "openshift.{{ demo_route_53_subdomain }}"
osm_default_node_selector: "node_type=compute"
#openshift_hosted_router_selector: "node_type=infra"
openshift_cloudprovider_kind: aws
openshift_hosted_infra_selector: "node_type=infra"
openshift_hosted_registry_storage_kind: object
openshift_hosted_registry_storage_provider: s3
openshift_hosted_registry_storage_s3_accesskey: <my_s3_access_key>
openshift_hosted_registry_storage_s3_secretkey: <my_s3_secret_key>
openshift_hosted_registry_storage_s3_bucket: <my_s3_bucket>
openshift_hosted_registry_storage_s3_region: us-east-1
openshift_hosted_registry_storage_s3_create: yes
openshift_hosted_metrics_storage_kind: dynamic
openshift_hosted_metrics_cassandra_nodes: 3
openshift_hosted_metrics_storage_volume_size: 100Gi
openshift_hosted_metrics_deployer_prefix: detiber/origin-
openshift_hosted_metrics_deployer_version: v1.2.0-dynamic

openshift_additional_repos:
- id: ops_mirror
  name: Ops Mirror
  baseurl: <url to private mirror>
  enabled: 1
  gpgcheck: 0
  sslclientcert: /var/lib/yum/client-cert.pem
  sslclientkey: /var/lib/yum/client-key.pem
demo_repo_cert_contents: <contents of the client cert for above repo>
demo_repo_key_contents: <contents of the client key for above repo>
   ```

- Create the hosts:
```
ansible-playbook -i inventory/aws/ec2.py -e"@~/keynote-demo-private-vars.yml" provision.yml
```

- Configure the hosts:
```
ansible-playbook -i inventory/aws/ec2.py -e "@~/keynote-demo-private-vars.yml" configure.yml
```

- Teardown the environment:
```
ansible-playbook -i inventory/aws/ec2.py -e "@~/keynote-demo-private-vars.yml" teardown.yml
```
