# Ping Identity BugBounty Setup
Docker compose configuration for a PA-PF-PD stack for quick deployment and testing.

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

   * Apache Directory Studio for PingDirectory
     - LDAP Port: 1636
     - LDAP BaseDN: dc=example,dc=com
     - Root Username: cn=administrator
     - Root Password: 2FederateM0re
6. Stop all containers `docker-compose down` when you done testing

## Troubleshooting
1. Check full logs with `docker-compose logs -f ` or for a single product with `docker compose logs -f pingaccess|pingdirectory|pingfederate`
2. Consult the troubleshooting [Guide](https://github.com/pingidentity/pingidentity-devops-getting-started/blob/master/docs/troubleshooting.md)
