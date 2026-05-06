# ecommerce-spring-cloud-discovery-server

Eureka Service Registry for the Spring Cloud microservices demo project.

## What This Service Does

This is the **Service Discovery** node of the system. Every other microservice registers itself here on startup and queries this registry to locate other services by name — without hard-coding any host or port.

It is built with **Netflix Eureka Server**, wrapped by Spring Cloud.

## Key Configuration

| Property | Value | Why |
|---|---|---|
| `server.port` | `8761` | Standard Eureka port |
| `eureka.client.register-with-eureka` | `false` | The server does not register with itself |
| `eureka.client.fetch-registry` | `false` | The server does not need to cache the registry locally |

## How to Run

```bash
./mvnw spring-boot:run
```

Once started, the Eureka dashboard is available at:

```
http://localhost:8761
```

You will see all registered services listed there as they come online.

## Stack

| Component | Version |
|---|---|
| Java | 21 |
| Spring Boot | 4.0.6 |
| Spring Cloud | 2025.1.1 |
| Eureka Server | via `spring-cloud-starter-netflix-eureka-server` |

## Startup Order

This service must start **second**, right after `config-server`:

```
1. config-server      :8888   ← reads centralized config from Git
2. discovery-server   :8761   ← this service
3. product-service    :8081
4. inventory-service  :8082
5. order-service      :8083
6. api-gateway        :8080
```

---

# Project Overview — Spring Cloud Microservices Demo

An educational project to learn Spring Cloud by building a minimal e-commerce system using a microservices architecture.

## Goal

Implement the core Spring Cloud building blocks — centralized configuration, service discovery, inter-service communication, API routing, and resilience — all wired together in a realistic (but simple) distributed system.

## Architecture

```
[HTTP Client]
      |
[API Gateway :8080]
      |
      ├──→ [Product Service   :8081]
      ├──→ [Inventory Service :8082]
      └──→ [Order Service     :8083]
                  |
                  ├──→ [Product Service]    (via OpenFeign)
                  └──→ [Inventory Service]  (via OpenFeign)

All services register on  → [Eureka Discovery Server :8761]
All services read config from → [Config Server :8888]
Config Server reads from      → [config-repo on GitHub]
```

## Repository Structure (Polyrepo)

| # | Repository | Role |
|---|---|---|
| 1 | `spring-cloud-config-repo` | Plain YAML files, read by Config Server via Git |
| 2 | `spring-cloud-config-server` | Serves centralized configuration over HTTP |
| 3 | `spring-cloud-discovery-server` | Eureka — service registry |
| 4 | `spring-cloud-api-gateway` | Single entry point, routes requests to microservices |
| 5 | `spring-cloud-product-service` | Product CRUD |
| 6 | `spring-cloud-inventory-service` | Inventory CRUD |
| 7 | `spring-cloud-order-service` | Order orchestration, calls product and inventory |

## Spring Cloud Concepts Covered

| Concept | Component | Repository |
|---|---|---|
| Centralized configuration | Spring Cloud Config | `config-server` + `config-repo` |
| Service Discovery | Netflix Eureka | `discovery-server` |
| Client-side load balancing | Spring Cloud LoadBalancer | built into Feign and Gateway |
| Inter-service communication | OpenFeign | `order-service` |
| API Gateway / routing | Spring Cloud Gateway | `api-gateway` |
| Circuit Breaker (optional) | Resilience4j | `order-service` |

## Common Stack

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.3.5</version>
</parent>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>2023.0.3</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

**Java:** 21
