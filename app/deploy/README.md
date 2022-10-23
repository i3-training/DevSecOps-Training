# DevSecOps CI/CD

deployment prerequisites.

- [ ] Project / namespace for dev and prod
- [ ] Pull secret to the image
- [ ] database

## 1. Create Project/Namespace

this ci/cd pipeline require 2 project to be used for deployment, dev project will be used for test by zap then the application will be deployed in prod namespace.

```bash
oc new-project devsecops-dev
oc new-project devsecops-prod
```

## 2. image pull secret

image pull secret required since we will puling our image from our own private registry, to create pull secret run command below.

```bash
oc create secret docker-registry <pullsecret-name> \
    --docker-server=<image-registry-address> \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<user-email>
```

after pull secret is created now we need to link the secret to service account, so the service account can pull the image.

```bash
oc secrets link default <pullsecret-name> --for=pull
```

## 3. database

create new database to be used by the app

```bash
oc adm policy add-scc-to-user anyuid -z default
```

Deploy db on dev project

```bash
oc new-app --template=mariadb-ephemeral \
-p MYSQL_USER=user -p MYSQL_PASSWORD=pass \
-p MYSQL_ROOT_PASSWORD=root -p MYSQL_DATABASE=a7db \
-p MEMORY_LIMIT=256Mi -p DATABASE_SERVICE_NAME=mariadb-dev
```

Deploy db on prod project

```bash
oc new-app --template=mariadb-ephemeral \
-p MYSQL_USER=user -p MYSQL_PASSWORD=pass \
-p MYSQL_ROOT_PASSWORD=root -p MYSQL_DATABASE=a7db \
-p MEMORY_LIMIT=256Mi -p DATABASE_SERVICE_NAME=mariadb-prod
```

Create a user inside database a7db for zap can use it for scanning inside the application.
