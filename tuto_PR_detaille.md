# Documented Pull Request: an example
---
# Run the doodle student project

The following will help you make the application run and understand how to launch it.

## Some precision about the project
The project is decomposed into two parts: the front in js, and the back that makes different modules communicate together.
This document provides a quick way to run the application, but it will not deploy the complete application into several containers.
Only the back part is using containers here (Docker more specifically).
That is what we call for ourselves the "dev mode" since it is easier to get to the core code of the application
and see the impact of the changes that are made.
An other mode that implies to place our front in a container as well and make all of that communicate (with potentially a NGinx service as well) is also possible. It requires a little more work to get it done but it will be helpful in the some cases such as monitoring or chaos engineering.

## Trying to make the back up-and-running

The back is composed, initially, of three different services: a database, an etherpad, and a mail service.
You can see them by reading the docker-compose.yaml file.

Just run 
```sh
docker compose up
```
in a terminal to start the services.

###  Starting the front

The file README.md describes all the possible way to launch the front;
we will simply use a second terminal and use
```sh
npm start
```

## A first problem...
You should be able to visit localhost:4200 and see the presentation of the app (the front...)
As you navigate and use the application, you should get the following error popping on your terminal managing the front

[webpack-dev-server] [HPM] Error occurred while proxying request localhost:4200/api/polls to http://localhost:8080/ [ECONNREFUSED] (https://nodejs.org/api/errors.html#errors_common_system_errors)

yet, you launched the containers regarding the back, what happened?

There is one piece missing in the back, the module compiled thanks to quarkus, which is not part of the docker compose file.
This module is supposed to link the front and its requests to the different calls to the other services.
What happens is without this module, all the requests from the front are routed... to the front, which is not querying the database or anything; hence the error.
To solve this, you will have to open a new terminal and use the mvnw script.
If you directly try
```sh
./mvnw
```
you should get the following error: 

[ERROR] No goals have been specified for this build. You must specify a valid lifecycle phase or a goal in the format <plugin-prefix>:<goal> or <plugin-group-id>:<plugin-artifact-id>[:<plugin-version>]:<goal>.

one of the possible goal we want to launch here is quarkus:dev;
```sh
./mvnw quarkus:dev
```

### To sum up
You will need three terminals:
 - one for the front
 - one to launch the docker compose file
 - one for the API calls that hold everything together

The front is launched via npm start;
the docker compose is launched with the command docker compose up
The last part is launch with the mvnw script using for instance ./mvnw quarkus:dev

With all of that, you should get a functioning first, simple app.
Of course, you can add other services if you want.


