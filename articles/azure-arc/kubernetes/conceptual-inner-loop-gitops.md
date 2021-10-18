---
title: Expérience développeur de boucles internes pour les équipes adoptant GitOps
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: conceptual
author: sudivate
ms.author: sudivate
description: 'Cet article fournit une vue d’ensemble conceptuelle de l’expérience du développeur de boucles internes pour les équipes adoptant GitOps '
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, conteneurs, intégration continue, livraison continue, Azure DevOps, Inner loop, Dev Experience
ms.openlocfilehash: 3bd60c3236bb187f14b924f4246d8e6fcfed4ed3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706415"
---
# <a name="inner-loop-developer-experience-for-teams-adopting-gitops"></a>Expérience développeur de boucles internes pour les équipes adoptant GitOps

Cet article explique comment une boucle interne établie peut améliorer la productivité des développeurs et aider à effectuer une transition transparente de la boucle de développement interne vers la boucle externe pour les équipes adoptant GitOps.

## <a name="inner-dev-loop-frameworks"></a>Infrastructures de boucles de développement internes

La génération et le déploiement de conteneurs peuvent ralentir l’expérience de développement interne et affecter la productivité de l’équipe. Les équipes de développement natives cloud bénéficieront d’une infrastructure de boucle de développement interne robuste. Les infrastructures de boucles de développement internes facilitent le processus itératif d’écriture de code, de génération et de débogage.

Les fonctionnalités des infrastructures de boucles de développement internes sont les suivantes :

 
- Automatisez les étapes répétitives comme la génération de code, les conteneurs et le déploiement sur le cluster cible. 
- Utilisation simplifiée des clusters locaux et distants et prise en charge du débogage de tunnel local pour l’installation hybride.
- Possibilité de configurer un flux personnalisé pour la productivité basée sur l’équipe.
- Autorise la gestion des dépendances de microservice. 
- Rechargement à chaud, réacheminement de port, journal et accès au terminal. 



Selon la maturité et la complexité du service, les équipes de développement déterminent la configuration de cluster qu’elles utiliseront pour accélérer la boucle de développement interne : 

* Entièrement local
* Entièrement distant
* Hybride 


Heureusement, il existe de nombreux frameworks qui prennent en charge les fonctionnalités répertoriées. Microsoft propose Bridge to Kubernetes pour le débogage de tunnel local et des offres de marché similaires comme DevSpace, Scaffold et Tilt, entre autres.

> [!NOTE]
> Ne confondez pas l’offre du marché [DevSpace](https://github.com/loft-sh/devspace) avec l’ancien DevSpace de Microsoft, qui s’appelle désormais [Bridge to Kubernetes](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes).


## <a name="inner-loop-to-outer-loop-transition"></a>Transition de boucle interne à externe 

Une fois que vous avez évalué et choisi une infrastructure de développement de boucle interne, créez une boucle interne transparente pour la transition vers une boucle externe.

Comme décrit dans l’exemple [Workflow CI/CD avec GitOps](conceptual-gitops-ci-cd.md) de l’article, un développeur d’applications travaille sur le code de l’application dans un référentiel d’applications. Ce référentiel d’applications contient également des modèles de déploiement Helm et/ou Kustomize de haut niveau. Pipelines CI/CD :

* Générer les manifestes de bas niveau à partir des modèles de haut niveau, en ajoutant des valeurs spécifiques à l’environnement
* Créez une demande de tirage qui fusionne les manifestes de bas niveau avec le référentiel GitOps qui contient l’état souhaité pour l’environnement spécifique. 

Les manifestes de bas niveau similaires peuvent être générés localement pour la boucle de développement interne, en utilisant les valeurs de configuration locales pour le développeur. Les développeurs d’applications peuvent effectuer des itérations sur les modifications de code et utiliser les manifestes de bas niveau pour déployer et déboguer des applications. La génération de manifestes de bas niveau peut être intégrée à un workflow de boucle interne, à l’aide de la configuration locale du développeur. La majeure partie de l’infrastructure de boucle interne permet de configurer des flux personnalisés par extension via des plug-ins personnalisés ou en injectant un appel de script basé sur des hooks. 

## <a name="example-inner-loop-workflow-built-with-devspace-framework"></a>Exemple de workflow de boucle interne généré avec DevSpace Framework


### <a name="diagram-a-inner-loop-flow"></a>Diagramme A : Flux de boucle interne
:::image type="content" source="media/dev-inner-loop.png" alt-text="Diagramme du flot de boucles internes avec devspace.":::

### <a name="diagram-b-inner-loop-to-outer-loop-transition"></a>Diagramme B : Transition de boucle interne à boucle externe
:::image type="content" source="media/inner-loop-to-outer-loop.png" alt-text="Diagramme de la boucle interne à la transition de boucle externe." :::


## <a name="example-workflow"></a>Exemple de flux de travail
En tant que développeur d’applications, Alice :
- Crée un devspace.yaml pour configurer la boucle interne.
- Écrit et teste le code de l’application à l’aide de la boucle interne pour plus d’efficacité.
- Déploie dans un environnement intermédiaire ou de production avec une boucle externe.


Supposons qu’Alice souhaite mettre à jour, exécuter et déboguer l’application dans un cluster local ou distant.

1. Alice met à jour la configuration locale pour l’environnement de développement représenté dans le fichier .env.
1. Alice exécute `devspace use context` et sélectionne le contexte du cluster Kubernetes.
1.  Alice sélectionne un espace de noms à utiliser en exécutant `devspace use namespace <namespace_name>`.
1.  Alice peut itérer les modifications au sein du code de l’application , puis déployer et déboguer l’application sur le cluster cible en exécutant `devspace dev`.
1. L’exécution de `devspace dev` génère des manifestes de bas niveau basés sur la configuration locale d’Alice et déploie l’application. Ces manifestes de bas niveau sont configurés avec des hooks devspace dans devspace.yaml
1. Alice n’a pas besoin de reconstruire le conteneur chaque fois qu’elle effectue des modifications de code, car DevSpace permet le rechargement à chaud, avec la synchronisation de fichiers pour copier ses dernières modifications dans le conteneur.
1. L’exécution de `devspace dev` déploiera également toutes les dépendances configurées dans devspace.yaml, comme les dépendances principales vers le frontal. 
1. Alice teste ses modifications en accédant à l’application par le biais de la redirection configurée dans devspace.yaml.
1. Une fois qu’Alice finalise ses changements, elle peut purger le déploiement en exécutant `devspace purge` et créer une nouvelle demande de tirage pour fusionner ses modifications apportées à la branche de développement du référentiel d’applications.

> [!NOTE]
> Vous trouverez l’exemple de code pour le workflow ci-dessus sur ce [référentiel GitHub](https://github.com/Azure/arc-cicd-demo-src)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des connexions entre votre cluster et un dépôt Git en guise de [ressource de configuration intégrant Kubernetes avec Azure Arc](./conceptual-configurations.md)