---
title: Exemple de suppression de script Azure Service Fabric CLI (sfctl)
description: Supprimer une application d’un cluster Azure Service Fabric via l’interface CLI Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 83f2fc52debd24afd97a391466cb5a0b1a8cd93c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642714"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Supprimer une application d’un cluster Service Fabric

Cet exemple de script supprime une instance d’application Service Fabric en cours d’exécution, puis annule l’inscription d’un type d’application et de la version du cluster.  La suppression de l’instance d’application entraîne également celle de toutes les instances de service en cours d’exécution associées à cette application. Ensuite, les fichiers d’application sont supprimés du magasin d’images. 

Si nécessaire, installez l’[interface CLI Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à Service Fabric CLI](../service-fabric-cli.md).

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).
