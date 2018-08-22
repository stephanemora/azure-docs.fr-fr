---
title: Mettre à l’échelle des services dans une application Azure Service Fabric mesh | Microsoft Docs
description: Découvrez comment mettre à l’échelle de façon indépendante des services au sein d’une application s’exécutant sur Service Fabric mesh à l’aide d’Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038273"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Services de mise à l’échelle à l’intérieur d’une application s’exécutant sur Service Fabric mesh

Cet article explique comment mettre à l’échelle de façon indépendante des microservices à l’intérieur d’une application. Dans cet exemple, l’application Visual Objects se compose de deux microservices, `web` et `worker`.

Le service `web` est une application ASP.NET Core avec une page web affichant des triangles dans le navigateur. Le navigateur affiche un triangle pour chaque instance du service `worker`.

Le service `worker` déplace le triangle dans l’espace à un intervalle prédéfini, et envoie l’emplacement du triangle au service `web`. Il utilise DNS pour résoudre l’adresse du service `web`.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurer l’interface de ligne de commande Service Fabric Mesh

Pour accomplir cette tâche, vous pouvez utiliser le service Azure Cloud Shell ou une installation locale d’Azure CLI. Installez le module d’extension CLI de Service Fabric mesh en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure et définissez votre abonnement.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources dans lequel déployer l’application.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Déployer l’application avec un seul service worker

Créez votre application dans le groupe de ressources à l’aide de la commande `deployment create`.

L’exemple suivant déploie une application Linux à l’aide du [modèle mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Pour déployer une application Windows, utilisez le [modèle mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Les images conteneur de Windows sont plus grandes que celles de Linux, et leur déploiement peut nécessiter plus de temps.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

En quelques minutes, la commande devrait renvoyer :

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Ouvrir l’application

La commande de déploiement renvoie l’adresse IP publique du point de terminaison de service. À l’issue du déploiement de l’application, obtenez l’adresse IP publique du point de terminaison de service, et ouvrez-la dans un navigateur. Une page web avec un triangle animé s’affiche.

Le nom de la ressource réseau pour cette application est `visualObjectsNetwork`. Vous pouvez afficher des informations sur l’application telles que sa description, son emplacement, groupe de ressources, etc. à l’aide de la commande suivante :

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Mettre à l’échelle le service `worker`

Mettez à l’échelle le service `worker` en créant trois instances à l’aide de la commande suivante. L’exemple suivant déploie une application Linux à l’aide du [modèle mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Pour déployer une application Windows, utilisez le [modèle mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). N’oubliez pas que le déploiement d’images de conteneur plus grandes peut prendre plus de temps.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

Une fois l’application correctement déployée, le navigateur doit afficher une page web contenant trois triangles animés.

## <a name="delete-the-resources"></a>Supprimer les ressources

Supprimez régulièrement les ressources que vous n’utilisez plus dans Azure. Pour supprimer les ressources liées à cet exemple, supprimez le groupe de ressources dans lequel elles ont été déployées (ce qui supprime tous les éléments associés au groupe de ressources) avec la commande suivante :

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Regardez l’exemple d’application Visual Objects sur [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Pour en savoir plus sur le modèle de ressource Service Fabric, voir [Modèle de ressource Service Fabric mesh](service-fabric-mesh-service-fabric-resources.md).
- Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).