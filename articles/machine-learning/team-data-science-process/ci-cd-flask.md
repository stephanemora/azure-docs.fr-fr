---
title: Créer un pipeline Azure d’intégration continue - Team Data Science Process
description: 'DevOps pour applications d’intelligence artificielle (IA) : création d’un pipeline d’intégration continue sur Azure à l’aide de Docker et Kubernetes'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: d99149f8112c19a07208523a1ee26ba1c36e5362
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62103539"
---
# <a name="creating-continuous-integration-pipeline-on-azure-using-docker-kubernetes-and-python-flask-application"></a>Création d’un pipeline d’intégration continue sur Azure à l’aide de l’application Docker, Kubernetes et Python Flask
Pour une application IA, il existe souvent deux flux de travail : des chercheurs de données générant des modèles de Machine Learning et des développeurs d’applications générant l’application et la présentant aux utilisateurs finaux pour qu’ils l’utilise. Dans cet article, nous allons montrer comment implémenter un pipeline d’intégration continue (CI)/de livraison continue (CD) pour une application IA. Une application IA est une combinaison de code d’application intégré avec un modèle de Machine Learning (ML) préformé. Pour cet article, nous extrayons un modèle préformé d’un compte de stockage d’objets blob Azure privé. Il peut également s’agir d’un compte AWS S3. Dans cet article, nous allons utiliser une application web Python Flask simple.

> [!NOTE]
> Il s’agit de l’une des différentes méthodes de CI/CD. Il existe des alternatives aux outils et autres prérequis mentionnés ci-dessous. Nous les publierons à mesure que nous développerons d’autres contenus.
>
>

## <a name="github-repository-with-document-and-code"></a>Référentiel GitHub avec document et code
Vous pouvez télécharger le code source à partir de [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). Un [didacticiel détaillé](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) est également disponible.

## <a name="pre-requisites"></a>Conditions préalables
Les prérequis pour le pipeline CI/CD décrit ci-dessous sont les suivants :
* [Organisation Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Cluster Azure Container Service (ACS) exécutant Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Compte Azure Container Registry (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Installez Kubectl pour exécuter des commandes sur le cluster Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Nous en aurons besoin pour récupérer la configuration du cluster ACS. 
* Dupliquez le référentiel sur votre compte GitHub.

## <a name="description-of-the-cicd-pipeline"></a>Description du pipeline CI/CD
Le pipeline démarre pour chaque nouvelle validation, exécute la suite de tests et, si le test réussit, prend la dernière build et la place dans un conteneur Docker. Le conteneur est ensuite déployé à l’aide d’Azure Container Service (ACS) et les images sont stockées de manière sécurisée dans Azure Container Registry (ACR). ACS exécute Kubernetes pour gérer le cluster de conteneur, mais vous pouvez choisir Docker Swarm ou Mesos.

L’application extrait en toute sécurité le dernier modèle d’un compte de stockage Azure et le place dans l’application. L’application déployée contient le code d’application et le modèle de ML dans un même conteneur. Ceci permet de séparer les développeurs d’applications et les chercheurs de données pour s’assurer que leur application de production exécute toujours le dernier code avec le dernier modèle de ML.

L’architecture du pipeline est indiquée ci-dessous. 

![Architecture](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Étapes du pipeline CI/CD
1. Le développeur travaille dans l’IDE de son choix sur le code d’application.
2. Il valide le code sur le contrôle de source de son choix (Azure DevOps prend bien en charge les différents contrôles de source)
3. À part, le chercheur de données travaille sur le développement de son modèle.
4. Lorsque le résultat le satisfait, il publie le modèle dans un référentiel de modèle. Ici, nous utilisons un compte de stockage d’objets blob. 
5. Une build est lancée dans Azure DevOps en fonction de la validation dans GitHub.
6. Le pipeline de build Azure DevOps extrait le dernier modèle du conteneur d’objets blob, et crée un conteneur.
7. Azure DevOps envoie l’image au référentiel d’image privée dans Azure Container Registry
8. Selon une planification définie (de nuit), le pipeline de mise en production est démarré.
9. La dernière image d’ACR est extraite et déployée sur le cluster Kubernetes sur ACS.
10. Des utilisateurs demandent l’application via un serveur DNS.
11. Le serveur DNS transfère la requête à l’équilibreur de charge et renvoie la réponse à l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes
* Reportez-vous au [didacticiel](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) pour connaître les détails et implémenter votre propre pipeline CI/CD pour votre application.

## <a name="references"></a>Références
* [TDSP (Team Data Science Process)](https://aka.ms/tdsp)
* [Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Azure DevOps](https://www.visualstudio.com/vso/)
* [Azure Kubernetes Services (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
