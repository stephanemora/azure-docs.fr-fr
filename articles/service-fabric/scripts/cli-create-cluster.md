---
title: Exemple de déploiement du script de Azure CLI
description: Guide pratique pour créer un cluster Service Fabric Linux sécurisé dans Azure à l’aide de l’interface de ligne de commande (CLI) Azure.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b454ab7396b8185e344944d7ff526414540032e2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258930"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Création d’un cluster Service Fabric Linux sécurisé dans Azure

Cette commande crée un certificat auto-signé, l’ajoute à un coffre de clés et télécharge le certificat localement.  Le nouveau certificat est utilisé pour sécuriser le cluster lorsqu’il se déploie.  Vous pouvez également utiliser un certificat existant au lieu d’en créer un nouveau.  Dans les deux cas, le nom de sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour fournir un certificat TLS à Service Fabric Explorer et aux points de terminaison de gestion HTTPS du cluster. Vous ne pouvez pas obtenir de certificat TLS/SSL auprès d’une autorité de certification pour le domaine `.cloudapp.azure.com`. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Si nécessaire, installez l’Interface de ligne de commande Azure ([Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)).

## <a name="sample-script"></a>Exemple de script

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, le cluster et toutes les ressources associées.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) | Crée un cluster Service Fabric.  |

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).
