---
title: Vue d’ensemble d’Azure DevOps Starter | Microsoft Docs
description: Découvrez comment Azure DevOps Starter vous aide à lancer, déployer et gérer votre application à partir d’une seule vue dans le portail Azure.
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 3e6f28da37980717e15b05ab94c582aa5c77e9e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856860"
---
# <a name="overview-of-azure-devops-starter"></a>Vue d’ensemble d’Azure DevOps Starter

 Azure DevOps Starter permet de démarrer facilement avec Azure. Il vous permet de lancer votre application favorite sur le service Azure de votre choix en seulement quelques étapes rapides à partir du portail Azure. 

 DevOps Starter configure tout ce dont vous avez besoin pour développer, déployer et superviser votre application. Vous pouvez utiliser le tableau de bord DevOps Starter pour superviser les validations de code, les builds et les déploiements dans une même vue du portail Azure.

## <a name="advantages-of-using-devops-starter"></a>Avantages de l’utilisation de DevOps Starter

  DevOps Starter automatise la configuration d’un pipeline complet d’intégration continue (CI) et de livraison continue (CD) sur Azure.  Vous pouvez démarrer avec du code existant ou utiliser un des exemples d’application fournis. Vous pouvez ensuite déployer rapidement cette application sur différents services Azure, comme Machines virtuelles, App Service, Azure Kubernetes Service (AKS), Azure SQL Database et Azure Service Fabric.  

  Azure DevOps Starter s’occupe de toute la configuration initiale d’un pipeline DevOps, notamment configurer le dépôt Git initial, configurer le pipeline CI/CD, créer une ressource Application Insights pour la supervision et fournir une même vue de l’ensemble de la solution avec la création d’un tableau de bord DevOps Projects dans le portail Azure.

Vous pouvez utiliser DevOps Starter pour :

* Déployer rapidement votre application dans Azure
* Automatiser la configuration d’un pipeline CI/CD
* Voir et comprendre comment configurer correctement un pipeline CI/CD
* Personnaliser davantage les pipelines de mise en production selon vos scénarios spécifiques

## <a name="how-to-use-devops-starter"></a>Comment utiliser DevOps Starter ?

  DevOps Starter est disponible à partir du portail Azure. Vous créez une ressource DevOps Starter comme toute autre ressource Azure à partir du portail. DevOps Projects fournit une expérience de type Assistant pas à pas pour les différentes options de configuration.  

Vous choisissez plusieurs options de configuration dans le cadre de la configuration initiale. Ces options sont les suivantes :

* Utiliser l’exemple d’application fourni ou utiliser votre propre code
* Sélectionner un langage pour l’application
* Choisir un framework d’application en fonction du langage
* Sélectionner un service Azure (cible de déploiement)
* Créer une organisation Azure DevOps ou utiliser une organisation existante 
* Choisir votre abonnement Azure
* Choisir l’emplacement des services Azure
* Choisir parmi différents niveaux tarifaires pour les services Azure

Après avoir utilisé DevOps Starter, vous pouvez également supprimer toutes les ressources à partir d’un même emplacement dans le tableau de bord DevOps Starter sur le portail Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>Intégration de DevOps Starter et Azure DevOps

DevOps Starter repose sur la technologie Azure DevOps. DevOps Starter automatise tout le travail nécessaire dans Azure Pipelines pour configurer un pipeline CI/CD. Il crée un dépôt Git dans une organisation Azure DevOps nouvelle ou existante, puis valide un exemple d’application ou votre code existant dans un nouveau dépôt Git.  

L’automatisation établit également un déclencheur CI pour la build de sorte que chaque nouvelle validation de code lance une build. DevOps Starter crée un déclencheur CD et déploie chaque nouvelle build réussie sur le service Azure de votre choix.  

Les pipelines de build et de mise en production peuvent être personnalisés pour d’autres scénarios. Vous pouvez également cloner les pipelines de build et de mise en production pour les utiliser dans d’autres projets.

Après avoir créé votre projet DevOps Starter, vous pouvez :

* Personnaliser votre build et pipeline de mise en production
* Utiliser des requêtes Pull pour gérer votre flux de code et conserver une qualité élevée
* Tester et générer chaque validation avant de fusionner votre code pour optimiser la qualité
* Effectuer le suivi de votre backlog et de vos problèmes en même temps que votre application

## <a name="getting-started-with-devops-starter"></a>Bien démarrer avec DevOps Starter

* [Bien démarrer avec DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Vidéos de DevOps Starter

* [Create CI/CD with Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)