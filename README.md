# Clone azure-devops-python api to local to check the code

https://github.com/microsoft/azure-devops-python-api

Readme on this repo gives some idea for connecting the appropriate client and hence deserves a quick glance :).
PAT token is required for client connection. Create a PAT token from the tfs collection as required. [Reference](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page)

# Explore TFS Security from backend SQL Server using T-SQL

**Set Deny bits for these**
* Bypass rules on work item updates
* Change process of team project.
* Create tag definition
* Create test runs
* Delete and restore work items
* Delete team project
* Delete test runs
* Edit project-level information
* Manage project properties
* Manage test configurations
* Manage test environments
* Permanently delete work items
* Rename team project
* Suppress notifications for work item updates
* Update project visibility

**Set Allow bits for these**
* View project-level information
* View test runs

**Find bits in tbl_SecurityAction**
```SQL
:connect SQL_Instance
Use [Collection Database]
select *  from tbl_SecurityAction
where NamespaceId = 'Namespace ID'
```
**Find Namespace ID of Project from tbl_SecurityNamespace**
```SQL
select * from tbl_SecurityNamespace
```

**OR to find the set bit value for Deny permission**
```SQL
:connect SQL_Instance
Use [Collection Database]
select sum(Bit)  from tbl_SecurityAction
where NamespaceId = 'Namespace ID' and 
DisplayName in ('Bypass rules on work item updates','Change process of team project.','Create tag definition','Create test runs','Delete and restore work items','Delete team project','Delete test runs','Edit project-level information','Manage project properties','Manage test configurations','Manage test environments','Permanently delete work items','Rename team project','Suppress notifications for work item updates','Update project visibility')

```

**Some More**
```SQL
select * from tbl_SecurityAccessControlEntry 
where NamespaceGuid='Namespace ID' and SecurityToken = '$PROJECT:ProjectURI:'
```

```SQL
select sum(Bit)  from tbl_SecurityAction
where NamespaceId = 'Namespace ID' and 
DisplayName in ('Bypass rules on work item updates','Change process of team project.','Create tag definition','Create test runs','Delete and restore work items','Delete team project','Delete test runs','Edit project-level information','Manage project properties','Manage test configurations','Manage test environments','Permanently delete work items','Rename team project','Suppress notifications for work item updates','Update project visibility')
```

```SQL
select * from tbl_SecurityTokenDelta 
order by ChangeDate desc
```

```SQL
SELECT [ProjectUri]
FROM [Collection Database].[dbo].[tbl_Project]
where projectname='GFS-CSIPRM'
```
ProjectURI collected above is required to create a security token. For example, the security token for Project namespace is given by

Can be validated by checking the entry for the tfs project and namespace
```SQL
select * from tbl_SecurityAccessControlEntry 
where denypermission > 0 and NamespaceGuid='Namespace ID' and SecurityToken = '$PROJECT:ProjectURI:'
```
Play around with the tfs_security_from_api.ipynb notebook.