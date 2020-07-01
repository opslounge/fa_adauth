

# Active Directory Authentication for Flash Array

These Playbooks will help you setup Active Directory Authentication, and configure AD roles to manage Flash Array


### Prerequisites

What things you need to install the software and how to install them


 4 user groups for each role for mapping to:
- array_admin
- ops_admin
- readonly
- storage_admin

 URI = ldap address of Global Catalog server
- ldap.server.domain

 Bind user/pass
- a domain account preferrably a service account


### Get Jupyterhub

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



Add/update the helm repo for JupyterHub
