# Docker Roadmap — From Basics to Advanced

A complete learning guide covering Docker fundamentals, core concepts, advanced topics, and real-world use cases.

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [1. Docker Basics](#1-docker-basics)
4. [2. Docker Images](#2-docker-images)
5. [3. Docker Containers](#3-docker-containers)
6. [4. Docker Networking](#4-docker-networking)
7. [5. Docker Volumes & Storage](#5-docker-volumes--storage)
8. [6. Docker Compose](#6-docker-compose)
9. [7. Docker Registry & Distribution](#7-docker-registry--distribution)
10. [8. Docker in CI/CD](#8-docker-in-cicd)
11. [9. Docker Security](#9-docker-security)
12. [10. Advanced Docker Concepts](#10-advanced-docker-concepts)
13. [11. Docker & Kubernetes](#11-docker--kubernetes)
14. [12. Real-World Projects](#12-real-world-projects)
15. [13. Learning Resources](#13-learning-resources)

## Introduction

Docker is a platform to build, ship, and run applications using lightweight containers. This roadmap provides a full guide from beginner to expert.

## Prerequisites

* Basic Linux commands
* Git & version control
* Basic programming knowledge (Node.js / Python / Java etc.)
* Optional: Cloud basics (AWS / Azure / GCP)

# 1. Docker Basics

## 1.1 What is Docker?

* Virtualization vs containerization
* Why Docker exists
* Benefits of Docker
* Docker ecosystem overview

## 1.2 Install & Setup Docker

* Docker Desktop (Windows/Mac)
* Docker Engine (Linux)
* Basic Docker commands
* Running first container (`hello-world`, `alpine`, `ubuntu`)

## 1.3 Docker Architecture

* Docker Engine
* Docker CLI
* Docker Daemon
* Docker Hub
* Docker Registry
* Container runtime

# 2. Docker Images

## 2.1 Understanding Images

* Image layers
* Base vs custom images
* Tags

## 2.2 Dockerfile Basics

* FROM, RUN, CMD, ENTRYPOINT
* EXPOSE, WORKDIR
* COPY vs ADD
* ENV, USER

## 2.3 Building Images

* `docker build`
* Versioning
* Multi-stage builds
* Reducing image size

## 2.4 Image Management

* `docker images`
* `docker rmi`
* Pruning
* Digest vs tags

# 3. Docker Containers

## 3.1 Understanding Containers

* What is a container?
* Containers vs VMs
* Namespaces & cgroups

## 3.2 Container Execution

* `docker run`
* Interactive vs detached
* Attaching to containers
* Restart policies

## 3.3 Container Lifecycle

* start, stop, restart, pause, kill, rm

## 3.4 Logs & Monitoring

* `docker logs`
* `docker top`
* `docker stats`

# 4. Docker Networking

## 4.1 Networking Basics

* Network drivers: bridge, host, none, overlay, macvlan

## 4.2 Port Mapping

* EXPOSE vs `-p`
* Container-to-host communication

## 4.3 Custom Networks

* Creating custom networks
* DNS service discovery

## 4.4 Multi-host Networking

* Swarm overlay
* Load balancing

# 5. Docker Volumes & Storage

## 5.1 Importance of Storage

* Persistence & data integrity

## 5.2 Volume Types

* Anonymous
* Named
* Bind mounts

## 5.3 Managing Volumes

* `docker volume` commands
* Backups
* Sharing volumes

## 5.4 Storage Drivers

* AUFS, OverlayFS, ZFS, Btrfs

# 6. Docker Compose

## 6.1 Introduction

* Multi-container applications
* YAML basics

## 6.2 compose.yaml Structure

* Services, networks, volumes
* Build section
* Environment variables
* depends_on

## 6.3 Compose Commands

* up, down, logs, build, start, stop

## 6.4 Production Compose

* Override files
* Multi-stage setups
* Scaling

# 7. Docker Registry & Distribution

## 7.1 Docker Hub

* Public vs private repos
* Push/pull
* Automated builds

## 7.2 Private Registries

* Harbor, GitHub Packages, GitLab Registry, ECR, GCR, ACR

## 7.3 Image Versioning

* Semantic versioning
* Avoiding `latest`
* Hash-based references

# 8. Docker in CI/CD

## 8.1 CI Builds

* Best practices
* Caching

## 8.2 CI Tools

* GitHub Actions
* GitLab CI
* Jenkins
* Azure DevOps

## 8.3 Registry Authentication

* Secure pushes
* Secrets handling

# 9. Docker Security

## 9.1 Security Basics

* Attack surfaces
* Scanners: Trivy, Snyk, Anchore

## 9.2 Best Practices

* Non-root user
* Minimal images
* Distroless
* Capabilities restrictions

## 9.3 Secrets Management

* Env vars
* Docker secrets
* Vault & cloud secret stores

# 10. Advanced Docker Concepts

## 10.1 Internals

* cgroups
* namespaces
* union filesystems

## 10.2 Advanced Dockerfile

* ENTRYPOINT vs CMD
* HEALTHCHECK
* ONBUILD
* Advanced multi-stage builds

## 10.3 Performance

* Build cache
* Layer optimization
* Pipeline performance

## 10.4 Debugging

* Exec into containers
* Debugging network issues
* Resource profiling

# 11. Docker & Kubernetes

## 11.1 Container Runtime

* Docker vs containerd
* CRI basics

## 11.2 Deployment

* Pods, Deployments, Services
* Ingress
* ConfigMaps, Secrets
* Helm basics

# 12. Real-World Projects

## Beginner

* Simple app build
* Custom Dockerfile
* Compose with DB

## Intermediate

* Multi-stage Dockerfile
* Private registry setup
* Microservices with Compose

## Advanced

* Full CI/CD with Docker
* Secure images
* Kubernetes deployment
* Image scanning & signing

# 13. Learning Resources

## Videos

* Docker Official
* FreeCodeCamp
* KodeKloud

## Documentation

* Docker Docs
* Awesome-Docker

## Hands-on Labs

* Play With Docker
* Killercoda
* KodeKloud

