---
title: Mettre à l’échelle des services dans une application Azure Service Fabric mesh | Microsoft Docs
description: Découvrez comment mettre à l’échelle de façon indépendante des services au sein d’une application s’exécutant sur Service Fabric mesh à l’aide d’Azure CLI.
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a4260fd808643971036ad87c01bd2fdec299ccc6
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089741"
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
Créez un groupe de ressources dans lequel déployer l’application. Vous pouvez utiliser un groupe de ressources existant et ignorer cette étape. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Déployer l’application avec un seul service worker
Créez votre application dans le groupe de ressources à l’aide de la commande `deployment create`.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
La commande précédente déploie une application Linux à l’aide du [modèle mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Si vous souhaitez déployer une application Windows, utilisez un [modèle mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Les images conteneur de Windows sont plus grandes que celles de Linux, et leur déploiement peut nécessiter plus de temps.

Dans quelques minutes, votre commande devrait renvoyer :

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Ouvrir l’application
À l’issue du déploiement de l’application, obtenez l’adresse IP publique du point de terminaison de service, et ouvrez-la dans un navigateur. Celui-ci affiche une page web contenant un triangle se déplaçant dans l’espace.

La commande de déploiement renvoie l’adresse IP publique du point de terminaison de service. Si vous le souhaitez, vous pouvez également interroger la ressource réseau pour trouver l’adresse IP publique du point de terminaison de service. 
 
Le nom de la ressource réseau pour cette application est `visualObjectsNetwork`. Extrayez les informations la concernant à l’aide de la commande suivante. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Mettre à l’échelle le service `worker`

Mettez à l’échelle le service `worker` en créant trois instances à l’aide de la commande suivante. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
La commande précédente déploie une application Linux à l’aide du [modèle mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Si vous souhaitez déployer une application Windows, utilisez le [modèle mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Les images conteneur de Windows sont plus grandes que celles de Linux, et leur déploiement peut nécessiter plus de temps.

Une fois l’application correctement déployée, le navigateur doit afficher une page web contenant trois triangles se déplaçant dans l’espace.

## <a name="delete-the-resources"></a>Supprimer les ressources

Pour économiser les ressources limitées affectées au programme en préversion, supprimez les ressources fréquemment. Pour supprimer les ressources liées à cet exemple, supprimez le groupe de ressources dans lequel elles ont été déployées.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes
- Regardez l’exemple d’application Visual Objects sur [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Pour en savoir plus sur le modèle de ressource Service Fabric, voir [Modèle de ressource Service Fabric mesh](service-fabric-mesh-service-fabric-resources.md).
- Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).