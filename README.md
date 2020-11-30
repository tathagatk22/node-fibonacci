# Sample Node Application

## Problem Statement:

Create a sample NodeJs application to view fibonacci number for a particular number.

### Steps to start the application

To initialize the application we need to install Node.js, and perform these below steps,

```
$ npm install # To install the required packages

$ npm start # To start the application
```

This Node.js Web application can perform operations such as,


1. Performing Fibonacci for the number

    Example:
    
    ```
   /fibonacci?value=15
    ```

## Containerization

This application can be containerize using Dockerfile present in the repository.

```
docker build -t <image name> .
```

This above command will create a new Docker Image which therefore can be used in Docker Environment as well as any other Container Orchestration Environment.

Need to remove
