
1. Deploy a demo application in namespace called “namespace-a”

* kubectl config set-context --current --namespace=namespace-a
* Verify that the NGINX Ingress controller is running

## kubectl get pods -n ingress-nginx
Output:

```shell
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-2tgrf        0/1     Completed   0          3m28s
ingress-nginx-admission-patch-68b98         0/1     Completed   0          3m28s
ingress-nginx-controller-59b45fb494-lzmw2   1/1     Running     0          3m28s

* Create a Deployment

##  kubectl create deployment web --image=gcr.io/google-samples/hello-app:1.0
* Expose the Deployment:
## kubectl expose deployment web --type=NodePort --port=8080

* Verify the Service is created and is available on a node port:

## kubectl get service web

## Create an Ingress resource ##

Create example-ingress.yaml from the following file:

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - host: hello-world.info
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web
                port:
                  number: 8080

* Create the Ingress resource by running the following command:

## kubectl apply -f https://k8s.io/examples/service/networking/example-ingress.yaml

* Verify the IP address is set:

## kubectl get ingress

* Verify that the Ingress controller is directing traffic: 

curl hello-world.info




=======================================

2. Deploy another demo application in other namespace called “namespace-b”

kubectl config set-context --current --namespace=namespace-b
Create Second Deployment 
## kubectl create deployment web2 --image=gcr.io/google-samples/hello-app:2.0

* Expose the Deployment:
## kubectl expose deployment web2 --port=8080 --type=NodePort

* Edit the existing example-ingress.yaml and add the following lines:
      - path: /v2
        pathType: Prefix
        backend:
          service:
            name: web2
            port:
              number: 8080
* Apply the changes:
## kubectl apply -f example-ingress.yaml

* Test Access the 2nd version of the Hello World app.
## curl hello-world.info
## ip:port

* Test Access the 2nd version of the Hello World app.
## curl hello-world.info/v2
## ip:port

* Access the 1st version of the Hello World app:

3. Create a namespace called “jobs”

kubectl config set-context --current --namespace=jobs

4. Create a Cronjob in the “jobs” namespace called “job-a” that run an ubuntu container 
and run the command “kubectl get all -n namespace-a” (ensure it have only get access permissions for the namespace “namespace-a”)


cron file:

job-a
* * * * * root kubectl get all -n namespaces-a >> /root/job-a

Dockerfile
************************************************
* FROM ubuntu:latest


# Add crontab file in the cron directory
ADD job-a /etc/cron.d/job-a


RUN apt-get update && apt-get -y install cron

# Copy hello-cron file to the cron.d directory
COPY job-a /etc/cron.d/job-a

# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/job-a

# Apply cron job
RUN crontab /etc/cron.d/job-a

# Create the log file to be able to run tail
RUN touch /var/log/cron.log

# Run the command on container startup
CMD ["cron", "-f"]

*****************************************
## docker build -t job-a .
 
## docker run -dp 8080:8080 job-a

Remember the -d and -p flags? We’re running the new container in “detached” mode (in the background) and creating a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, we wouldn’t be able to access the application.

***********************************

5. Create a Cronjob in the “jobs” namespace called “job-cross” that run an ubuntu container and run the command “kubectl get all —all-namespaces” (ensure it have only get access permissions for all namespaces)
****************************************
 job-cross file:
 

 * * * * * root kubectl get all --all-namespaces >> /root/job-cross


***********************
Dockerfile
*******************************************
FROM ubuntu:latest


# Add crontab file in the cron directory
ADD job-cross /etc/cron.d/job-cross


RUN apt-get update && apt-get -y install cron

# Copy hello-cron file to the cron.d directory
COPY job-cross /etc/cron.d/job-cross

# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/job-cross

# Apply cron job
RUN crontab /etc/cron.d/job-cross

# Create the log file to be able to run tail
RUN touch /var/log/cron.log

# Run the command on container startup
CMD ["cron", "-f"]

*******************************

## docker build -t job-cross .
 
## docker run -dp 8090:8090 job-cross

Remember the -d and -p flags? We’re running the new container in “detached” mode (in the background) and creating a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, we wouldn’t be able to access the application.

$$$$$$$$$$$$$$$$$$$$$$$$

issues: 
invoke-rc.d: policy-rc.d denied execution of start.

resolved by: 
printf '#!/bin/sh\nexit 0' > /usr/sbin/policy-rc.d

connect to docker:
docker exec -it 451b116f86c6 /bin/bash
