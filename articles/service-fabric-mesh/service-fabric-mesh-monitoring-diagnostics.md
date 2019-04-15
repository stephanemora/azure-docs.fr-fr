---
title: Surveillance et diagnostics dans les applications Azure Service Fabric mesh | Microsoft Docs
description: Découvrez l’application de surveillance et de diagnostic dans Service Fabric mesh sur Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f857c23b5500bc7790a0ff7fcf81eaec51f37c9
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358276"
---
# <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics
Azure Service Fabric mesh est un service entièrement géré qui permet aux développeurs de déployer des applications de microservices sans gestion de machines virtuelles, de stockage ou de mise en réseau. La surveillance et les diagnostics pour Service Fabric mesh portent sur trois types principaux de données de diagnostic :

- Journaux des applications : ils sont définis comme les journaux d’activité de vos applications en conteneur, selon la façon dont vous avez instrumenté votre application (par exemple, journaux d’activité Docker).
- Événements de plateforme : événements de la plateforme Mesh pertinents pour votre opération de conteneur. Ils incluent actuellement l’activation, la désactivation et l’arrêt de conteneur.
- Métriques de conteneur : métriques de l’utilisation et des performances des ressources pour vos conteneurs (statistiques Docker).

Cet article décrit les options de surveillance et de diagnostic pour la dernière préversion disponible.

## <a name="application-logs"></a>Journaux d’activité d’application

Vous pouvez afficher les journaux d’activité Docker de vos conteneurs déployés, séparément pour chaque conteneur. Dans le modèle d’application Service Fabric mesh, chaque conteneur est un package de code dans votre application. Pour afficher les journaux d’activité associés avec un package de code, utilisez la commande suivante :

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Vous pouvez utiliser la commande « az mesh service-replica » pour obtenir le nom du réplica. Les noms de réplica sont des numéros incrémentés à partir de 0.*

Voici à quoi cela ressemble pour l’affichage de journaux d’activité du conteneur VotingWeb.Code de l’application de vote :

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Service Fabric mesh, voir la
- [Vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md)
