# Ansible for Pulsar

This repo contains an ansible playbook which will create a instance of
pulsar running on three regions.

Each region contains 3 zookeeper nodes and 3 pulsar/bookkeeper
nodes. One of the zookeeper nodes in each region is also part of a
global zookeeper instance.

The playbook creates all the nodes on GCE using n1-standard-1
instances. Each instance has 1 CPU, and 3.8GB Ram.

By default all instances are preemptible. This means they are cheaper,
though google may reclaim them at any time. If this happens, try
running the script again.

To cleanup, just delete all the instances from the GCE console.

# Preparing GCE

To run the playbook, you need a set of credentials. Create a service
account in "IAM & admin > Service Accounts". The service account
should have "Project> Service Account Actor" and "Compute Engine >
Compute Admin" roles. Make sure you tick the field to furnish a new
private key. Place the generated json file with credentials in the
root directory of the playbook. Then modify vars/credentials.yaml to
point to the downloaded file. Also update the email address and
project to point to those for the service account. I recommend also
creating a new project for the playbook to make cleanup easier.

# SSH Keys

The host running ansible must be able to ssh into the generated
instances. For this, generate an ssh key if you do not have one
already and add it to the project in "Metadata > SSH keys".

# Running the playbook

To run the playbook, run the following commands:

```
export ANSIBLE_HOST_KEY_CHECKING=False
ansible-playbook pulsar.yaml
```

It will take a while to run. Once it finishes it will print out the
list of hosts and their IPs.

Ensure the cluster has come up by logging into one of the pulsar nodes
and running:

```
/opt/pulsar/bin/pulsar-admin clusters list
```

The output should list the 3 regions and the global region. You can
now start creating properties and namespaces, publishing messages and
subscribing to topics. See "Provisioning new tenants" at
http://pulsar.apache.org/docs/latest/deployment/InstanceSetup/ for
more details.
