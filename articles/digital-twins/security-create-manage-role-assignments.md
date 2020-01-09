---
title: Créer et gérer des attributions de rôle - Azure Digital Twins | Microsoft Docs
description: Apprenez à créer et gérer des attributions de rôle dans Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/12/2019
ms.custom: seodec18
ms.openlocfilehash: 7eeaadc80a97a96e6effdfc9e5cc76c201998f3f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438057"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Créer et gérer des attributions de rôle dans Azure Digital Twins

Azure Digital Twins utilise le contrôle d’accès en fonction du rôle ([RBAC](./security-role-based-access-control.md)) pour gérer l’accès aux ressources.

## <a name="role-assignments-overview"></a>Vue d’ensemble des attributions de rôle

Chaque attribution de rôle est conforme à la définition suivante :

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

Le tableau suivant décrit chaque attribut :

| Attribut | Name | Obligatoire | Type | Description |
| --- | --- | --- | --- | --- |
| roleId | Identificateur de la définition de rôle | Oui | String | ID unique de l’attribution de rôle souhaitée. Pour connaître les définitions de rôles et leurs identificateurs, interrogez l’API système ou reportez-vous au tableau ci-dessous. |
| objectId | Identificateur d'objet | Oui | String | ID Azure Active Directory, ID objet de principal de service ou nom de domaine. À quoi ou à qui le rôle est attribué. L’attribution de rôle doit être mise en forme en fonction du type qui lui est associé. Pour l’objectIdType `DomainName`, objectId doit commencer par le caractère `“@”`. |
| objectIdType | Type d’identificateur d’objet | Oui | String | Type d’identificateur d’objet utilisé. Consultez **ObjectIdTypes pris en charge** ci-dessous. |
| path | Chemin d’espace | Oui | String | Chemin complet de l’objet `Space`. par exemple `/{Guid}/{Guid}`. Si l’identificateur a besoin de l’attribution de rôle pour l’intégralité du graphe, spécifiez `"/"`. Ce caractère désigne la racine. Cependant, il est déconseillé de l’utiliser. Suivez toujours le principe des privilèges minimum. |
| tenantId | Identificateur de locataire | Variable | String | Dans la plupart des cas, un ID de locataire Azure Active Directory. Interdit pour les ObjectIdTypes `DeviceId` et `TenantId`. Obligatoire pour les ObjectIdTypes `UserId` et `ServicePrincipalId`. Facultatif pour l’ObjectIdType DomainName. |

### <a name="supported-role-definition-identifiers"></a>Identificateurs de définition de rôle pris en charge

Chaque attribution de rôle associe une définition de rôle à une entité dans votre environnement Azure Digital Twins.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Types d’identificateur d’objet pris en charge

L’attribut **objectIdType** a été présenté précédemment.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Opérations d’attribution de rôle

Azure Digital Twins prend en charge les opérations *CREATE*, *READ* et *DELETE* pour les attributions de rôle. Les opérations *UPDATE* sont gérées par l’ajout d’attributions de rôle, la suppression d’attributions de rôle ou la modification des nœuds de [graphique d’intelligence spatiale](./concepts-objectmodel-spatialgraph.md) auxquels les attributions de rôle donnent accès.

[![Points de terminaison d'attribution de rôle](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

La documentation de référence Swagger fournie contient des informations complémentaires sur tous les point de terminaison d’API disponibles, les opérations de requête et les définitions.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Accorder des autorisations à votre principal de service

L’octroi d’autorisations à votre principal de service est souvent l’une des premières étapes à franchir lorsque vous utilisez Azure Digital Twins. Cela implique les opérations suivantes :

1. Connexion à votre instance Azure via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou [PowerShell](https://docs.microsoft.com/powershell/azure/).
1. Acquisition de vos informations de principal de service.
1. Attribution du rôle souhaité à votre principal de service.

Votre ID d’application vous est attribué dans Azure Active Directory. Pour en savoir plus sur la configuration et l’approvisionnement d’Azure Digital Twins dans Active Directory, consultez le guide de [Démarrage rapide](./quickstart-view-occupancy-dotnet.md).

Une fois que vous disposez de l'ID d'application, exécutez l'une des commandes suivantes. Dans Azure CLI :

```azurecli
az login
az ad sp show --id <ApplicationId>
```

Dans PowerShell :

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Un utilisateur ayant le rôle **Admin** peut alors assigner le rôle Administrateur d’espace à un autre utilisateur en soumettant une requête HTTP POST authentifiée à l’URL :

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Avec le corps JSON suivant :

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Récupérer tous les rôles

[![Rôles système](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Pour dresser la liste de tous les rôles disponibles (définitions de rôles), exécutez une requête HTTP GET authentifiée dans :

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Une requête réussie renvoie un tableau JSON répertoriant les rôles pouvant être attribués :

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

### <a name="check-a-specific-role-assignment"></a>Contrôler une attribution de rôle spécifique

Pour contrôler une attribution de rôle spécifique, exécutez une requête HTTP GET authentifiée dans :

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Valeur du paramètre** | **Obligatoire** |  **Type** |  **Description** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | String |   ID d’objet pour l’objectIdType UserId. |
| YOUR_PATH | True | String |   Chemin dont l’accès est contrôlé. |
| YOUR_ACCESS_TYPE |  True | String |   *Lire*, *Créer*, *Mettre à jour* ou *Supprimer* |
| YOUR_RESOURCE_TYPE | True | String |  *Device*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *Endpoint*, *KeyStore*, *Matcher*, *Ontology*, *Report*, *RoleDefinition*, *Sensor*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *System*, *UerDefinedFunction*, *User*, *UserBlobMetadata* ou *UserExtendedProperty* |

Une requête réussie renvoie un booléen `true` ou `false` pour indiquer si le type d’accès a été attribué à l’utilisateur pour le chemin et la ressource donnés.

### <a name="get-role-assignments-by-path"></a>Obtenir des attributions de rôle en fonction du chemin d’accès

Pour obtenir toutes les attributions de rôle pour un chemin d’accès, exécutez une requête HTTP GET authentifiée dans :

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Valeur | Remplacer par |
| --- | --- |
| YOUR_PATH | Chemin complet de l’espace |

Une requête réussie renvoie un tableau JSON avec chaque attribution de rôle associée au paramètre **path** sélectionné :

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Révoquer une autorisation

Pour révoquer l'autorisation d'un destinataire, supprimez l'attribution de rôle en effectuant une requête HTTP DELETE authentifiée :

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Paramètre | Remplacer par |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **id** de l’attribution de rôle à supprimer |

Une requête de suppression réussie renvoie l’état de réponse 204. Vérifiez la suppression de l’attribution de rôle en [contrôlant](#check-a-specific-role-assignment) si l’attribution de rôle est toujours valable.

### <a name="create-a-role-assignment"></a>Création d'une affectation de rôle

Pour créer une attribution de rôle spécifique, exécutez une requête HTTP POST authentifiée dans l’URL :

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Vérifiez que le corps JSON est conforme au schéma suivant :

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Une requête réussie retourne l’état de réponse 201 avec l’**id** de l’attribution de rôle nouvellement créée :

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Exemples de configuration

Les exemples suivants montrent comment configurer le corps JSON dans plusieurs scénarios d’attribution de rôles couramment rencontrés.

* **Exemple** : Un utilisateur a besoin d’un accès administratif à un étage d’un espace de locataire.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Exemple** : Une application exécute des scénarios de test simulant des capteurs et des appareils.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Exemple** : Tous les utilisateurs qui font partie d’un domaine obtiennent un accès en lecture aux espaces, aux capteurs et aux utilisateurs. Cet accès inclut les objets connexes correspondants.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Étapes suivantes

- Pour passer en revue le contrôle d’accès en fonction du rôle Azure Digital Twins, lisez [Contrôle d’accès en fonction du rôle](./security-authenticating-apis.md).

- Pour plus d’informations sur l’authentification de l’API Azure Digital Twins, lisez [Authentification des API](./security-authenticating-apis.md).
