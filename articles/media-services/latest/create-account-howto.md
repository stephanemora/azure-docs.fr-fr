---
title: Créer un compte Azure Media Services
description: Ce tutoriel vous guide à travers les étapes de création d’un compte Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49cac230363750e481e165712bf4f619e5cba7ae
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017843"
---
# <a name="create-a-media-services-account"></a>Créer un compte Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pour commencer à chiffrer, coder, analyser, gérer et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services. Un compte Media Services doit être associé à un ou plusieurs comptes de stockage. Cet article décrit les étapes à suivre pour créer un compte Azure Media Services.

> [!NOTE]
> Le compte Media Services et tous les comptes de stockage associés doivent être faire partie du même abonnement Azure. Il est fortement recommandé d’utiliser des comptes de stockage situés au même emplacement que le compte Media Services afin d’éviter des frais supplémentaires de sortie des données et une importante latence.

 Vous pouvez utiliser le portail Azure ou l’interface CLI pour créer un compte Media Services. Choisissez l’onglet de la méthode que vous souhaitez utiliser.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portail](#tab/portal/)

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Le portail Azure permet de créer rapidement un compte Azure Media Services. Vous pouvez utiliser ce dernier pour accéder à Media Services afin de stocker, de chiffrer, d’encoder, de gérer et de diffuser du contenu multimédia dans Azure.

Vous pouvez actuellement utiliser le portail [Azure](https://portal.azure.com/) pour :

* Gérer les [événements en direct](live-events-outputs-concept.md) Media Services v3. 
* Consulter (et non gérer) les [actifs multimédias](assets-concept.md) v3. 
* [Obtenir des informations sur l’accès aux API](./access-api-howto.md). 

Pour toutes les autres tâches de gestion (par exemple, les [transformations et travaux](transforms-jobs-concept.md) et la [protection de contenu](content-protection-overview.md)), utilisez l’[API REST](/rest/api/media/accountfilters), l’interface [CLI](/cli/azure/ams) ou l’un des [SDK](media-services-apis-overview.md#sdks) pris en charge.
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Utiliser le portail Azure pour créer un compte Media Services

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Cliquez sur **+ Créer une ressource** > **Média** > **Media Services**.
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

## <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli/)

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Configurer l’abonnement Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Créer un compte Media Services

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Voir aussi

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Attacher un stockage secondaire à un compte Media Services](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Étapes suivantes

[Diffuser un fichier](stream-files-dotnet-quickstart.md)