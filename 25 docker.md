# Docker

## Important folders

## Assignments

DOCKER:
Dockerfiles in /etc/scripts/docker
assignment 2 = Dockerfile + urls.html  
 assignment 3 = https://hub.docker.com/repository/docker/ferreverheyensasm2324/sasm_docker/general

    op eigen pc, kan niet op server
    sudo docker login\
    sudo docker build -t nginx-urls-image .
    sudo docker tag nginx-urls-image ferreverheyensasm2324/sasm_docker:latest
    sudo docker push ferreverheyensasm2324/sasm_docker:latest

    assignment 4 = assignment

## Handy stuff

https://wiki.uclllabs.be/index.php/SaSM_Lab_docker

**Docker file**

    # Start from Alpine
    FROM alpine:latest

    # Install NGINX
    RUN apk add --no-cache nginx

    # Create a directory for your HTML file
    RUN mkdir -p /usr/share/nginx/html

    # Copy your HTML file into the NGINX directory
    COPY urls.html /usr/share/nginx/html/index.html

    # Expose port 80
    EXPOSE 80

    # Start NGINX
    CMD ["nginx", "-g", "daemon off;"]
