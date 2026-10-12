# Docker Nginx & Jenkins Assignment

A beginner-friendly Docker project that builds custom Nginx and Jenkins images, runs containers with port mapping, and documents the AWS EC2 Security Group rules needed to access the services.

## Project objectives

- Install Docker on an Ubuntu EC2 instance.
- Build a custom Nginx image.
- Build a custom Jenkins image.
- Run containers in detached mode and map host ports to container ports.
- Configure EC2 inbound rules safely.

## Project structure

```text
docker-nginx-jenkins-assignment/
├── nginx/
│   └── Dockerfile
├── jenkins/
│   └── Dockerfile
└── README.md
```

## 1. Install Docker on Ubuntu

Run these commands on your Ubuntu EC2 instance:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

If Docker commands require elevated permissions, prefix them with `sudo`.

## 2. Build and run the Nginx image

Move into the Nginx folder:

```bash
cd ~/docker-nginx-jenkins-assignment/nginx
```

Build the image:

```bash
sudo docker build -t web-container:v1 .
sudo docker images
```

Run the container, mapping host port `80` to container port `80`:

```bash
sudo docker run -d --name my-nginx -p 80:80 web-container:v1
```

Check the running container and logs:

```bash
sudo docker ps
sudo docker logs my-nginx
```

Open this address in your browser:

```text
http://<EC2-PUBLIC-IP>:80
```

## 3. Build and run the Jenkins image

Move into the Jenkins folder:

```bash
cd ~/docker-nginx-jenkins-assignment/jenkins
```

Build the image:

```bash
sudo docker build -t jenkins-container:v1 .
sudo docker images
```

Run Jenkins, mapping host port `8080` to container port `8080`:

```bash
sudo docker run -d --name my-jenkins -p 8080:8080 jenkins-container:v1
```

Check the container and logs:

```bash
sudo docker ps
sudo docker logs my-jenkins
```

Open Jenkins:

```text
http://<EC2-PUBLIC-IP>:8080
```

**Note:** The first Jenkins startup can take a few minutes. This simple assignment uses the official Jenkins LTS image. For persistent Jenkins data, add a volume; for a long-lived deployment, review Jenkins' official installation and security guidance.

## 4. AWS EC2 Security Group

Add only the inbound rules you need. A safer starting point is:

| Type | Protocol | Port | Source | Purpose |
|---|---|---:|---|---|
| SSH | TCP | 22 | Your public IP `/32` | SSH administration |
| HTTP | TCP | 80 | `0.0.0.0/0` if public access is intended | Nginx website |
| Custom TCP | TCP | 8080 | Your public IP `/32` or trusted IP range | Jenkins web interface |

Do not expose SSH to everyone. Jenkins can control build jobs and access credentials, so avoid opening port `8080` to the entire internet. Use a trusted IP range or a private access method.

The notebook mentions port `8081` as a possible custom TCP rule. The Docker commands in this README use host port `8080` for Jenkins; open `8081` only if you intentionally change the host-side port mapping.

## 5. Useful Docker commands

```bash
# List running containers
sudo docker ps

# List all containers, including stopped ones
sudo docker ps -a

# List local images
sudo docker images

# Stop containers
sudo docker stop my-nginx my-jenkins

# Start containers again
sudo docker start my-nginx my-jenkins

# Remove containers (stop them first)
sudo docker rm my-nginx my-jenkins

# Remove images after removing containers, if no longer needed
sudo docker rmi web-container:v1 jenkins-container:v1
```

## Cleanup

To stop and remove the containers created for this assignment:

```bash
sudo docker rm -f my-nginx my-jenkins
```

## What I learned

- Docker images and containers
- Writing a Dockerfile with `FROM` and `EXPOSE`
- Building images with `docker build`
- Running containers with `docker run`
- Port mapping between an EC2 host and a container
- Basic AWS EC2 Security Group configuration

## License

This project is for learning and practice. Use and adapt it for your own learning portfolio.
