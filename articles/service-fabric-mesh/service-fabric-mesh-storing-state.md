---
title: Options de stockage d’état sur Azure Service Fabric mesh | Microsoft Docs
description: Découvrez comment stocker un état de façon fiable dans des applications Service Fabric mesh s’exécutant sur Azure Service Fabric mesh.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075508"
---
# <a name="state-management-with-service-fabric"></a>Gestion d’état avec Service Fabric
Service Fabric prend en charge de nombreuses option de stockage d’état. Pour une vue d’ensemble conceptuelle des modèles de gestion d’état et de Service Fabric, voir [Concepts de Service Fabric : état](/azure/service-fabric/service-fabric-concepts-state). Tous ces concepts s’appliquent, que vos services s’exécutent à l’intérieur ou à l’extérieur de Service Fabric mesh. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Options de stockage d’état dans Azure Service Fabric mesh
Avec Service Fabric mesh, vous pouvez facilement déployer une nouvelle application et la connecter à un magasin de données existant hébergé dans Azure. En plus la possibilité d’utiliser toute base de données distante, il existe plusieurs options pour le stockage de données, selon que le service désire un stockage local ou distant. 

* Données répliquées stockées localement
  * Collections fiables (non disponibles en préversion)
    * Bibliothèque qui implémente des structures de données telles que des files d’attente et des paires clé-valeur à utiliser dans le service.
    * Cela constitue le moyen le plus simple et le plus rapide pour interagir avec des données, tout en offrant un routage de partition aisé en combinaison avec le routage intelligent dans Service Fabric mesh.
  * Pilote de volume Service Fabric (non disponible en préversion)
    * Pilote de volume Docker pour monter un volume local sur un conteneur.
    * Offre une flexibilité sans égale pour le stockage local des données, via toute API prenant en charge le stockage de fichiers.

* Stockage distant
  * Pilote de volume Azure Files
    * Pilote de volume Docker pour monter un partage de fichiers Azure sur un conteneur.
    * Vous donne une option pour les données, certes moins performante mais moins chère, totalement flexible et fiable, via n’importe quelle API prenant en charge le stockage de fichiers.
    * [Guide pratique : Déployer une application avec volume Azure Files](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le modèle d’application, voir [Ressources Service Fabric](service-fabric-mesh-service-fabric-resources.md)
