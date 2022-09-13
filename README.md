# python3-flask-app
 Dockerize a python 3 application
 
 
# How to modify app.py
Let's add the following lines of code to our app.py:

from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def hello_world():
    return render_template('index.html')

if __name__ == '__main__':
    app.run(debug=True,host='0.0.0.0')

Now, if we run python app.py on the command line to test our Flask app, we should get results similar to the ones shown below:

 * Serving Flask app 'app' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 316-584-348

How to modify the Dockerfile
# syntax=docker/dockerfile:1
# init a base image (Alpine is small Linux distro)

FROM python:3.6.1-alpine
# update pip to minimize dependency errors 
RUN pip install --upgrade pip
# define the present working directory
WORKDIR /docker-flask-test
# copy the contents into the working dir
ADD . /docker-flask-test
# run pip to install the dependencies of the flask app
RUN pip install -r requirements.txt
# define the command to start the container
CMD ["python","app.py"]

Before we build an image for the application we just created, let's first understand what the lines of code in the Docker file above mean and what role they play.

The below code should be the first line of every Dockerfile – it tells the Docker builder what syntax to use while parsing the Dockerfile and the location of the Docker syntax file.

# syntax=docker/dockerfile:1
While it is possible to create our own base images, there is no need to go that far because Docker allows us to inherit existing images. The following line tells Docker which base image to use — in this case, a Python image.

FROM python:3.6.1-alpine
To keep things organized, we also tell Docker which folder to use for the rest of the operations, so we use a relative path as shown below.

In this case, we're telling Docker to use the same directory and name for the rest of its operations — it's a directory contained within our container image.

WORKDIR /docker-flask-test

In the fourth and fifth lines, we tell Docker to copy the contents of our requirements.txt file into the container image's requirements.txt file. Then run pip install to install all the dependencies in the same file to be used by the image.

ADD . /docker-flask-test
RUN pip3 install -r requirements.txt
Continuing with the copying, we now copy the remainder of the files in our local working directory to the directory in the docker image.

COPY . .
docker-illustration
Our image so far has all of the files that are similar to those in our local working directory. Our next task is to assist Docker in understanding how to run this image inside a container.

This line specifically instructs Docker to run our Flask app. Then it instructs Docker to make the container available externally, such as from our browser, rather than just from within the container. We pass the host port:

CMD ["python","app.py"]
Since we had,

`if __name__ == "__main__":
    app.run(debug=True)`
    
Because we had the "if" statement in our application file, this will be true if we run this module as a standalone program. As a result, it can function as a module imported by another program or as a standalone program, but it will only execute the code in the if statement if run as a program.

# How to Build a Docker Image
After that, all that remains is to build our image. Using docker build, we can now enlist Docker's help in building the image. You can combine the build command with other tags, such as the "--tag" flag, to specify the image name.

docker build --tag python-docker .

# How to run an image as a container
Running an image inside a container is as simple as building one. But before we do so, we'd like to see what other images are available in our environment. To view images from the command line, execute the following:

`docker images`
If the above command finds any images, the output should look something like this:

`REPOSITORY      TAG       IMAGE ID       CREATED             SIZE
python          3.9.2     587b1bc803b3   7 months ago        885MB`

Now we can choose which image to run. Using the docker run command, we can run an image by passing the image's name as a parameter.

docker run
While running the above command, you'll notice that on the command line it indicates that the application is running. But when you enter http://localhost:5000/ on the browser, the greeting will be:

This site can’t be reached localhost refused to connect.
Regardless of whether the container is running, it is doing so in isolation mode and cannot connect to localhost:5000.

The best solution is to run the image in detached mode. Because we need to view this application in the browser rather than the container, we'll modify our docker run and add two additional tags: "-d" to run it in detached mode and "-p" to specify the port to be exposed.

The docker run command will now be formatted as follows:

docker run -d -p 5000:5000 python-docker
This time, we'll see the following output if we run it in detached mode and visit localhost at port 5000:

d
We can use the following command to see which containers are currently running:

docker ps
The output is as follows:

`CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS                    NAMES`

To stop the currently running container, we execute this command:

`docker stop <container-name>`
 
Another useful command to have when working with Docker is this one:

`docker container prune`
 
It removes unused resources, freeing up space and keeping your system clean.

And that's it!
