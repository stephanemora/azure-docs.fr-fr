---
title: Présentation d’Azure DevOps Project | Microsoft Docs
description: Comprendre la valeur d’Azure DevOps Project
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: eef71aabaab71f908f950a4467da918dca1787fa
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492542"
---
# <a name="overview-of-azure-devops-project"></a>Présentation d’Azure DevOps Project

Azure DevOps Project facilite la prise en main d’Azure. La ressource DevOps Project vous permet de lancer votre type d’application favori sur le service Azure de votre choix en seulement quelques étapes rapides à partir du portail Azure. DevOps Project définit tout ce dont vous avez besoin pour développer, déployer et surveiller votre application.
Le tableau de bord DevOps Project vous permet de contrôler les validations, les builds et les déploiements de code, à partir d’une seule vue dans le portail Azure.

## <a name="why-should-i-use-the-azure-devops-project"></a>Pourquoi utiliser Azure DevOps Project ?

Azure DevOps Project automatise la configuration d’un pipeline d’intégration continue (CI) et de livraison continue (CD) vers Azure.  Vous pouvez démarrer avec votre code existant ou utiliser l’un des exemples d’applications fournis, puis déployer rapidement cette application vers divers services Azure, comme les Machines virtuelles, App Service, Azure Container Service, Azure SQL Database et Azure Service Fabric.  

Azure DevOps Project effectue tout le travail pour la configuration initiale d’un pipeline DevOps : configurer le référentiel Git initial, configurer le pipeline CI/CD, créer une ressource Application Insights pour le monitoring et fournir une seule vue de l’ensemble de la solution avec la création d’un tableau de bord Azure DevOps Project sur le portail Azure.

Vous pouvez utiliser Azure DevOps Project comme suit :

* Déployer rapidement votre application dans Azure
* Automatiser la configuration d’un pipeline CI/CD VSTS
* Utiliser DevOps Project en tant que modèle pour afficher et comprendre comment configurer correctement l’intégration continue/la livraison continue dans Azure avec VSTS
* Bien démarrer avec le pipeline CI/CD sur Azure, puis personnaliser davantage le pipeline de mise en production en fonction de vos scénarios spécifiques

## <a name="how-do-i-use-the-azure-devops-project"></a>Comment utiliser Azure DevOps Project ?

Azure DevOps Project est disponible à partir du portail Azure.  Vous créez une ressource Azure DevOps Project comme toute autre ressource Azure à partir du portail.  Le projet DevOps Project fournit une expérience de type Assistant étape par étape pour les différentes options de configuration.  

Vous choisissez plusieurs options de configuration dans le cadre de la configuration initiale.  Ces options sont les suivantes :

* Utiliser l’exemple d’application fourni ou apporter votre propre code
* Sélectionner un langage pour l’application
* Choisir une infrastructure d’application en fonction du langage
* Sélectionner un service Azure (cible de déploiement)
* Compte VSTS (nouveau ou existant)
* Choisir votre abonnement Azure
* Choisir l’emplacement des services Azure
* Choisir parmi les différents niveaux de tarification pour les services Azure

Après avoir utilisé Azure DevOps Project, vous pouvez également supprimer toutes les ressources à partir d’un emplacement unique dans le tableau de bord Azure DevOps Project sur le portail Azure.

## <a name="azure-devops-project-and-vsts-integration"></a>Intégration d’Azure DevOps Project et de VSTS

Les projets DevOps Projects sont alimentés par VSTS.  Le projet DevOps Project automatise tout le travail nécessaire dans VSTS pour configurer l’intégration continue/la livraison continue vers Azure.  Un référentiel Git est créé dans un compte VSTS nouveau ou existant.  Le projet DevOps Project valide un exemple d’application ou de votre code existant dans un référentiel Git.  L’automatisation établit également un déclencheur CI pour la build afin que chaque nouvelle validation de code lance une build.  Le projet DevOps Project crée également un déclencheur CD et déploie chaque nouvelle build réussie sur le service Azure de votre choix.  Les définitions de build et de mise en production peuvent être personnalisées pour d’autres scénarios.  Vous pouvez également cloner les définitions de build et de mise en production pour une utilisation dans d’autres projets.

Après avoir créé votre projet DevOps Project, vous pouvez :

* Personnaliser votre build et pipeline de mise en production
* Utiliser des requêtes Pull pour gérer votre flux de code et conserver une qualité élevée
* Tester et générer chaque validation avant de fusionner votre code pour optimiser la qualité
* Effectuer le suivi des problèmes et du backlog de votre projet avec votre application

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Comment commencer à utiliser Azure DevOps Project ?

* [Bien démarrer avec Azure DevOps Project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Vidéos Azure DevOps Project

* [Créer CI/CD avec Azure DevOps Project](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
