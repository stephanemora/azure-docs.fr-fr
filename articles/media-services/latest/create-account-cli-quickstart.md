---
title: 'Démarrage rapide : Créer un compte Azure Media Services avec Azure CLI | Microsoft Docs'
description: Suivez les étapes de ce démarrage rapide pour créer un compte Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: abed9fd8d466b582b534b365f4be4257f4986435
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736000"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Démarrage rapide : créer un compte Azure Media Services

> [!NOTE]
> La dernière version d’Azure Media Services (30/03/2018) est préversion. Cette version est également appelée v3. 

Que vous soyez un développeur ou un créateur de contenu multimédia, pour stocker, chiffrer, encoder, gérer et diffuser du contenu multimédia dans Azure, vous devez créer un compte Media Services. Lorsque vous créez un compte Media Services, vous devez fournir l’ID d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. Cette ressource de compte de stockage doit se trouver dans la même zone géographique que le compte Media Services.  

Ce démarrage rapide décrit les étapes pour créer un compte Azure Media Services à l’aide d’Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](http://portal.azure.com) et lancez **CloudShell** pour exécuter les commandes CLI, comme indiqué dans les étapes suivantes.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0 ou une version ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Configurer l’abonnement Azure

Dans la commande suivante, fournissez l’ID d’abonnement Azure que vous souhaitez utiliser pour le compte Media Services. Vous pouvez afficher la liste des abonnements auxquels vous avez accès à en accédant à [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Créer un groupe de ressources Azure

La commande suivante crée un groupe de ressources dans lequel vous souhaitez avoir le compte de stockage et le compte Media Services. Remplacez l’espace réservé *myresourcegroup* par le nom que vous souhaitez utiliser pour votre groupe de ressources.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Lorsque vous créez un compte Media Services, vous devez fournir l’ID d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. 

Vous devez disposer d’un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Media Services prend en charge les comptes **v2 à usage général** ou **v1 à usage général**. Les comptes de stockage d’objets blob ne sont pas autorisés en tant que comptes **principaux**. Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](../../storage/common/storage-account-overview.md). 

La commande suivante crée le compte de stockage qui sera associé au compte Media Services (principal). Dans le script ci-dessous, remplacez l’espace réservé *storageaccountforams*. La longueur du « nom_du_compte » doit être inférieure à 24.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Créer un compte Azure Media Services

Vous trouverez ci-dessous les commandes Azure CLI qui créent un nouveau compte Media Services. Il vous suffit de remplacer les valeurs en surbrillance suivantes :

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment le compte Media Services que vous avez créé dans ce guide de démarrage rapide, supprimez le groupe de ressources.

Dans **CloudShell**, exécutez la commande suivante :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Diffuser un fichier](stream-files-dotnet-quickstart.md)
