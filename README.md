# Sistema Integrado de Gestión [Nombre del Proyecto]

> **Plataforma de Orquestación y Visualización de Datos.**
> Este proyecto unifica y extiende las capacidades de 7 subsistemas: Portal, Geoportal, Bróker, Administración, Repositorio, Dashboards y Estudios.

![Version](https://img.shields.io/badge/version-2.0.0--beta-blue)
![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![Tech Stack](https://img.shields.io/badge/stack-Node%20%7C%20Python%20%7C%20React-orange)

## 📋 Tabla de Contenidos

1. [Arquitectura del Sistema](#-arquitectura-del-sistema)
2. [Descripción de Módulos] (https://github.com/evalenciEAFIT/documentacion/blob/main/grupos/portal/readme.md)
3. [Instalación y Despliegue](#-instalación-y-despliegue)
4. [Configuración de Entorno (.env)](#-configuración-de-entorno)
5. [Guía de Integración por Módulo](#-guía-de-integración-por-módulo)
    * [Administración (Auth)](#1-administración-auth--roles)
    * [Bróker (IoT)](#2-bróker-iot--mensajería)
    * [Geoportal & Mapas](#3-geoportal--capas-espaciales)
    * [Estudios & Repositorio](#4-estudios--repositorio)
    * [Dashboards & Visualización](#5-dashboards--portal)
6. [Flujo de Trabajo (Git)](#-flujo-de-trabajo)

---

## 🧩 Arquitectura del Sistema

El sistema funciona como un **Middleware de Integración** y **Frontend Unificado**. No almacena toda la data, sino que orquesta el flujo entre los servicios especializados.

```mermaid
graph TD
    User((Usuario)) --> Portal[Frontend / Portal Web]
    
    subgraph "Core Services"
        Portal --> API[API Gateway / Backend]
        API --> Admin[Módulo Administración]
        API --> Repo[Módulo Repositorio]
    end

    subgraph "Data & Analytics"
        Broker[Módulo Bróker IoT] -- MQTT/WS --> API
        API --> Dash[Módulo Dashboards]
        Estudios[Módulo Estudios] --> API
    end

    subgraph "Geospatial"
        API --> Geo[Módulo Geoportal]
        Geo --> MapServer[GeoServer / MapStore]
    end

    Repo -.-> Estudios
    Broker -.-> Dash
