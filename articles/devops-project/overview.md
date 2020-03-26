---
title: Vue d’ensemble d’Azure DevOps Projects | Microsoft Docs
description: Comprendre l’intérêt d’Azure DevOps Projects
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
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "71971474"
---
# <a name="overview-of-azure-devops-projects"></a>Vue d’ensemble d’Azure DevOps Projects

 Azure DevOps Projects facilite la prise en main d’Azure. Il vous permet de lancer votre application favorite sur le service Azure de votre choix en seulement quelques étapes rapides à partir du portail Azure. 

 DevOps Projects configure tout ce dont vous avez besoin pour développer, déployer et superviser votre application. Vous pouvez utiliser le tableau de bord DevOps Projects pour superviser les validations de code, les builds et les déploiements dans une même vue du portail Azure.

## <a name="why-should-i-use-devops-projects"></a>Pourquoi utiliser DevOps Projects ?

  DevOps Projects automatise la configuration d’un pipeline complet d’intégration continue (CI) et de livraison continue (CD) sur Azure.  Vous pouvez démarrer avec du code existant ou utiliser un des exemples d’application fournis. Vous pouvez ensuite déployer rapidement cette application sur différents services Azure, comme Machines virtuelles, App Service, Azure Kubernetes Service (AKS), Azure SQL Database et Azure Service Fabric.  

  Azure DevOps Projects s’occupe de toute la configuration initiale d’un pipeline DevOps, notamment configurer le dépôt Git initial, configurer le pipeline CI/CD, créer une ressource Application Insights pour la supervision et fournir une même vue de l’ensemble de la solution avec la création d’un tableau de bord DevOps Projects dans le portail Azure.

Vous pouvez utiliser DevOps Projects pour :

* Déployer rapidement votre application dans Azure
* Automatiser la configuration d’un pipeline CI/CD
* Voir et comprendre comment configurer correctement un pipeline CI/CD
* Personnaliser davantage les pipelines de mise en production selon vos scénarios spécifiques

## <a name="how-do-i-use-devops-projects"></a>Comment utiliser DevOps Projects ?

  DevOps Project est disponible à partir du portail Azure. Vous créez une ressource DevOps Projects comme toute autre ressource Azure à partir du portail. DevOps Projects fournit une expérience de type Assistant pas à pas pour les différentes options de configuration.  

Vous choisissez plusieurs options de configuration dans le cadre de la configuration initiale. Ces options sont les suivantes :

* Utiliser l’exemple d’application fourni ou utiliser votre propre code
* Sélectionner un langage pour l’application
* Choisir un framework d’application en fonction du langage
* Sélectionner un service Azure (cible de déploiement)
* Créer une organisation Azure DevOps ou utiliser une organisation existante 
* Choisir votre abonnement Azure
* Choisir l’emplacement des services Azure
* Choisir parmi différents niveaux tarifaires pour les services Azure

Après avoir utilisé DevOps Projects, vous pouvez également supprimer toutes les ressources à partir d’un même emplacement dans le tableau de bord DevOps Projects sur le portail Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>Intégration de DevOps Projects et d’Azure DevOps

DevOps Projects s’appuie sur Azure DevOps. DevOps Projects automatise tout le travail nécessaire dans Azure Pipelines pour configurer un pipeline CI/CD. Il crée un dépôt Git dans une organisation Azure DevOps nouvelle ou existante, puis valide un exemple d’application ou votre code existant dans un nouveau dépôt Git.  

L’automatisation établit également un déclencheur CI pour la build de sorte que chaque nouvelle validation de code lance une build. DevOps Projects crée un déclencheur CD et déploie chaque nouvelle build réussie sur le service Azure de votre choix.  

Les pipelines de build et de mise en production peuvent être personnalisés pour d’autres scénarios. Vous pouvez également cloner les pipelines de build et de mise en production pour les utiliser dans d’autres projets.

Après avoir créé votre projet DevOps Project, vous pouvez :

* Personnaliser votre build et pipeline de mise en production
* Utiliser des requêtes Pull pour gérer votre flux de code et conserver une qualité élevée
* Tester et générer chaque validation avant de fusionner votre code pour optimiser la qualité
* Effectuer le suivi de votre backlog et de vos problèmes en même temps que votre application

## <a name="how-do-i-start-using-devops-projects"></a>Comment commencer à utiliser DevOps Projects ?

* [Bien démarrer avec DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Vidéos sur DevOps Projects

* [Create CI/CD with Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
