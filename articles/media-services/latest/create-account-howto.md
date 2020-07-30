---
title: Créer un compte Azure Media Services
description: Ce tutoriel vous guide à travers les étapes de création d’un compte Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: e32a9053e4ab7cc3618f7b50b7291a660a14e1b1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053490"
---
# <a name="create-a-media-services-account"></a>Créer un compte Media Services

Pour commencer à chiffrer, coder, analyser, gérer et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services. Un compte Media Services doit être associé à un ou plusieurs comptes de stockage.

> [!NOTE]
> Le compte Media Services et tous les comptes de stockage associés doivent être faire partie du même abonnement Azure. Il est fortement recommandé d’utiliser des comptes de stockage situés au même emplacement que le compte Media Services afin d’éviter des frais supplémentaires de sortie des données et une importante latence.

Cet article décrit les étapes à suivre pour créer un compte Azure Media Services. Choisissez parmi les onglets suivants.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Le portail Azure permet de créer rapidement un compte Azure Media Services. Vous pouvez utiliser ce dernier pour accéder à Media Services afin de stocker, de chiffrer, d’encoder, de gérer et de diffuser du contenu multimédia dans Azure.

Vous pouvez actuellement utiliser le portail [Azure](https://portal.azure.com/) pour :

* Gérer les [événements en direct](live-events-outputs-concept.md) Media Services v3. 
* Consulter (et non gérer) les [actifs multimédias](assets-concept.md) v3. 
* [Obtenir des informations sur l’accès aux API](./access-api-howto.md). 

Pour toutes les autres tâches de gestion (par exemple, les [transformations et travaux](transforms-jobs-concept.md) et la [protection de contenu](content-protection-overview.md)), utilisez l'[API REST](https://aka.ms/ams-v3-rest-ref), l'interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l'un des [kits de développement logiciel (SDK)](media-services-apis-overview.md#sdks) pris en charge.

Cet article montre comment créer un compte Media Services à l’aide du portail Azure.

### <a name="create-a-media-services-account"></a>Créer un compte Media Services

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Cliquez sur **+Créer une ressource** > **Média** > **Media Services**.
1. Dans la section **Créer un compte Media Services**, entrez les valeurs requises.
    
    | Nom | Description |
    | ---|---|
    |**Nom du compte**|Entrez le nom du nouveau compte Media Services. Un nom de compte Media Services se compose de lettres en minuscules ou de chiffres, sans espaces. Sa longueur est comprise entre 3 et 24 caractères.|
    |**Abonnement**|Si vous disposez de plusieurs abonnements, sélectionnez-en un dans la liste des abonnements Azure auxquels vous avez accès.|
    |**Groupe de ressources**|Sélectionnez la ressource (nouvelle ou existante). Un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies. En savoir plus [ici](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Lieu**|Sélectionnez la zone géographique à utiliser pour stocker les enregistrements multimédias et les métadonnées de votre compte Media Services. Cette région servira à traiter et diffuser vos médias. Seules les régions Media Services disponibles s’affichent dans la liste déroulante. |
    |**Compte de stockage**|Sélectionnez le compte de stockage qui fournira le stockage d'objets blob du contenu multimédia provenant de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.<br/><br/>Vous devez disposer d’un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Vous pouvez utiliser le portail Azure pour ajouter des comptes de stockage secondaires. Pour plus d'informations, consultez [Comptes de stockage Azure avec comptes Azure Media Services](storage-account-concept.md).<br/><br/>Le compte Media Services et tous les comptes de stockage associés doivent être faire partie du même abonnement Azure. Il est fortement recommandé d’utiliser des comptes de stockage situés au même emplacement que le compte Media Services afin d’éviter des frais supplémentaires de sortie des données et une importante latence.|
    
1. Sélectionnez **Épingler au tableau de bord** pour voir la progression du déploiement du compte.
1. Cliquez sur **Créer** en bas du formulaire.

    Une fois votre compte Media Services créé, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer le streaming de votre contenu et tirer parti de l'[empaquetage dynamique](dynamic-packaging-overview.md) et du [chiffrement dynamique](content-protection-overview.md), le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l'état **En cours d'exécution**. 

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Configurer l’abonnement Azure

Dans la commande suivante, fournissez l’ID d’abonnement Azure que vous souhaitez utiliser pour le compte Media Services. Vous pouvez afficher la liste des abonnements auxquels vous avez accès à en accédant à [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de la commande suivante. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources comme les comptes Azure Media Services et les comptes de stockage associés sont déployées et gérées.

Vous pouvez remplacer `amsResourceGroup` par votre valeur.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. Pour plus d’informations sur l’utilisation des comptes de stockage dans Media Services, consultez [Comptes de stockage](storage-account-concept.md).

Vous devez avoir un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Media Services prend en charge les comptes **v2 à usage général** (GPv2) ou **v1 à usage général** (GPv1). Les comptes Blob uniquement ne sont pas autorisés en tant que comptes **principaux**. Pour plus d’informations sur les comptes de stockage, consultez [Options du compte de stockage Azure](../../storage/common/storage-account-overview.md). 

Dans cet exemple, nous créons un compte v2 universel, LRS standard. Si vous voulez faire des expériences avec des comptes de stockage, utilisez `--sku Standard_LRS`. Cependant, lors de la sélection d’une référence SKU pour la production, envisagez `--sku Standard_RAGRS`, qui offre la réplication géographique pour la continuité de l’activité. Pour plus d’informations, consultez [Comptes de stockage](/cli/azure/storage/account?view=azure-cli-latest).
 
La commande suivante crée un compte de stockage qui sera associé au compte Media Services. Dans le script ci-dessous, vous pouvez remplacer `storageaccountforams` par votre valeur. `amsResourceGroup` doit correspondre à la valeur que vous avez donnée au groupe de ressources à l’étape précédente. La longueur du nom du compte de stockage doit être inférieure à 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Créer un compte Media Services

La commande suivante de Azure CLI crée un nouveau compte Media Services. Vous pouvez remplacer les valeurs suivantes : `amsaccount` `storageaccountforams` (doit correspondre à la valeur donnée pour votre compte de stockage), et `amsResourceGroup` (doit correspondre à la valeur donnée pour le groupe de ressources).

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Voir aussi

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Attacher un stockage secondaire à un compte Media Services](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)
