---
layout: page
title: "How to: use basic Docker commands"
date: 2018-10-19
---

To make a minimal Docker app, we need three files: a Dockerfile, app.py, and requirements.txt. Here is the minimal dockerfile as given in the official tutorial:

	# The app contains three files: this Dockerfile, app.py, and requirements.txt
	# When the Dockerfile builds into an image, app.py and requirements.txt are copied 
	# into the /app subfolder. 
	# Output from app.py is accessible through EXPOSE 80. 
	# Use an official Python runtime as a parent image
	FROM python:2.7-slim
	# Set the working directory to /app
	WORKDIR /app
	# Copy the current directory contents into the container at /app
	COPY . /app
	# Install any needed packages specified in requirements.txt
	RUN pip install --trusted-host pypi.python.org -r requirements.txt
	# Make port 80 available to the world outside this container
	EXPOSE 80
	# Define environment variable
	ENV NAME world
	# Run app.py when the container launches
	CMD ["python", "app.py"]

Once the directory is created, here are some useful commands:

	docker build -t friendlyhello # Create image using this directory's Dockerfile and name it 'friendlyhello'
	docker run -p 4000:80 friendlyhello # Run image, mapping machine port 4000 to output port 80

To upload, first login, then tag:

	docker login
	docker tag <image> username/repository:tag

Then push:

	docker push username/repository:tag

To run from registry:

	docker run username/repository:tag

To list containers and images:

	docker container ls -a
	docker image ls -a

