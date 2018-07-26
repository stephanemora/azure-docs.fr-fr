---
title: Présentation du modèle de ressource Azure Service Fabric | Microsoft Docs
description: Découvrez le modèle de ressource Service Fabric, une approche simplifiée de la définition d’applications Service Fabric mesh.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075290"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Présentation du modèle de ressource Azure Service Fabric

Le modèle de ressource Service Fabric décrit une approche simple de la définition des ressources qui composent une application Service Fabric mesh. Les types de ressources actuellement pris en charge dans ce modèle sont les suivants :

- APPLICATIONS
- Services
- Volumes
- Réseaux

Chaque ressource est décrite de manière déclarative dans un fichier de ressources, qui est un simple document YAML ou JSON décrivant l’application mesh, et approvisionnée par la plateforme Service Fabric.

## <a name="resource-overview"></a>Vue d’ensemble des ressources

### <a name="applications-and-services"></a>Applications et services

Une ressource d’application est l’unité de déploiement, le contrôle de version et la durée de vie d’une application mesh. Elle est composée d’une ou plusieurs ressources de service qui représentent un microservice. Chaque ressource de service, à son tour, se compose d’un ou plusieurs packages de code qui décrivent tout ce qui est nécessaire pour exécuter l’image conteneur associée au package de code, dont les éléments suivants :

- Nom, version et registre du conteneur
- Ressources de processeur et de mémoire requises pour chaque conteneur
- Points de terminaison réseau
- Volumes à monter dans le conteneur, faisant référence à une ressource de volume distincte.

Tous les packages de code définis comme faisant partie d’une ressource de service sont déployés et activés ensemble en tant que groupe. La ressource de service décrit également le nombre d’instances du service à exécuter, et référence d’autres ressources (par exemple, des ressources réseau) dont elle dépend.

Si une application mesh est composée de plusieurs services, il n’est pas garanti que ceux-ci s’exécutent ensemble sur le même nœud. De même, pendant une mise à niveau de l’application, l’échec de mise à niveau d’un seul service entraîne la restauration de la version précédente de tous les services.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Comme mentionné précédemment, le cycle de vie de chaque instance d’application peut être géré indépendamment. Par exemple, une instance d’application peut être mise à niveau indépendamment des autres instances d’application. Généralement, vous maintenez un nombre relativement faible de services dans une application car, plus vous placez de services dans une application, plus il devient difficile de gérer chacun d’eux de façon indépendante.

### <a name="networks"></a>Réseaux

Une ressource réseau est une ressource déployable individuellement, indépendante de toute ressource d’application ou de service qui pourrait y faire référence en tant que dépendance. Elles permet de créer un réseau privé pour vos applications. Plusieurs services de différentes applications peuvent faire partie d’un même réseau.

> [!NOTE]
> La préversion actuelle ne prend en charge que le mappage un-à-un entre les applications et les réseaux.

### <a name="volumes"></a>Volumes

Les volumes sont des répertoires montés à l’intérieur de vos instances de conteneur, que vous pouvez utiliser pour conserver l’état. La ressource de volume est une méthode déclarative pour décrire la façon dont un répertoire est monté et le stockage de sauvegarde qui y est associé.

## <a name="programming-models"></a>Modèles de programmation
Une ressource de service ne requiert qu’une image conteneur pour s’exécuter, qui est référencée dans les packages de code associés à la ressource. Vous pouvez exécuter n’importe quel code, écrit dans n’importe quel langage, à l’aide de n’importe quelle infrastructure au sein du conteneur sans avoir besoin de connaître ou d’utiliser des API spécifiques de Service Fabric mesh. 

Votre code d’application reste portable, même en dehors de Service Fabric mesh, et vos déploiements d’applications restent cohérents indépendamment du langage ou de l’infrastructure utilisés pour implémenter vos services. Que votre application soit ASP.NET Core, Go, ou simplement un ensemble de processus et de scripts, le modèle de déploiement de ressource Service Fabric mesh reste le même. 

## <a name="deployment"></a>Déploiement

Lors du déploiement sur Service Fabric mesh, les ressources sont déployées sur Azure en tant que modèles Azure Resource Manager via HTTP ou Azure CLI. 


## <a name="next-steps"></a>Étapes suivantes 
Pour en savoir plus sur Service Fabric mesh, voir la
- [Vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md)
