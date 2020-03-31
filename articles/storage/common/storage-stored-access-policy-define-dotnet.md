---
title: Définir une stratégie d’accès stockée avec le stockage .NET-Azure
description: Découvrez comment définir une stratégie d’accès stockée à l’aide de la bibliothèque cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990605"
---
# <a name="define-a-stored-access-policy-with-net"></a>Définir une stratégie d’accès stockée avec .NET

Une stratégie d’accès stockée fournit un niveau de contrôle supplémentaire sur les signatures d’accès partagé (SAP) au niveau du service côté serveur. La définition d’une stratégie d’accès stockée sert à regrouper des signatures d’accès partagé et à fournir des restrictions supplémentaires pour les signatures d’accès partagé liées par la stratégie. Vous pouvez utiliser une stratégie d'accès stockée pour modifier l'heure de début, l'heure d'expiration ou les autorisations d'une SAP, ou de la révoquer après sa publication.
  
 Les ressources de stockage suivantes prennent en charge les stratégies d’accès stockées :  
  
- Conteneurs d’objets blob  
- Partages de fichiers  
- Files d’attente  
- Tables  
  
> [!NOTE]
> Une stratégie d’accès stockée sur un conteneur peut être associée à une signature d’accès partagé accordant des autorisations au conteneur lui-même ou aux objets blob qu’il contient. De même, une stratégie d’accès stockée sur un partage de fichiers peut être associée à une signature d’accès partagé accordant des autorisations au partage lui-même ou aux fichiers qu’il contient.  
>
> Les stratégies d’accès stockées sont prises en charge pour une SAP de service uniquement. Les stratégies d’accès stockées ne sont pas prises en charge pour les SAP de compte ou les SAP de délégation d’utilisateur.  

## <a name="create-a-stored-access-policy"></a>création d’une stratégie d’accès stockée

Le code suivant crée une stratégie d’accès stockée sur un conteneur. Vous pouvez utiliser la stratégie d’accès pour spécifier des contraintes pour une SAP de service sur le conteneur ou sur ses objets blob.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Voir aussi

- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](storage-sas-overview.md)
- [Définir une stratégie d’accès stockée](/rest/api/storageservices/define-stored-access-policy)

