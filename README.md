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
