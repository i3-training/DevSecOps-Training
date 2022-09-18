# Push and Pull docker image from private registry

## Configure Nexus repo

### 1 Adding new user

For security concern we won't allow anonymous access to our repo because of this we need to create user to give user in our organization access to the registry, gif below showing how to create a user in nexus.

![create user](../images/nexus-createuser.gif)

First you need to login in web console then open server administration and configuration, on the side bar click user then select Create local user, fill the form then click Create local user.

### 2 Create docker repository

#### 2.1 Create new Blob storage

Before create a new repository its a good idea to create a new blob storage to separate data between repository, in this case we will create a new blob storage to store our images, to create a new blob storage follow steps below.

![create blob](../images/nexus-blob.gif)

Login to your nexus web console then open server administration and configuration, on the side bar click Blob stores then select Create Blob Store to create new blob storage, first select type as an file since we will using local storage, then give your blob a name and specify your blob location, you can also create Quota for your blob here after that save the change and new blob storage is created and ready to be use by an repository.

#### 2.2 Create Repo

### 3 Setting up local machine
