---
title: Exemple de déploiement de script de l’interface CLI Azure Service Fabric (sfctl)
description: Déployer une application sur un cluster Azure Service Fabric via l’interface CLI Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 7e2bd212e88b3efcf7f3aad3ef1555b3fc48ef1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641728"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Déployer une application sur un cluster Service Fabric

Cet exemple de script copie un package d’application dans le magasin d’images d’un cluster, inscrit le type d’application dans le cluster et crée une instance d’application à partir du type d’application. Tous les services par défaut sont également créés à ce stade.

Si nécessaire, installez l’[interface CLI Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Lorsque vous avez terminé, vous pouvez utiliser le script [remove](cli-remove-application.md) pour supprimer l’application. Ce script supprime l’instance d’application, annule l’inscription du type d’application et supprime le package d’application du magasin d’images.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [documentation relative à Service Fabric CLI](../service-fabric-cli.md).

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).
