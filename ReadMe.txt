Create a file 'Dockerfile'

	FROM maven:3.8.6-openjdk-18-slim AS build		//Base image => Maven and OpenJDK 18		
	WORKDIR /home/app					//Setting up working directory => which is container directory	

	COPY ./pom.xml /home/app/pom.xml			//Copying pom.xml to container directory
	COPY ./src/main/java/com/restapi/insurancerestapi/InsuranceRestapiApplication.java	/home/app/src/main/java/com/restapi/insurancerestapi/InsuranceRestapiApplication.java		//copying main src file to container directory

	RUN mvn -f /home/app/pom.xml clean package	//build the application using the pom.xml file from container directory	
							//resulting JAR file will be placed in the target directory
	COPY . /home/app					
	RUN mvn -f /home/app/pom.xml clean package

	FROM openjdk:18.0-slim					//Base image => OpenJDK 18
	EXPOSE 9000						//Exposing port number 
	COPY --from=build /home/app/target/*.jar app.jar	//copies the JAR file from the previous build stage to the container's root directory	
	ENTRYPOINT [ "sh", "-c", "java -jar /app.jar" ]		//sets the entry point to run the command java -jar /app.jar when the container starts	

Docker image build:
	Open cmd in the directory where Dockerfile is present,

	docker build -t imageName:v1 .			//Building an image with tag(version 'v1')   // '.' is build contextwhere dockerfile directory and other files located)

Docker image list:
	docker image ls

Docker image delete:
	docker rmi imageName:v1
	docker rmi -f imageName:v1

Docker container/image run:
	docker run -d -p 9000:9000 imageName:v1		//Create a new container and run the docker container in detached mode with port 9000

Docker container list:
	docker container ls

Docker container stop:
	docker stop <ContainerID>

Using maven plugins(No Dockerfile needed):
	Go to maven panel -> plugins -> spring:boot -> spring-boot:build-image

	To run this image:
	docker container run -d -p 9000:9000 imageName:0.0.1-SNAPSHOT		//0.0.1-SNAPSHOT is from pom.xml

Docker image save:
	create a folder 'dockerimages' => mkdir dockerimages,

 	docker image save imageName -o dockerimages/imageName.tar

Docker image load:
	docker image load -i dockerimages/imageName.tar
