---
title: Déployer une application sur Azure Service Fabric mesh à l’aide d’un modèle | Microsoft Docs
description: Découvrez comment déployer une application .NET Core sur Service Fabric mesh à partir d’un modèle en utilisant Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038249"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Déployer une application Service Fabric mesh sur Service Fabric mesh à l’aide d’un modèle
Cet article explique comment déployer une application .NET Core sur Service Fabric mesh à l’aide d’un modèle. Lorsque vous avez terminé, vous disposez d’une application de vote avec un composant web frontal ASP.NET Core qui enregistre les résultats de vote dans un service principal dans le cluster. Le composant frontal utilise DNS pour résoudre l’adresse du service principal.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurer l’interface de ligne de commande Service Fabric Mesh 
Pour accomplir cette tâche, vous pouvez utiliser le service Azure Cloud Shell ou une installation locale d’Azure CLI. Installez le module d’extension CLI de Service Fabric mesh en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous à Azure et définissez votre abonnement.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources dans lequel déployer l’application. Vous pouvez utiliser un groupe de ressources existant et ignorer cette étape. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Déployer l’application
Créez votre application dans le groupe de ressources à l’aide de la commande `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

La commande précédente déploie une application Windows à l’aide du [modèle mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Si vous souhaitez déployer une application Linux, utilisez le [modèle mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Les images conteneur de Windows sont plus grandes que celles de Linux, et leur déploiement peut nécessiter plus de temps.

Dans quelques minutes, votre commande devrait renvoyer :

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Ouvrir l’application
À l’issue du déploiement de l’application, obtenez l’adresse IP publique du point de terminaison de service, et ouvrez-la dans un navigateur. Celui-ci affiche la page web suivante. 

![Application de vote](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Vous pouvez à présent ajouter des options de vote à l’application, et voter sur celle-ci ou supprimer les options de vote.

La commande de déploiement renvoie l’adresse IP publique du point de terminaison de service. Si vous le souhaitez, vous pouvez également interroger la ressource réseau pour trouver l’adresse IP publique du point de terminaison de service. 

Le nom de la ressource réseau pour cette application est `VotingAppNetwork`. Extrayez les informations la concernant à l’aide de la commande suivante. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Vérifier les détails de l’application
Vous pouvez vérifier l’état de l’application à l’aide de la commande `app show`. Le nom d’application pour l’application déployée est « VotingApp ». Donc, extrayez ses détails. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Répertorier les applications déployées
Vous pouvez utiliser la commande « app list » pour obtenir la liste des applications que vous avez déployées sur votre abonnement. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Supprimer les ressources
Lorsque vous n’avez plus besoin de l’application et de ses ressources, supprimez le groupe de ressources qui les contient. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Étapes suivantes
- Regardez l’exemple d’application de vote sur [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).


