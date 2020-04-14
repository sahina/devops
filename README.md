# Devops

Misc devops scripts.

## Ansible Roles

Roles and playbooks for ansible playbooks.


## Node Setup

Rename file `inventory-example.yml` to `inventory.yml`

Prepare nodes in your inventory to be managed by ansible.

**On all nodes:**

- create `ansible` user with a password of your choosing.

```
sudo adduser ansible
sudo usermod -aG sudo ansible
echo "ansible  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
```

**On controller (i.e. your laptop):**

Copy ssh key to all nodes. Ansible playbooks will use this key connect to all nodes.

```
su - ansible; ssh-keygen
ssh-copy-id -i ~/.ssh/id_ansible_rsa ansible
ssh-copy-id -i ~/.ssh/id_ansible_rsa ansible
```

## Postgres Playbook

Download the role to local roles folder. We'll override some values from the downloaded role.

```
# step 1 -  download geerlingguy.postgresql from ansible galaxy
ansible-galaxy install geerlingguy.postgresql -p ./roles

# step 2 - update vars `var/main.yml`. available vars are in `geerlingguy.postgres/defaults/main.yml`

# step 3 - run the postgres playbook
ansible-playbook postgres.yml

# step 4 - connect to postgres in pg1
root@pg1:~#  psql --host=localhost --dbname=testdb -U testdb_user
```

Postgres will not be accessible remotely, only from localhost by default. Enable remote access:

```
# edit postgresql.conf
find / -name "postgresql.conf"

# change from:
listen_addresses = 'localhost'

# to all as below or put specific IP address list.
listen_addresses = '*'

# update pg_hba.conf
find / -name "pg_hba.conf"

# add these lines to pg_hba.conf
host    all             all              0.0.0.0/0                       md5
host    all             all              ::/0                            md5

# restart postgresql server
service postgresql restart
netstat -nlt
```

Ref: `https://blog.bigbinary.com/2016/01/23/configure-postgresql-to-allow-remote-connection.html`


## Webserver playbook

Download asdf role to local roles folder.

`ansible-galaxy install -p roles cimon-io.asdf`
