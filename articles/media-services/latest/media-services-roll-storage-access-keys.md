---
title: Mettre à jour Media Services v3 après la substitution de clés d’accès de stockage | Microsoft Docs
description: Cet article vous donne des conseils sur la mise à jour de Media Services v3 après la substitution de clés d’accès de stockage.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572235"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Mettre à jour Media Services v3 après la substitution de clés d’accès de stockage

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Vous êtes invité à sélectionner un compte de stockage Azure lorsque vous créez un nouveau compte Azure Media Services (AMS).  Vous pouvez ajouter plusieurs comptes de stockage à votre compte Media Services. Cet article explique comment orchestrer la rotation des clés de stockage. Elle montre également comment ajouter des comptes de stockage à un compte multimédia.

Pour terminer les actions décrites dans cet article, vous devez utiliser des [API Azure Resource Manager](/rest/api/media/operations/azure-media-services-rest-api-reference) et [Powershell](/powershell/module/az.media).  Pour plus d’informations, consultez [Guide pratique pour gérer les ressources Azure avec PowerShell et Resource Manager](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Génération de clé d’accès de stockage

Lors de la création d’un nouveau compte de stockage, Azure génère deux clés d’accès de stockage 512 bits, qui sont utilisées pour authentifier l’accès à votre compte de stockage. Pour sécuriser vos connexions de stockage, régénérez et alternez périodiquement votre clé d’accès de stockage. Vous bénéficiez de deux clés d’accès (primaire et secondaire) pour vous permettre de conserver vos connexions au compte de stockage à l’aide d’une clé d’accès lorsque vous régénérez l’autre clé. Cette procédure est également appelée « substitution des clés d’accès ».

Media Services dépend d'une clé de stockage qui lui est fournie. Plus précisément, les localisateurs utilisés pour diffuser en continu ou télécharger vos ressources dépendent de la clé d’accès de stockage spécifiée. Lorsqu’un compte AMS est créé, il est dépendant de la clé d’accès de stockage primaire par défaut. Toutefois, en tant qu’utilisateur, vous pouvez mettre à jour la clé de stockage qu’AMS possède. Vous devez laisser Media Services savoir quelle clé utiliser en procédant comme suit :

>[!NOTE]
> Si vous possédez plusieurs comptes de stockage, vous devez effectuer cette procédure pour chacun d’eux. L’ordre de régénération des clés de stockage n’est pas fixe. Vous pouvez commencer par la clé primaire ou la clé secondaire.
>
> Avant d’appliquer les étapes sur un compte de production, veillez à les tester sur un compte de pré-production.
>

## <a name="steps-to-rotate-storage-keys"></a>Étapes de régénération des clés de stockage
 
 1. Modifiez la clé primaire du compte de stockage par le biais du cmdlet PowerShell ou du Portail [Azure](https://portal.azure.com/).
 2. Appeler le cmdlet `Sync-AzMediaServiceStorageKeys` avec les paramètres appropriés pour forcer le compte de média à récupérer les clés de compte de stockage
 
    L’exemple suivant montre comment synchroniser des clés avec des comptes de stockage.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Attendez environ une heure. Vérifiez que les scénarios de streaming fonctionnent.
 4. Modifiez la clé secondaire du compte de stockage par le biais du cmdlet PowerShell ou du Portail Azure.
 5. Appeler PowerShell `Sync-AzMediaServiceStorageKeys` avec les paramètres appropriés pour forcer le compte de média à récupérer les nouvelles clés de compte de stockage.
 6. Attendez environ une heure. Vérifiez que les scénarios de streaming fonctionnent.
 
### <a name="a-powershell-cmdlet-example"></a>Exemple de cmdlet PowerShell

L’exemple suivant montre comment obtenir le compte de stockage et le synchroniser avec le compte AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Étapes à suivre pour ajouter des comptes de stockage à votre compte AMS

L’article suivante montre comment ajouter des comptes de stockage à votre compte AMS : [Associez plusieurs comptes de stockage à un compte Media Services](media-services-managing-multiple-storage-accounts.md).
