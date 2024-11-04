---
title: "Kubernetes"
weight: 10
author: Arne Duyver
draft: false
---

## Wat is Kubernetes?

Kubernetes (afgekort als K8s) is een open-source platform ontworpen voor het automatiseren van de uitrol, schaalvergroting en beheer van gecontaineriseerde applicaties. Het helpt ontwikkelaars en operationele teams om applicaties in containers (zoals Docker) te beheren en te schalen. Kubernetes biedt een flexibele, gedistribueerde infrastructuur waarin je eenvoudig verschillende services kunt orkestreren en beheren.

### Kernconcepten van Kubernetes:

- **Pods**: De kleinste eenheid in Kubernetes die een of meerdere containers bevat. Pods draaien op nodes in een cluster.
- **Nodes**: Fysieke of virtuele machines waarop containers draaien. Een Kubernetes-cluster bestaat uit meerdere nodes.
- **Cluster**: Een verzameling nodes beheerd door Kubernetes, waarin je je gecontaineriseerde applicaties kunt draaien.
- **Deployment**: Een object dat ervoor zorgt dat je applicaties in een bepaalde staat blijven, door pods te schalen en up-to-date te houden.
- **Service**: Een abstractie die een stabiel netwerk-IP biedt om toegang te krijgen tot een set pods.
- **Namespace**: Een mechanisme voor logische scheiding in een cluster, zodat meerdere teams of projecten resources kunnen delen.

## Hoe gebruik je Kubernetes?

### 1. Installatie van Kubernetes
Je kunt Kubernetes lokaal installeren met tools zoals Minikube of in de cloud met platforms als Google Kubernetes Engine (GKE), Amazon Elastic Kubernetes Service (EKS) of Microsoft Azure Kubernetes Service (AKS).

### 2. Containerizen van Applicaties
Gebruik containertechnologieën zoals Docker om je applicaties in containers te plaatsen. Een container bevat de code, afhankelijkheden en configuraties die nodig zijn om de applicatie uit te voeren.

### 3. Het creëren van Kubernetes-configuratiebestanden
Kubernetes gebruikt YAML-bestanden om de gewenste toestand van je applicaties te definiëren.

## Opdracht

Bekijk volgende [video](https://www.youtube.com/watch?v=s_o8dwzRlu4&t=5s) om de basis te leren en vertrokken te raken met Minicube en K8S. Volgende les breiden we dit uit om een cluster te maken van echte servers en je eigen applicaties er op te installeren.
