---
title: Afficher des journaux de conteneurs dans Azure Service Fabric | Microsoft Docs
description: Décrit comment afficher des fichiers journaux de conteneurs de services Service Fabric en cours d’exécution à l’aide de Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Afficher les fichiers journaux d’un service de conteneurs Service Fabric
Azure Service Fabric, qui est un orchestrateur de conteneurs, prend en charge les [conteneurs Linux et Windows](service-fabric-containers-overview.md).  Cet article décrit comment afficher les fichiers journaux de conteneurs d’un service de conteneurs en cours d’exécution, de manière à ce que vous puissiez diagnostiquer et réparer les problèmes.

## <a name="access-container-logs"></a>Accéder aux fichiers journaux de conteneurs
Les fichiers journaux de conteneurs sont accessibles via [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Dans un navigateur web, ouvrez Service Fabric Explorer à partir du point de terminaison de gestion du cluster, en accédant à [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Les fichiers journaux de conteneurs sont hébergés sur le nœud de cluster sur lequel est exécutée l’instance du service de conteneurs. Par exemple, récupérez les journaux du conteneur web frontal de l’[exemple d’application Linux Voting](service-fabric-quickstart-containers-linux.md). Dans l’arborescence, développez **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**.  Ensuite, développez la partition (d1aa737e-f22a-e347-be16-eec90be24bc1, dans cet exemple) et vérifiez que le conteneur est exécuté sur le nœud de cluster *_lnxvm_0*.

Dans l’arborescence, recherchez le package de code sur le nœud *_lnxvm_0*, en développant **Nœuds**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Packages de code**>**code**.  Ensuite, sélectionnez **Journaux de conteneur** afin d’afficher les fichiers journaux de conteneurs.

![Plateforme Service Fabric][Image1]


## <a name="next-steps"></a>Étapes suivantes
- Exécutez le [didacticiel sur la création d’une application conteneur Linux](service-fabric-tutorial-create-container-images.md).
- En savoir plus sur [Service Fabric et les conteneurs](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
