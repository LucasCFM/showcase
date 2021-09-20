# Test Gorilla Home Assignment

This is the **Home Assignment** test project, a step in the **Test Gorilla** hiring process. The main technologies used in this project are:

- Python
- Django
- MongoDB

## Table of Contents

2. [How to run](#how-to-run)
3. [Data and Logic](#data-and-logic)
4. [DB Visualization](#db-visualization)
5. [Tests](#tests)
6. [Improvements](#improvements)
6. [Appreciation](#appreciation)


## How to run
This project was implemented with the help of Docker and Docker Compose. To run it you must have docker and docker-compose both installed in your machine. The steps to install them can be found below:

1. First you need to [install docker](https://docs.docker.com/get-docker/):

2. Check if Docker is installed by simply typing the following command on the terminal:
```shell
docker --version
```

3. Then install docker-compose:
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

_Note: newer versions of Docker might already include docker-compose._

After making sure Docker and Docker Compose are installed you can run the project that is composed of 3 environments:

- Development:Run both the database and the project, and also Mongo Express wich is a GUI (Graphical User Interface) to visualize and manage the mongo collections and databases;
- Deployment: Same as the Development environment but without running Mongo Express;
- Testing: Run unit|integration tests and generate a test coverage report in HMTL. `mongomock` was used to mock the interaction between the application and MongoDB.

To start the application, please run the following commands keeping in mind the different build environments. Each `docker-compose` file can be found in the root folder and must replace the key `DOCKER_FILE` below. Docker Compose files:

- `docker-compose.yml`
- `docker-compose-dev.yml` 
- `docker-compose-test.yml`

```shell
# Build the service images
docker-compose -f {DOCKER_FILE} build
```

```shell
# Start the services in detached (background) mode
docker-compose -f DOCKER_FILE up -d 
```

After running the above commands, the application should be up. You can check the current status by typing:
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

The last column has the ports that you can access to reach each service. To use any service just point to localhost - or 0.0.0.0 - and the respective service port, e.g. http://localhost:8080 .


## Data and Logic
For this assignment it was asked to create and list a Test example model for the **Test Gorilla** platform.
The test model has the following structure:

```js
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

With these models, it's possible to manage all the custom question types that **Test Gorilla** has:
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
In the development environment, we can visualize the data in a user-friendly way. After running the development environment, simply open your preferred browser and type `localhost:8081` - or you can just [click here](http://localhost:8081).

You will open a page similar to the following:
![Mongo Express Home Page](./doc/mongo-express-home.png)

Access the database (test-data) by clicking on the blue link named `test-data` or in the green button next to it, you can visualize the collections within that DB as follows:
![Mongo Express DB Page](./doc/mongo-express-db-view.png)

To view a specific collection, click on its name or the green button. You should see something like this page:
![Mongo Express Collection Page](./doc/mongo-express-collection-view.png)

It's not restricted only to visualization, feel free to also CRUD (create, read, update and delete) any collection, if needed.


## Tests
As previously mentioned, this project has a test environment that runs the unit tests (actually it only has one, just for demonstration) and outputs its results and coverage in a HTML page. To have it you only need to `up` the test service/environment by running the docker compose with the `docker-compose-test.yml` file.

After executing the command, a few files should appear in the `coverage` folder located in the root level of this project, go ahead and open the `index.html` file to see the test coverage report that should look like the following.

![Test coverage](./doc/test-coverage.png)


## Improvements
This is a simple project to show you my technical skills, and I wish I had more time to implement more features to re-enforce how important this project and the possibility of joining the company is to my carreer. Below you can find a few features that I had in mind:

- Improve comments:  
Not all methods are commented 😕
- Create env file having all env variables, instead of puting it raw in docker-compose file:  
Make docker-compose files cleaner and separate all the environment variables in a specific folder
- Custom Exception classes:  
Make it better to validate and check errors and exceptions
- Logging for code and DB commands:  
To improve excution follow up and analyze flows
- Cache control:  
Python mongoengine library has a cache enabled by default, a nice-to-have feature is the ability to control it via `cache-headers` in the request
- Get CSRF Token from cookie:  
Instead of avoiding CSRF Token check get it from the response cookie
- Separeted user answer:  
To improve collection reusability duplicate duplicate Question collection - and maybe the Test.


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


## Final Thoughts
I would like to thank Otto and Nicolás for the opportunity and the time they gave me during the entire process. I truly hope I was able to deliver a project you were expecting and I look forward to working with you if approved.
