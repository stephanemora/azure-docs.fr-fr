---
title: Stocker un état en montant un volume basé sur Azure Files à l’intérieur du conteneur dans une application Service Fabric mesh | Microsoft Docs
description: Découvrez comment stocker un état en montant un volume basé sur Azure Files à l’intérieur du conteneur dans une application Service Fabric mesh à l’aide d’Azure CLI.
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
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090630"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Stocker un état en montant un volume basé sur Azure Files dans une application Service Fabric mesh

Cet article montre comment stocker un état dans Azure Files en montant un volume à l’intérieur du conteneur d’une application Service Fabric mesh. Dans cet exemple, un compteur d’application a un service ASP.NET Core avec une page web qui affiche la valeur du compteur dans un navigateur. 

Le `counterService` lit périodiquement une valeur de compteur dans un fichier, l’incrémente, puis la réécrit dans le fichier. Le fichier est stocké dans un dossier monté sur le volume soutenu par un partage Azure Files. 

## <a name="set-up-service-fabric-mesh-cli"></a>Configurer l’interface de ligne de commande Service Fabric Mesh 
Pour accomplir cette tâche, vous pouvez utiliser le service Azure Cloud Shell ou une installation locale d’Azure CLI. Installez le module d’extension CLI de Service Fabric mesh en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous à Azure et définissez votre abonnement.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Créer un partage de fichiers 
Créez un partage de fichiers Azure en suivant ces [instructions](/azure/storage/files/storage-how-to-create-file-share). Le nom du compte de stockage, la clé du compte de stockage et le nom du partage de fichiers sont référencés respectivement en tant que `<storageAccountName>`, `<storageAccountKey>` et `<fileShareName>` dans les instructions suivantes.

## <a name="create-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources dans lequel déployer l’application. Vous pouvez utiliser un groupe de ressources existant et ignorer cette étape. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Déployer le modèle

Créez l’application et les ressources associées à l’aide de la commande suivante, et fournissez les valeurs pour `storageAccountName`, `storageAccountKey` et `fileShareName` de l’étape précédente.

Le paramètre `storageAccountKey` dans le modèle est un `securestring`. Il n’apparaîtra pas dans l’état du déploiement et les commandes `az mesh service show`. Assurez-vous qu’il est correctement spécifié dans la commande suivante.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

La commande précédente déploie une application Linux à l’aide du [modèle mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Si vous souhaitez déployer une application Windows, utilisez le [modèle mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Les images conteneur de Windows sont plus grandes que celles de Linux, et leur déploiement peut nécessiter plus de temps.

Dans quelques minutes, votre commande devrait renvoyer :

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Ouvrir l’application
À l’issue du déploiement de l’application, obtenez l’adresse IP publique du point de terminaison de service, et ouvrez-la dans un navigateur. Celui-ci affiche une page web avec la valeur du compteur mise à jour toutes les secondes.

La commande de déploiement renvoie l’adresse IP publique du point de terminaison de service. Si vous le souhaitez, vous pouvez également interroger la ressource réseau pour trouver l’adresse IP publique du point de terminaison de service. 
 
Le nom de la ressource réseau pour cette application est `counterAppNetwork`. Extrayez les informations la concernant à l’aide de la commande suivante. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Vérifier que l’application est capable d’utiliser le volume
L’application crée un fichier nommé `counter.txt` dans le partage de fichiers à l’intérieur du dossier `counter/counterService`. Le contenu de ce fichier est la valeur du compteur affiché sur la page web.

Le fichier peut être téléchargé à l’aide de n’importe quel outil permettant de parcourir un partage de fichiers Azure Files. L’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) est un exemple d’un tel outil.

## <a name="delete-the-resources"></a>Supprimer les ressources

Pour économiser les ressources limitées affectées au programme en préversion, supprimez les ressources fréquemment. Pour supprimer les ressources liées à cet exemple, supprimez le groupe de ressources dans lequel elles ont été déployées.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Étapes suivantes

- Affichez l’exemple d’application de volume Azure Files sur [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Pour en savoir plus sur le modèle de ressource Service Fabric, voir [Modèle de ressource Service Fabric mesh](service-fabric-mesh-service-fabric-resources.md).
- Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).
