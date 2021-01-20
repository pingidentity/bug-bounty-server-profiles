# Ping Identity BugBounty Setup
Docker compose configuration for quick deployment and testing of the Ping stack.

## Prerequisites
* [Docker](https://docs.docker.com/install/)
* [Docker Compose](https://docs.docker.com/compose/install/) (included with Docker Desktop on Mac and Windows)
* DevOps credentials to obtain licenses

## Setup
1. Rename `env.example` to `.env` and replace `<USER_EMAIL>` `<USER_KEY>` with your credentials
2. From a directory where the `docker-compose.yaml` file is located run `docker-compose up -d`
3. Monitor containers status with `docker-compose ps`
4. Verify all containers are in a healthy state:
```
Name                     Command                  State                                               Ports
-----------------------------------------------------------------------------------------------------------------------------------------------------------
pingaccess-bb        ./bootstrap.sh wait-for pi ...   Up (healthy)   3000/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:9000->9000/tcp
pingdataconsole-bb   ./bootstrap.sh start-server      Up (healthy)   0.0.0.0:8443->8443/tcp
pingdelegator-bb     ./bootstrap.sh start-server      Up (healthy)   0.0.0.0:6443->6443/tcp
pingdirectory-bb     ./bootstrap.sh start-server      Up (healthy)   0.0.0.0:1389->389/tcp, 0.0.0.0:1443->443/tcp, 5005/tcp, 0.0.0.0:1636->636/tcp, 689/tcp
pingfederate-bb      ./bootstrap.sh wait-for pi ...   Up (healthy)   0.0.0.0:9031->9031/tcp, 0.0.0.0:9999->9999/tcp
```
5. Log in to the management consoles for the products:

   * Ping Data Console for PingDirectory
     - Console URL: `https://localhost:8443/console`
     - Server: pingdirectory
     - User: Administrator
     - Password: 2FederateM0re

   * PingFederate
     - Console URL: `https://localhost:9999/pingfederate/app`
     - User: Administrator
     - Password: 2FederateM0re

   * PingAccess
     - Console URL: `https://localhost:9000`
     - User: Administrator
     - Password: 2FederateM0re
   * Sample App through PingAccess
     - httpbin.org: `https://localhost:3000`

   * PingDelegator
     - URL: `https://localhost:6443`
     - User: administrator
     - Password: 2FederateM0re
     - PingFederate OIDC

   * Apache Directory Studio for PingDirectory
     - LDAP Port: 1636
     - LDAP BaseDN: dc=example,dc=com
     - Root Username: cn=administrator
     - Root Password: 2FederateM0re
     - HTTPS Port: 1443
     - HTTPS extensions endpoints
     ```log
      Available or Degraded State : https://localhost:1443/available-or-degraded-state
      Available State             : https://localhost:1443/available-state
      Configuration               : https://localhost:1443/config/*
      Consent                     : https://localhost:1443/consent/v1/*
      Delegated Admin             : https://localhost:1443/dadmin/v2/*
      Directory REST API          : https://localhost:1443/directory/v1/*
      Instance Root File          : https://localhost:1443/instance-root/*
      SCIM2                       : https://localhost:1443/scim/v2/*
      ```

6. Stop all containers with `docker-compose down` when done testing

## Troubleshooting
1. Check full logs with `docker-compose logs -f ` or for a single product with `docker compose logs -f pingaccess|pingdirectory|pingfederate`
2. Consult the troubleshooting [Guide](https://devops.pingidentity.com/reference/troubleshooting/)
3. Delete volumes `docker volume rm $(docker volume ls -q)`
4. Update images with `docker-compose pull` and restart with `docker-compose up --build --force-recreate -d`

### Errors
```
ERROR: for pingdelegator  Cannot start service pingdelegator: driver failed programming external connectivity on endpoint pingdelegator-bb (025e96555814d12caae4e79dc61c03182dba525f05d8c03deb678393e3fda3ac): Error starting userland proxy: listen tcp 0.0.0.0:6443: bind: address already in use
ERROR: Encountered errors while bringing up the project.
```
The Delegator application is running on the port 6443. Unfortunately, there is no simple way to change the port through environment variables. Disabling other services that use the same port solves the issue. **Kubernetes API** service is running on 6443.

## Hints
### Container's anatomy
Containers' internal structure is described in the [Guide](https://devops.pingidentity.com/reference/config/#imagecontainer-anatomy)

### SH into a container
You can shell into containers to inspect the file structure, view log files, make configuration changes with `docker exec -it pingdirectory-bb /bin/sh`. Use the `container_name` attribute value from the compose file as a target.
