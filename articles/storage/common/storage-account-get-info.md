---
title: Obtenez le type de compte de stockage et le nom de référence SKU avec .NET – Stockage Azure
description: Découvrez comment obtenir le type de compte de stockage Azure et le nom de référence SKU à l’aide de la bibliothèque cliente .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828877"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Obtenez le type de compte de stockage et le nom de référence SKU avec .NET

Cet article explique comment obtenir le type de compte de stockage Azure et le nom de référence (SKU) pour un objet blob à l'aide de la [bibliothèque cliente de stockage Azure pour .NET](/dotnet/api/overview/azure/storage/client).

Les informations de compte sont disponibles sur les versions de service à partir de la version 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>À propos du type de compte et du nom de référence SKU

**Type de compte** : Les types de comptes valides incluent `BlobStorage`, `BlockBlobStorage`, `FileStorage`, `Storage` et `StorageV2`. [Vue d’ensemble du compte de stockage Azure](storage-account-overview.md) contient plus d’informations, notamment des descriptions des différents comptes de stockage.

**Nom de la référence SKU** : Les noms de références SKU valides incluent `Premium_LRS`, `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_RAGZRS`, et `Standard_ZRS`. Les noms de références sont sensibles à la casse et sont des champs de chaîne dans la [classe SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Récupérer les informations de compte

Pour obtenir le type de compte de stockage et le nom de la référence (SKU) associés à un objet blob, appelez la méthode [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) ou [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet).

L’exemple de code suivant récupère et affiche les propriétés du compte en lecture seule.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres opérations que vous pouvez effectuer sur un compte de stockage via le [portail Azure](https://portal.azure.com) et l’API REST Azure.

- [Gérer un compte de stockage](storage-account-manage.md)
- [Mettre à niveau un compte de stockage](storage-account-upgrade.md)
- [Opération d’extraction des informations de compte (REST)](/rest/api/storageservices/get-account-information)
