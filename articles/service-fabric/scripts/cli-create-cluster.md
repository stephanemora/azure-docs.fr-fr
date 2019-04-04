---
title: Exemple de déploiement du script de Azure CLI
description: Créez un cluster Service Fabric Linux sécurisé dans Azure à partir de Azure CLI.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 3a50d6672833ab1b4986e38ca1ddf24a8c33c562
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664452"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Création d’un cluster Service Fabric Linux sécurisé dans Azure

Cette commande crée un certificat auto-signé, l’ajoute à un coffre de clés et télécharge le certificat localement.  Le nouveau certificat est utilisé pour sécuriser le cluster lorsqu’il se déploie.  Vous pouvez également utiliser un certificat existant au lieu d’en créer un nouveau.  Dans les deux cas, le nom de sujet du certificat doit correspondre au domaine utilisé pour accéder au cluster Service Fabric. Cela est nécessaire pour la fourniture d’un certificat SSL pour les points de terminaison de gestion HTTPS du cluster et pour Service Fabric Explorer. Vous ne pouvez pas obtenir de certificat SSL auprès d’une autorité de certification pour le domaine `.cloudapp.azure.com`. Vous devez obtenir un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

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
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Crée un cluster Service Fabric.  |

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des exemples Service Fabric CLI supplémentaires pour Azure Service Fabric dans la rubrique [Exemples Service Fabric CLI](../samples-cli.md).
