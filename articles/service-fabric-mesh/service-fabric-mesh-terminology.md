---
title: Terminologie d’Azure Service Fabric mesh | Microsoft Docs
description: Découvrez les termes couramment utilisés en rapport avec Azure Service Fabric mesh.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7c3ce5571c54d6c613114ea49999e450934c8ff4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136196"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologie de Service Fabric mesh

Azure Service Fabric mesh est un service entièrement géré qui permet aux développeurs de déployer des applications de microservices sans gestion de machines virtuelles, de stockage ou de mise en réseau. Cet article décrit en détail la terminologie utilisée dans Azure Service Fabric mesh pour que vous compreniez les termes utilisés dans la documentation.

## <a name="service-fabric"></a>Service Fabric

Azure Service Fabric est une plateforme de systèmes distribués open source qui facilite l’empaquetage, le déploiement et la gestion de microservices évolutifs et fiables. Service Fabric est l’orchestrateur qui sous-tend Service Fabric mesh. Service Fabric offre des options pour la façon de générer et d’exécuter des applications de microservices. Vous pouvez utiliser n’importe quelle infrastructure pour écrire vos services, et choisir l’emplacement où exécuter l’application parmi plusieurs options d’environnement.

## <a name="deployment-and-application-models"></a>Modèles de déploiement et d’application 

Pour déployer vos services, vous devez décrire la manière dont ils doivent s’exécuter. Service Fabric prend en charge trois modèles de déploiement :

### <a name="resource-model"></a>Modèle de ressource
Les ressources sont tout ce qui peut être déployé individuellement sur Service Fabric, dont des applications, des services, des réseaux et des volumes. Les ressources sont définies à l’aide d’un fichier YAML ou JSON utilisant le schéma de modèle de ressource Azure.

Le modèle de ressource est la façon la plus simple de décrire vos applications Service Fabric. Son principal objectif est un déploiement et une gestion simples des services en conteneur.

Les ressources peuvent être déployées partout où Service Fabric s’exécute.

### <a name="native-model"></a>Modèle natif
Le modèle d’application natif fournit à vos applications un accès complet de bas niveau à Service Fabric. Les applications et services sont définis en tant que types inscrits dans des fichiers manifeste XML.

Le modèle natif prend en charge l’infrastructure Reliable Services, qui fournit un accès aux API de runtime de Service Fabric et aux API de gestion du cluster en C# et Java. Le modèle natif prend également en charge les conteneurs et exécutables arbitraires.

Le modèle natif n’est pas pris en charge dans l’environnement de Service Fabric mesh.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) fait partie du projet Docker. Service Fabric fournit un support limité pour le déploiement d’applications à l’aide du modèle Docker Compose.

## <a name="environments"></a>Environnements

Service Fabric est une technologie de plateforme open source sur laquelle sont basés plusieurs produits et services différents. Microsoft propose les options suivantes :

 - **Service Fabric mesh** : service entièrement géré pour l’exécution d’applications Service Fabric dans Microsoft Azure.
 - **Azure Service Fabric** : offre de cluster Service Fabric hébergé dans Azure. Il fournit une intégration entre Service Fabric et l’infrastructure Azure, ainsi que la gestion de mise à niveau et de la configuration des clusters Service Fabric.
 - **Service Fabric autonome** : ensemble d’outils d’installation et de configuration pour [déployer des clusters Service Fabric n’importe où](/azure/service-fabric/service-fabric-deploy-anywhere) (localement ou sur n’importe quel fournisseur de cloud). Non géré par Azure.
 - **Cluster de développement service Fabric** : fournit une expérience de développement local sur Windows, Linux ou Mac, pour le développement d’applications Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrice de prise en charge d’environnement, d’infrastructure et de modèle de déploiement
Les différents environnements ont différents niveaux de prise en charge pour les infrastructures et les modèles de déploiement. Le tableau suivant décrit les combinaisons d’infrastructure et de modèle de déploiement prises en charge.

| Type d’Application | Décrit par | Azure Service Fabric mesh | Clusters de service Azure (tout système d’exploitation)| Cluster local : Windows | Cluster local : Linux | Cluster local : Mac | Cluster autonome (Windows)
|---|---|---|---|---|---|---|---|---|---|
| Applications Service Fabric mesh | Modèle de ressource (YAML et JSON) | Prise en charge |Non pris en charge | Prise en charge |Non pris en charge | Non pris en charge | Non pris en charge |
|Applications natives Service Fabric | Modèle d’application native (XML) | Non pris en charge| Prise en charge|Prise en charge|Prise en charge|Prise en charge|Prise en charge|

Le tableau suivant décrit les différents modèles d’application et les outils existants pour ceux-ci par rapport à Service Fabric.

| Type d’Application | Décrit par | Visual Studio 2017 | Visual Studio 2015 | Eclipse | Visual Studio Code | SFCTL | AZ CLI | PowerShell
|---|---|---|---|---|---|---|---|---|---|
| Applications Service Fabric mesh | Modèle de ressource (YAML et JSON) | Prise en charge |Non pris en charge |Non pris en charge |Non pris en charge |Non pris en charge | Pris en charge : environnement Mesh uniquement | Non pris en charge
|Applications natives Service Fabric | Modèle d’application native (XML) | Prise en charge| Prise en charge|Prise en charge|Prise en charge|Prise en charge|Prise en charge|Prise en charge|

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Service Fabric mesh, voir la
- [Vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md)
