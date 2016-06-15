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
demo_route_53_zone: <my route53 delegated zone>

demo_additional_ssh_keys:
- <ssh pub key 1>
- <ssh pub key 2>

demo_github_clientid: <github client id>
demo_github_clientsecret: <github client secret>
demo_github_organizations:
- <github organization>

openshift_master_htpasswd_users:
  demo: <pre-hashed password>

demo_registry_s3_accesskey: <s3 access key for registry>
demo_registry_s3_secretkey: <s3 secret key for registry>
demo_registry_s3_bucket_prefix: <prefix to use for s3 bucket>
demo_registry_s3_bucket: <s3 bucket name>

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
ansible-playbook -i inventory/aws/ec2.py -e "@vars.common.yml" -e "@vars.demo.prod.yml" -e"@~/keynote-demo-private-vars.yml" provision.yml
```

- Configure the hosts:
```
ansible-playbook -i inventory/aws/ec2.py -e "@vars.common.yml" -e "@vars.demo.prod.yml" -e "@~/keynote-demo-private-vars.yml" configure.yml
```

- Teardown the environment:
```
ansible-playbook -i inventory/aws/ec2.py -e "@vars.common.yml" -e "@vars.demo.prod.yml" -e "@~/keynote-demo-private-vars.yml" teardown.yml
```
