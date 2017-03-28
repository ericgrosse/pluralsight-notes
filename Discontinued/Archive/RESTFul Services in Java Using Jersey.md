# The Fundamentals of RESTFul Services in Java

## What is Jersey?

Jersey is Java's implementation of REST

## What we Won't Cover

The course doesn't cover all the steps of tying the app to a database because that would double the size of the course.

Author mentioned Hibernate and Spring JDBC template as database solutions

## Overview of REST

- REST was built with the pains of SOAP in mind
- Built on the principles of HTTP (ex/ HTTP verbs)

## RESTFul URLs

- Always centered around nouns and never around verbs
 - `https://localhost:8080/car-rental/cars`
 - `https://localhost:8080/car-rental/reservations`
- `cars` and `reservations` are the nouns
- Verbs are expressed through the protocol (GET, PUT, POST, DELETE)
- Path is used to get a specific instance
 - `https://localhost:8080/car-rental/reservations/1234`

# Building Your First RESTFul Service Using Jersey

## Prerequisites

- Java
- Maven
- Sprint STS (or Eclipse with the WTP plugin)
- Apache Tomcat

## Tomcat Overview

- Tomcat has become the defacto web server for Java development.
- Tomcat is a very lightweight web container
- RESTFul services are ran out of a basic war file

## Tomcat Download and Configuration Demo

Go on Tomcat's download page and download the core zip file (under Binary Distributions)
Author placed the unzipped file under `C:\dev\tools`, I decided to put it in the same place
Author also uses `C:\dev\workspace` for his IDE workspace

- In Eclipse, if you don't have a Servers tab, go to `Window -> Show View -> Servers`
- In the Servers tab, click on `new server wizard...`

**Don't have a Servers panel, download Sprint STS**