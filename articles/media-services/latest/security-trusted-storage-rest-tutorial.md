---
title: Stockage approuvé Azure Media Services
description: Dans ce tutoriel, vous allez découvrir comment activer le stockage approuvé pour Azure Media Services, utiliser des identités managées pour le stockage approuvé, et fournir à Azure Services l’accès à un compte de stockage en cas d’utilisation d’un pare-feu ou d’un VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: f076381333457c9ba2fd4325fa8aa7baad5d8a5b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281071"
---
# <a name="tutorial-media-services-trusted-storage"></a>Tutoriel : Stockage approuvé Media Services

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Comment activer le stockage approuvé pour Azure Media Services.
> - Comment utiliser des identités managées pour le stockage approuvé.
> - Comment autoriser Azure Services à accéder à un compte de stockage lors de l’utilisation d’un contrôle d’accès réseau tel qu’un pare-feu ou un VPN.

Avec l’API 2020-05-01, vous pouvez activer le stockage approuvé en associant une identité managée à un compte Media Services.

>[!NOTE]
>Le stockage approuvé est uniquement disponible dans l’API et n’est actuellement pas activé dans le portail Azure.

Media Services peut accéder automatiquement à votre compte de stockage à l’aide de l’authentification système. Media Services vérifie que le compte Media Services et le compte de stockage se trouvent dans le même abonnement. Il vérifie également que l’utilisateur qui ajoute l’association a accès au compte de stockage avec Azure Resource Manager RBAC.

Toutefois, si vous souhaitez utiliser l’accès réseau pour sécuriser votre compte de stockage et activer le stockage approuvé, l’authentification par [identités managées](concept-managed-identities.md) est obligatoire. Celle-ci permet à la plateforme Media Services d’accéder au compte de stockage configuré avec un pare-feu ou une restriction de réseau virtuel via un accès à un stockage approuvé.

## <a name="overview"></a>Vue d’ensemble

> [!IMPORTANT]
> Utilisez l’API 2020-05-01 pour toutes les requêtes à Media Services.

Voici les étapes générales pour créer un stockage approuvé pour Media Services :

1. Créez un groupe de ressources.
1. Créez un compte de stockage.
1. Interrogez le compte de stockage jusqu’à ce qu’il soit prêt. Lorsque le compte de stockage est prêt, la requête retourne l’ID du principal du service.
1. Recherchez l’ID du rôle *Contributeur aux données Blob du stockage*.
1. Appelez le fournisseur d’autorisation et ajoutez une attribution de rôle.
1. Mettez à jour le compte Media Services pour l’authentification auprès du compte de stockage à l’aide de Managed Identity.
1. Supprimez les ressources si vous ne souhaitez pas continuer à les utiliser et à être facturé pour elles.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin d’un abonnement Azure.  Si vous n’avez pas d’abonnement Azure, [créez un compte d’essai gratuit](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Obtenir votre ID d’abonnement et votre ID de locataire

Si vous ne savez pas comment obtenir votre ID de locataire et votre ID d’abonnement, consultez [Guide pratique pour trouver votre ID de locataire](setup-azure-tenant-how-to.md) et [Rechercher votre ID de locataire](setup-azure-tenant-how-to.md).

### <a name="create-a-service-principal-and-secret"></a>Créer un principal du service et un secret

Si vous ne savez pas comment créer un principal du service et un secret, consultez [Obtenir des informations d’identification pour accéder à l’API Media Services](access-api-howto.md).

## <a name="use-a-rest-client"></a>Utiliser un client REST

Ce script est destiné à être utilisé avec un client REST, comme ce qui est disponible dans les extensions Visual Studio Code.  Adaptez-le à votre environnement de développement.

## <a name="set-initial-variables"></a>Définir les variables initiales

Cette partie du script est censée être utilisée dans un client REST. Vous pouvez utiliser des variables différemment dans votre environnement de développement.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Obtenir un jeton pour Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Obtenir un jeton pour l’API Graph

Cette partie du script est censée être utilisée dans un client REST. Vous pouvez utiliser des variables différemment dans votre environnement de développement.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Obtenir les détails du principal du service

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Stocker l’ID du principal du service

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Créer un compte de stockage

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Obtenir l’état du compte de stockage

Il faut un certain temps avant que le compte de stockage soit prêt. Cette requête interroge donc son état.  Répétez cette requête jusqu’à ce que le compte de stockage soit prêt.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Obtenir les détails du compte de stockage

Une fois le compte de stockage prêt, récupérez ses propriétés.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Obtenir un jeton pour ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Créer un compte Media Services avec une identité managée affectée par le système

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Obtenir la définition du rôle de données Blob du stockage

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Définir l’attribution de rôle de stockage

L’attribution de rôle indique que le principal du service pour le compte Media Services a le rôle de stockage *Contributeur aux données Blob du stockage*.  Cela peut prendre un certain temps, et il est important d’attendre, sinon le compte Media Services ne sera pas configuré correctement.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Accorder à l’identité managée un accès de contournement au compte de stockage

Cette action change l’accès de « identité managée affectée par le système » en « Managed Identity ». Ainsi, Azure Services peut accéder au compte de stockage par le biais d’un pare-feu, car les services Azure peuvent accéder au compte de stockage quelles que soient les règles d’accès IP (ACL).

Là encore, attendez que le rôle ait été attribué dans le compte de stockage, sinon le compte Media Services sera configuré de manière incorrecte.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Mettre à jour le compte Media Services de façon à utiliser Managed Identity

Il peut être nécessaire de retenter cette requête à plusieurs reprises, car la propagation de l’attribution de rôle de stockage peut prendre quelques minutes.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Tester l’accès

Testez l’accès en créant une ressource dans le compte de stockage.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Supprimer des ressources

Si vous ne souhaitez pas conserver les ressources que vous avez créées et continuer à être facturé, supprimez-les.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Étapes suivantes

[Guide pratique pour créer une ressource](asset-create-asset-how-to.md)
