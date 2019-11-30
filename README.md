# MicroserviceDemo
A simple Asp.Net Core API project with EF Core and Sql server, running in a Docker container

## Prerequisites

### Docker setup
- Ensure you have Docker desktop installed (https://www.docker.com/products/docker-desktop)
- Switch to Windows containers from the Docker taskbar icon (If you see the option "Switch to Linux containers", that means it's already set to Windows containers so skip this step)
- Get SQL Server container image. This project uses Sql Server 2017 Developer edition, run the below command to pull the image from docker hub, this is around 4.5 gigs download so it may take a while
```
> docker pull microsoft/mssql-server-windows-developer:2017-latest
```
- You are ready to run the app in your local environment.

### How to run the app
- To run using Docker commands, goto the folder in the project which has 'docker-compose.yml' file and run the below commands - 
```
> docker-compose build
```
Once the build is successful - 
```
> docker-compose up
```
The above command starts the app, it runs EF migrations if it's running for the first time. You may see some errors at first, but it retries again once or twice and it should finally say "Application started". Now you can use postman or similar tools to test the API endpoints.

- Alternatively you can run it from Visual Studio, but I have had some trouble sometimes running with VS. Ensure you're running under "Docker Compose" and not "IIS Express" if you're running under Visual Studio (The project has been created using Visual Studio 2019)

## Useful info
### Test your SQL server image
- You can test your SQL server image by starting it using the below command, "test-sql" is the container name
```
>  docker run -d -p 1433:1433 --name test-sql -e 'SA_PASSWORD=1Secure*Password1' -e ACCEPT_EULA=Y microsoft/mssql-server-windows-developer:2017-latest
```

**Note** Ensure your password is complex enough like the one provided in above example. It's a bug with the current sql server docker image where the above command always passes the password complexity validation but still doesn't allow the user to login with the credentials provided. (More info here -  [Why I can't login SA with docker
](https://github.com/Microsoft/mssql-docker/issues/315)

- Test if you can login to the database from management studio or sqlcmd using the IP address of the SQL server and your credentials. You can get the IP address by running the below command - 
```
> docker inspect --format '{{.NetworkSettings.Networks.nat.IPAddress}}' test-sql
```
- If everything's fine, discard the test-sql container. Run the below command to stop and remove the container image
```
> docker container stop test-sql
> docker container rm test-sql
```

### About docker-compose.yml
- The compose file is where you define all the containers that your app requires to run. In this app, it requires 2 containers
1. microservicedemo
2. db-server
- The compose file in this app also defines 
1. the network name (within docker environment) under which both the container runs in
2. the ports for the api and the database. 

For example, the db-server ports are defined as "1400:1433", 1400 is the port with which the host computer (outside of docker) can access the db, you can open the db in management studio with "localhost,1400" as the server name and the credentials (defined in docker-compose.yml). But for the app however (i.e., within docker environment), the db is accessed with Server=db-server on port 1433. Take a look inside appsettings.json and docker-compose.yml

### Troubleshooting
- Check if all the containers are running, you should see both microservicedemo and db-server running
```
> docker ps
```
- To stop, remove the process and restart your app - 
```
> docker container stop <CONTAINER_NAME>
> docker container rm <CONTAINER_NAME>

> docker-compose build
> docker-compose up
```
Alternatively - 
```
> docker-compose down
> docker-composer build
> docker-composer up
```
