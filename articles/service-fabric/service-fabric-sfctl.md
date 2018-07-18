---
title: 'CLI Azure Service Fabric : sfctl | Microsoft Docs'
description: Décrit les commandes sfctl de l’interface de ligne de commande (CLI) Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 456257e54da83ac629039b714cc74f9dafda2174
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763270"
---
# <a name="sfctl"></a>sfctl
Commandes permettant de gérer les clusters et entités Service Fabric. Cette version est compatible avec le runtime Service Fabric 6.2.

Les commandes suivent le modèle nom-verbe. Pour plus d’informations, consultez la section Sous-groupes.

## <a name="subgroups"></a>Sous-groupes
|Sous-groupe|Description|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Permet de créer, de supprimer et de gérer les applications et les types d’application. |
| [chaos](service-fabric-sfctl-chaos.md) | Permet de démarrer, d’arrêter et de créer des rapports sur le service de test chaos. |
| [cluster](service-fabric-sfctl-cluster.md) | Permet de sélectionner, de gérer et d’utiliser les clusters Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Permet de créer, de supprimer et de gérer les applications Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Permet d’exécuter les commandes relatives au conteneur sur un nœud de cluster. |
| [is](service-fabric-sfctl-is.md) | Interroge et envoie des commandes vers le service d’infrastructure. |
| [node](service-fabric-sfctl-node.md) | Permet de gérer les nœuds qui forment un cluster. |
| [partition](service-fabric-sfctl-partition.md) | Interroge et gère des partitions pour tout service. |
| [property](service-fabric-sfctl-property.md) | Stocke et interroge des propriétés avec des noms Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Permet de gérer les réplicas qui font partie des partitions de service. |
| [rpm](service-fabric-sfctl-rpm.md) | Interroge et envoie des commandes vers le service gestionnaire de réparation. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Permet de gérer les clusters Service Fabric autonomes. |
| [service](service-fabric-sfctl-service.md) | Permet de créer, de supprimer et de gérer le service, les types de service et les packages de services. |
| [store](service-fabric-sfctl-store.md) | Effectue des opérations élémentaires au niveau des fichiers dans le magasin d’images de cluster. |

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).