# Test Gorilla Home Assignment

## Table of Contents

1. [Description](#description)
2. [How to run](#how-to-run)
3. [Data and Logic](#data-and-logic)
4. [DB Visualization](#db-visualization)
5. [Tests](#tests)
6. [Improvements](#improvements)
6. [Appreciation](#appreciation)


## Description

This is a project for Home Assignment test, a step on Test Gorilla hiring process. The project is a simple Django application that uses a NoSQL database (MongoDB) to store its models.


## How to run
This projects was developed using Python with Django framework, and Docker, also its compose tool for aggregating contatiners. To run it you must have docker and docker-compose both installed in your machine. The steps to install Docker and its compose tool are wrote down below:
1. First you need to [install docker](https://docs.docker.com/get-docker/):

2. Check if Docker is installed, by simply open a terminal and running:
```shell
docker --version
```

3. Then install the compose:
- Using Curl
```shell
curl -L https://github.com/docker/compose/releases/download/1.8.0-rc2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

- Using pip
```shell
pip install docker-compose
```

4. Check if docker-compose is installed, by simply open a terminal and running:
```shell
docker-compose --version
```

After having Docker and Docker Compose installed you are good to run the project.
It has 3 environment:
- Development: Which runs both the database and the project, and also Mongo Express wich is a GUI (Graphical User Interface) to visualize and manage the mongo collections and databases;
- Deployment: This basically is the development environment without the Mongo Express;
- Testing: This one will only runs the tests and provides a HMTL page for checking its results and coverage. Instead of a real Mongo DB it has a Mock library of that, called `mongomock`.

To start a environment you will need to run some Docker Compose commands. As it has 3 environments, 3 differents docker-compose files were create to architect and run the services following the above environment specifications. They are located in the root folder, named by `docker-compose` and its variations:  
`docker-compose.yml, docker-compose-dev.yml, docker-compose-test.yml`

To run an environment, first identify the corresponding file for that environment, and do:
```shell
# Build the service images
docker-compose -f DOCKER_FILE build
```

```shell
# Start the services in detached (background) mode
docker-compose -f DOCKER_FILE up -d 
```

After run the above commands and stop receiving its output you gonna have the services running. You can checkout the sevices lit by running:
```shell
docker-compose -f DOCKER_FILE ps
```

Something like that will be prompted to you
```shell
             Name                            Command               State                      Ports
-----------------------------------------------------------------------------------------------------------------------
home-test_mongo-db_1              docker-entrypoint.sh mongod      Up      0.0.0.0:27017->27017/tcp,:::27017->27017/tcp
home-test_mongo-express_1         tini -- /docker-entrypoint ...   Up      0.0.0.0:8081->8081/tcp,:::8081->8081/tcp
home-test_test-gorilla-django_1   sh ./start.sh                    Up      0.0.0.0:8080->8080/tcp,:::8080->8080/tcp
```

As you can see you gonna have some services running, depending on each docker file you choosed. The last column has the ports that you can access to reach each service. To use any service just point to localhost - or 0.0.0.0 - and the respective service port, e.g. http://localhost:8080 .


## Data and Logic
For this assignment it was asked to create and list a Test example model for the Test Gorilla platform.
The test model has the following structure:

```json
"Test": { // Document
    "name": "string",
    "summary": "string",
    "type": "int",
    "duration": "int", // in seconds
    "level": "string",
    "audience": "string",
    "language": "string",
    "skills": "List[string]",
    "questions": "List[Question]"
}

"Question" : { // Embedded Document
    "text": "string",
    "answers": "List[Answer]",
    "correctAnswers": "List[UUID]", // List of Answer ids
    "userAnswer": "List[UUID]", // List of Answer ids
    "order": "int" // To be displayed in the Test flux page
}

"Answer": { // Embedded Document
    "id": "UUID",
    "text": "string"
}
```

With this models it is possible to manage all the custom questions types that Test Gorilla has:
- Multiple-choice type:  
In this case we gonna have questions with a list of a possible **answers**, and the **correctAnswers** attribute will only have a single **answer** id, representing the Id of the correct answer option.
- Multiple-response type:  
Pretty similar to the above type (Multiple-choice) but now having more than one Id on the **correctAnswers** attribute
- True or false:  
In this case we gonna have only two **answers** (True or False) and the **correctAnswers** attribute will only have one of the **answers** Id, ether True or False. Almost the same as Multiple-choice type.
- Short text:  
Different from the others, for this type we gonna have the **answers** attribute empty and in the **correctAnswers** one we will have all the match possibilities to validate the inputted answer

The user answer (**userAnswer**) is validated according to the **correctAnswers** attribute.


## DB Visualization
In the development environment we can visualize the data we have in the database in a user friendly way. To achieve it, after running the development environment, by starting the services using the `docker-compose-dev.yml` file, simply open your prefered browser and type `localhost:8081` - or you can just [click here](http://localhost:8081).

And you will have a page like this:
![Mongo Express Home Page](./doc/mongo-express-home.png)

Acessing our database (test-data), by clickin on the blue link typed `test-data` or in the green button next to it, you can visualize the collections within that DB like this:
![Mongo Express DB Page](./doc/mongo-express-db-view.png)

To view a specific collection click on its name or in the green button in the beginning of the collection line. You will see something like this:
![Mongo Express Collection Page](./doc/mongo-express-collection-view.png)

It is not only restricted to visualization, you can definetelly CRUD (create, read, update and delete) the interely database and its collections.


## Tests
As mentioned priorly, this projects has a test environment that runs the unit tests (actually it only has one, just for demonstration) and outputs its results and coverage on a HTML page. To have it you only need to `up` the test service/environment by running the docker compose with the `docker-compose-test.yml` file.

After running it some files will appear in the `coverage` folder located in the root folder of this project, just open the `index.html` file inside it.

You will have something like this:
![Test coverage](./doc/test-coverage.png)


## Improvements
This is a simple project for showing some of my code skills, and I wish I would have coded more features and showed a little bit more of what I can do. Below are some features and aspects that I would like to have done in this project:
- Improve comments:  
Not all methods are commented 😕
- Create env file having all env variables, instead of puting it raw in docker-compose file:  
Makes docker-compose files more clean and separete all the environment variables in a specific folder
- Custom Exception classes:  
Makes it better to validade and check errors and exceptions
- Logging for code and DB commands:  
To improve excution follow up and analyze fluxes
- Cache controll:  
Python mongoengine library has a cache enabled by default, a good feature is controlling it by cache-headers in the request
- Get CSRF Token from cookie:  
Instead of avoiding CSRF Token check get it from the response cookie
- Separeted user answer:  
To improve collection reusability, so it is not necessary to duplicate Question collection - and maybe the Test.


## Docker and Docker compose useful commands
These are commands that helped me to run, re-run, and manage the project containers:   

```shell
docker run -d --name some-mongo -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=pwd -p 27017:27017 mongo
docker exec -it CONTAINER_ID /bin/bash
docker-compose -f docker-compose-dev.yml build --no-cache SERVICE
docker-compose -f docker-compose-dev.yml up -d --remove-orphans
docker-compose -f docker-compose-dev.yml logs -f
docker-compose -f docker-compose-dev.yml ps
docker-compose -f docker-compose-dev.yml stop SERVICE
docker-compose -f docker-compose-dev.yml rm
docker-compose -f docker-compose-dev.yml down
```


no_cache flag on requests
    find a way to make it generic for any request
    https://docs.mongoengine.org/guide/querying.html?highlight=cache#querying-the-database

validation with factory and strategy
    https://docs.mongoengine.org/guide/validation.html

mongomock for tests
    https://docs.mongoengine.org/guide/mongomock.html

add log for mongo commands
    https://docs.mongoengine.org/guide/logging-monitoring.html




## Appreciation
I would like to thank Otto and Nicolás for the opportunity and the time they gave to me for this whole process 😃
