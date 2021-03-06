

# Active Directory Authentication for Flash Array

These Playbooks will help you setup Active Directory Authentication, and configure AD roles to manage Flash Array


## Prerequisites

What things you need to install the software and how to install them

### CSV library for importing data

- ansible-role-includecsv [Library](https://github.com/mkouhei/ansible-role-includecsv)

### 4 user groups for each role for mapping to:

- array_admin
- ops_admin
- readonly
- storage_admin

### URI = ldap address of Global Catalog server
- ldap.server.domain

### Bind user/pass
- a domain account preferrably a service account

## Collect Active Directory information

You can open a powershell session from a windows server logged in with a domain account and 
execute the below command to obtain a list of the groups and the baseDN. 

```
PS C:\Users\aparsons.PURESTORINT> dsquery group -name *pure*
"CN=Pure Storage Sales,CN=Users,DC=purestorage,DC=int"
"CN=pureadmins,OU=pure,DC=purestorage,DC=int"
"CN=purereadonly,OU=pure,DC=purestorage,DC=int"
"CN=pureusers,OU=purestorage.int,DC=purestorage,DC=int"
"CN=purestorageadmin,OU=pure,DC=purestorage,DC=int"
"CN=pureopsadmin,OU=pure,DC=purestorage,DC=int"
``` 
Here the baseDN="DC=purestorage,DC=int"

## CSV setup


Each playbook pulls the array information from a CSV file
you need to input the array url/ip address, and the API token of each array to be modified. 

You can extract the api token using this CLI command when SSH to the array

```
pureadmin list --api-token --expose
```
csv file 
```
url,token
10.226.224.120,a2bc1b63-a4eb-35dd-846f-xxxxxxx
10.226.224.130,84f1db69-0d00-79fe-40ef-xxxxxxx
10.226.116.120,4ac5c338-a688-3c77-d6cb-xxxxxxx
10.226.224.110,aa009fd2-7686-7d48-8698-xxxxxxx
```
NOTE: the name of the CSV file is called in each playbook under 2 areas

1
```
tasks:
    - include_csv: src=adcred.csv #csvnamehere
```
2
```
     with_items: " {{ adcred }}"  #csvnamehere
```


## Create Roles

This playbook will bind your AD groups to Roles for management of the array.

You need to edit the playbook to correlate the role to an AD group. 

```
purefa_dsrole:
        state: present
        role: array_admin 
        group_base: "OU=pure"  #specify your AD OU here
        group: pureadmins   #specify your AD group here
        fa_url: "{{ item.url }}"
        api_token: "{{ item.token }}"
      with_items: " {{ adcred }}"
```
Execute the playbook to create roles for each array. 

```
ansible-playbook configureadrole.yaml

```

## Configure AD on each array

This playbook will configure AD auth on the array. 

you need to edit the playbook to correlate to your AD settings. 

```
      purefa_ds:
        enable: true
        state: present
        uri: "ldap://srv-ad02.purestorage.int"   #your LDAP server here
        base_dn: "DC=purestorage,DC=int"         #your base DN
        bind_user: purebind                      #your bind user
        bind_password: "password"                #your bind password
        fa_url: "{{ item.url }}"
        api_token: "{{ item.token }}"
      with_items: " {{ adcred }}"
```

Execute the playbook to configure and enable Active Directory Authentiation

```
ansible-playbook configuread.yaml
```

Authors

* **Andy Parsons** - - [opslounge](https://github.com/opslounge)

