---
title: Stocker un état dans une application Service Fabric Mesh en montant un volume basé sur Azure Files à l’intérieur du conteneur | Microsoft Docs
description: Découvrez comment stocker un état dans une application Service Fabric Mesh en montant un volume basé sur Azure Files dans le conteneur avec Azure CLI.
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038098"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Stocker un état dans une application Service Fabric Mesh en montant un volume basé sur Azure Files à l’intérieur du conteneur

Cet article montre comment stocker un état dans Azure Files en montant un volume à l’intérieur du conteneur d’une application Service Fabric mesh. Dans cet exemple, l’application de compteur dispose d’un service ASP.NET Core, avec une page web qui affiche la valeur du compteur dans un navigateur. 

`counterService` lit périodiquement une valeur de compteur dans un fichier, l’incrémente, puis la réécrit dans le fichier. Le fichier est stocké dans un dossier monté sur le volume soutenu par un partage Azure Files.

## <a name="prerequisites"></a>Prérequis

Pour accomplir cette tâche, vous pouvez utiliser le service Azure Cloud Shell ou une installation locale d’Azure CLI. Pour utiliser Azure CLI avec cet article, vérifiez que `az --version` retourne au moins `azure-cli (2.0.43)`.  Installez (ou mettez à jour) le module d’extension Service Fabric Mesh CLI en suivant les [instructions](service-fabric-mesh-howto-setup-cli.md) ci-après.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure et définissez votre abonnement.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Créer un partage de fichiers

Créez un partage de fichiers Azure en suivant ces [instructions](/azure/storage/files/storage-how-to-create-file-share). Le nom du compte de stockage, la clé du compte de stockage et le nom du partage de fichiers sont référencés respectivement en tant que `<storageAccountName>`, `<storageAccountKey>` et `<fileShareName>` dans les instructions suivantes. Ces valeurs sont disponibles dans votre portail Azure :
* <storageAccountName> - Sous **Comptes de stockage**, il s’agit du nom du compte de stockage que vous avez utilisé lorsque vous avez créé le partage de fichiers.
* <storageAccountKey> - Sélectionnez votre compte de stockage sous **Comptes de stockage**, puis sélectionnez **Clés d’accès** et utilisez la valeur sous **key1**.
* <fileShareName> - Sélectionnez votre compte de stockage sous **Comptes de stockage**, puis sélectionnez **Fichiers**. Le nom à utiliser est le nom du partage de fichiers que vous venez de créer.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources dans lequel déployer l’application. La commande suivante crée un groupe de ressources nommé `myResourceGroup` dans un emplacement situé dans l’Est des États-Unis.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Déployer le modèle

Créez l’application et les ressources associées au moyen de la commande suivante, et fournissez les valeurs pour `storageAccountName`, `storageAccountKey` et `fileShareName` de l’étape précédente [Créer un partage de fichiers](#create-a-file-share).

Le paramètre `storageAccountKey` dans le modèle est une chaîne sécurisée. Il n’apparaîtra pas dans l’état du déploiement et les commandes `az mesh service show`. Assurez-vous qu’il est correctement spécifié dans la commande suivante.

La commande suivante déploie une application Linux à l’aide du [modèle mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Pour déployer une application Windows, utilisez le [modèle mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). N’oubliez pas que le déploiement d’images de conteneur plus grandes peut prendre plus de temps.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Dans quelques minutes, votre commande devrait retourner le message suivant `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Ouvrir l’application

La commande de déploiement doit retourner l’adresse IP publique du point de terminaison de service. À l’issue du déploiement de l’application, récupérez l’adresse IP publique du point de terminaison de service et ouvrez-la dans un navigateur. Une page web doit s’afficher avec la valeur du compteur mise à jour toutes les secondes.

Le nom de la ressource réseau pour cette application est `counterAppNetwork`. Vous pouvez afficher des informations sur l’application, telles que sa description, son emplacement, son groupe de ressources, etc. à l’aide de la commande suivante :

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Vérifier que l’application est capable d’utiliser le volume

L’application crée un fichier nommé `counter.txt` dans le partage de fichiers à l’intérieur du dossier `counter/counterService`. Le contenu de ce fichier est la valeur du compteur affiché sur la page web.

Le fichier peut être téléchargé à l’aide de n’importe quel outil permettant de parcourir un partage de fichiers Azure Files, tel que l’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Supprimer les ressources

Supprimez régulièrement les ressources que vous n’utilisez plus dans Azure. Pour supprimer les ressources liées à cet exemple, supprimez le groupe de ressources dans lequel elles ont été déployées (ce qui supprime tous les éléments associés au groupe de ressources) avec la commande suivante :

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Affichez l’exemple d’application de volume Azure Files sur [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Pour en savoir plus sur le modèle de ressource Service Fabric, voir [Modèle de ressource Service Fabric mesh](service-fabric-mesh-service-fabric-resources.md).
- Pour en savoir plus sur Service Fabric mesh, voir la [vue d’ensemble de Service Fabric mesh](service-fabric-mesh-overview.md).