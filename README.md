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
```
:connect TFSGFS4LN
Use [Tfs_GFS Service Mgmt]
select *  from tbl_SecurityAction
where NamespaceId = '52D39943-CB85-4D7F-8FA8-C6BAAC873819'
```
**OR to find the set bit value for Deny permission**
```
:connect TFSGFS4LN
Use [Tfs_GFS Service Mgmt]
select sum(Bit)  from tbl_SecurityAction
where NamespaceId = '52D39943-CB85-4D7F-8FA8-C6BAAC873819' and 
DisplayName in ('Bypass rules on work item updates','Change process of team project.','Create tag definition','Create test runs','Delete and restore work items','Delete team project','Delete test runs','Edit project-level information','Manage project properties','Manage test configurations','Manage test environments','Permanently delete work items','Rename team project','Suppress notifications for work item updates','Update project visibility')

```

**Some More**
```
select * from tbl_SecurityAccessControlEntry 
where NamespaceGuid='52D39943-CB85-4D7F-8FA8-C6BAAC873819' and SecurityToken = '$PROJECT:vstfs:///Classification/TeamProject/d5ca8447-9e11-479e-b6da-4aa00231bea9:'
```

```
select sum(Bit)  from tbl_SecurityAction
where NamespaceId = '52D39943-CB85-4D7F-8FA8-C6BAAC873819' and 
DisplayName in ('Bypass rules on work item updates','Change process of team project.','Create tag definition','Create test runs','Delete and restore work items','Delete team project','Delete test runs','Edit project-level information','Manage project properties','Manage test configurations','Manage test environments','Permanently delete work items','Rename team project','Suppress notifications for work item updates','Update project visibility')
```

```
select * from tbl_SecurityTokenDelta 
order by ChangeDate desc
```

```
SELECT [ProjectUri]
FROM [Tfs_GFS Service Mgmt].[dbo].[tbl_Project]
where projectname='GFS-CSIPRM'
--vstfs:///Classification/TeamProject/d5ca8447-9e11-479e-b6da-4aa00231bea9
```
```
SELECT *
FROM [Tfs_GFS Service Mgmt].[dbo].[tbl_Area]
Where ProjectUri = 'vstfs:///Classification/TeamProject/d5ca8447-9e11-479e-b6da-4aa00231bea9'
```
```
Project URI - 
SELECT [ProjectUri]
FROM [Tfs_GFS Service Mgmt].[dbo].[tbl_Project]
where projectname='GFS-CSIPRM'

--vstfs:///Classification/TeamProject/d5ca8447-9e11-479e-b6da-4aa00231bea9
```
Can be validated by checking the entry for the tfs  project(uri - vstfs:///Classification/TeamProject/d5ca8447-9e11-479e-b6da-4aa00231bea9 ) and namespace(Project - 52D39943-CB85-4D7F-8FA8-C6BAAC873819)
```
select * from tbl_SecurityAccessControlEntry 
where denypermission > 0 and NamespaceGuid='52D39943-CB85-4D7F-8FA8-C6BAAC873819' and SecurityToken = '$PROJECT:vstfs:///Classification/TeamProject/d5ca8447-9e11-479e-b6da-4aa00231bea9:'
```
Play around with the tfs_security_from_api.ipynb notebook.