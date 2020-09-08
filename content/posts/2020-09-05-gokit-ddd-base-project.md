---
title: The DDD microservice with GO
date: "2020-09-05T22:40:32.169Z"
template: "post"
draft: false
slug: "gokit-ddd-base-project"
category: "templates"
tags:
  - template
  - golang
  - ddd
  - gokit
  - microservice 
description: "A microservice base project template with DDD to start microservices with GO."
socialImage: "https://github.com/FabianBG/dinoplayer-neuralnet/blob/master/docs/dino1.png?raw=true"

---

# Intro
Hi everybody, these last days I decided to star a base project with the basic folder structure and libraries to start easily a new microservice with GOLANG. Right now there is a lot of continuous change in the GOLANG environment so this project uses a specific tech stack but can be adapted to any tech stack due to the architecture of DDD which is an architecture that I think helps a lot to make more refactorable any project.

# Tech Stack
Basically knowing that the arch gonna be Doman Driven Design I find the best suitable techs to apply this patterns to the project so at the end if you know about the current frameworks on the internet you should know (GOKIT)[https://github.com/go-kit/kit] as its definition is not a framework is a bunch of tools to develop microservices it supports a lot of advanced features like services discovering and circuit breakers. SO this is the main reason for me to choose this tech to develop this base project.
For the data persistence, I go for a mongo to keep a simple and versatile data model there is an official mongo connector for GOLANG so it is simple to make the decision of which library use.

![Tech stack](https://dev-to-uploads.s3.amazonaws.com/i/9o4uck63ujn4w351gqxt.png)

# Architecture
The chosen architecture will be Doman Driven Desing to keep the project well organized and very scalable.

![DDD architecture diagram](https://dev-to-uploads.s3.amazonaws.com/i/rnpl06p9zfov7zd19td0.png)

# Project
So there is the project it could be found on my GitHub page on clicking this link [https://github.com/FabianBG/gokit-base].

![Project structure](https://dev-to-uploads.s3.amazonaws.com/i/6wsmha7yx8bwl0fmxqs2.png)