Containers Overview — Complete Notes

What is the Problem That Containers Solve?
Problem 1 — Dependency Conflict
When multiple applications need different versions of the same software on one server, they conflict with each other. For example, App A needs Node.js v6 and App B needs Node.js v10 — both cannot exist on the same OS at the same time.
Problem 2 — "Works on My Machine"
An application works perfectly on a developer's laptop but crashes on the testing or production server because each machine has a different OS version, different library versions, and different configurations.
Problem 3 — Long Setup Time
Every new developer who joins the team has to spend days setting up their machine — installing the right OS, right versions of tools, fixing conflicts — before they can even start working.

What is a Container?
A container is a lightweight, standalone, executable package that includes everything needed to run an application:

Application code
Dependencies and libraries
Configuration files
Minimum OS libraries

Containers are isolated from each other and from the host machine. This means two containers with different versions of the same software can run on the same server without any conflict.

How Containers Solve the Problems
Problem 1 solved — Container 1 can have Node v6 and Container 2 can have Node v10, both running on the same server, completely isolated from each other.
Problem 2 solved — The developer builds an image on their laptop and the exact same image runs on testing and production. No more "works on my machine" issue.
Problem 3 solved — A new developer just needs to install Docker, pull the image, and run it. Setup time goes from days to minutes.

Container vs Virtual Machine
A Virtual Machine (VM) contains a full Guest Operating System inside it, which makes it heavy (GBs in size) and slow to start (takes minutes). A Container shares the Host OS kernel and only packages the application and its dependencies, making it lightweight (MBs in size) and fast to start (takes seconds).
FeatureVirtual MachineContainerSizeGigabytesMegabytesStartup TimeMinutesSecondsOSFull Guest OS insideShares Host OS kernelPerformanceSlower (hypervisor overhead)Near native speedIsolationComplete hardware levelProcess levelPortabilityLess portableHighly portable

What is Docker?
Docker is the most popular platform for building and running containers. It has three main components:
1. Dockerfile
A Dockerfile is a text file containing step-by-step instructions to build a container image. It defines the base OS, what software to install, what code to copy, and what command to run.
2. Image
An image is a read-only template built from a Dockerfile. It is like a blueprint or recipe. You cannot run a Dockerfile directly — you first build it into an image.
3. Container
A container is a running instance of an image. Just like you can bake multiple cakes from one recipe, you can create multiple containers from one image.

Image vs Container
Image = The recipe (read-only, static, not running)
Container = The actual cake (running instance created from the image)
One image can create many containers. The image never changes — only containers are started, stopped, and deleted.

Docker Basic Commands
docker pull nginx                   → Download image from Docker Hub
docker run nginx                    → Create and start a container
docker run -d nginx                 → Run container in background
docker run -p 8080:80 nginx         → Map port 8080 on host to 80 in container
docker ps                           → List running containers
docker ps -a                        → List all containers including stopped
docker stop <container-name>        → Stop a running container
docker rm <container-name>          → Delete a stopped container
docker rmi nginx                    → Delete an image
docker exec -it <name> bash         → Enter inside a running container
docker build -t myapp:1.0 .         → Build image from Dockerfile
docker images                       → List all downloaded images
docker logs <container-name>        → View container logs

Container Lifecycle
Dockerfile → (docker build) → Image → (docker run) → Container
Container states:

Running — container is active and working
Stopped — container has been stopped but not deleted
Deleted — container is permanently removed

Image can be pushed to Docker Hub (public registry) so others can pull and use it on any machine.

How Docker Internals Work
Containers achieve isolation using two Linux kernel features:
Namespaces — Isolates the container's view of the system. Each container gets its own isolated process tree, network stack, filesystem, and hostname. It cannot see other containers' processes.
Cgroups (Control Groups) — Limits how much CPU, memory, and disk I/O a container can use. This prevents one container from consuming all server resources.

What is Docker Hub?
Docker Hub is a public cloud registry where Docker images are stored and shared. It is like GitHub but for container images instead of code. You can pull official images like nginx, mysql, redis, node directly from Docker Hub for free.

Container and Kubernetes Connection
Docker is used to build and run individual containers. When you have hundreds of containers running across multiple servers, managing them manually is impossible. This is where Kubernetes comes in.
Kubernetes is the orchestration tool that sits on top of containers and automatically handles:

Which container runs on which server
Restarting crashed containers
Scaling containers up and down based on traffic
Load balancing traffic across containers
Zero downtime deployments

In short: Docker creates containers. Kubernetes manages them at scale.

Key Interview Questions and Answers
Q: What is the difference between a container and a virtual machine?
A: A VM contains a full Guest OS making it heavy (GBs) and slow (minutes to start). A container shares the Host OS kernel and only packages the app and dependencies making it lightweight (MBs) and fast (seconds to start).
Q: What is the difference between an image and a container?
A: An image is a read-only static blueprint or template. A container is a live running instance created from that image. Multiple containers can be created from a single image.
Q: How does a container achieve isolation?
A: Using Linux Namespaces (isolates processes, network, filesystem) and Cgroups (limits CPU and memory usage per container).
Q: What is Docker Hub?
A: Docker Hub is a public registry where pre-built Docker images are stored. Anyone can pull official images like nginx, mysql, redis from Docker Hub and run them instantly.
Q: What problem does containerization solve?
A: It solves dependency conflicts between applications, the "works on my machine" problem, and slow environment setup. Containers package everything the app needs so it runs consistently on any machine.

Summary
A container is a self-contained package of an application with all its dependencies that runs consistently on any environment. Docker is the tool used to build and run containers. Containers are lightweight and fast compared to Virtual Machines because they share the Host OS kernel instead of running a full Guest OS. Kubernetes is used to orchestrate and manage hundreds of containers at scale automatically.