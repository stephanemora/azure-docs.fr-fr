---
title: Comprendre la connectivité et l’authentification des appareils Azure Digital Twins | Microsoft Docs
description: Utilisez Azure Digital Twins pour connecter et authentifier des appareils.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: f032e3ebf6a10411057cd6d41df0cad6248f328b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636235"
---
# <a name="create-and-manage-role-assignments"></a>Créer et gérer des attributions de rôle

Azure Digital Twins utilise le contrôle d’accès en fonction du rôle ([RBAC](./security-role-based-access-control.md)) pour gérer l’accès aux ressources.

Chaque attribution de rôle comprend :

* un **identificateur d’objet** : ID Azure Active Directory, ID objet de principal de service ou nom de domaine ;
* un **type d’identificateur d’objet** ;
* un **ID de définition de rôle** ;
* un **chemin d’espace** ;
* un **ID de locataire** : ID de locataire Azure Active Directory dans la plupart des cas.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="role-definition-identifiers"></a>Identificateurs de définition de rôle

Le tableau suivant indique ce que peuvent donner des requêtes à l’API système/rôles.

| **Rôle** | **Identificateur** |
| --- | --- |
| Administrateur de l’espace | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrateur d'utilisateurs| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrateur de l’appareil | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrateur des clés | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrateur des jetons | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Utilisateur | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Spécialiste du support | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Installateur des appareils | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Appareil de passerelle | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>ObjectIdTypes pris en charge

`ObjectIdTypes` pris en charge :

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Création d'une affectation de rôle

```plaintext
HTTP POST YOUR_MANAGEMENT_API_URL/roleassignments
```

| **Nom** | **Obligatoire** | **Type** | **Description** |
| --- | --- | --- | --- |
| roleId| Oui |Chaîne | Identificateur de la définition de rôle. Pour connaître les définitions de rôles et leurs identificateurs, interrogez l’API système. |
| objectId | Oui |Chaîne | ID d’objet pour l’attribution de rôle qui doit être mis en forme en fonction du type qui lui est associé. Pour l’ObjectIdType `DomainName`, ObjectId doit commencer par le caractère `“@”`. |
| objectIdType | Oui |Chaîne | Type de l’attribution de rôle. Doit être l’une des lignes suivantes de la table. |
| tenantId | Varie | Chaîne |Identificateur du locataire. Interdit pour les ObjectIdTypes `DeviceId` et `TenantId`. Obligatoire pour les ObjectIdTypes `UserId` et `ServicePrincipalId`. Facultatif pour l’ObjectIdType DomainName. |
| path* | Oui | Chaîne |Chemin complet de l’objet `Space`. Par exemple `/{Guid}/{Guid}`. Si l’identificateur a besoin de l’attribution de rôle pour l’intégralité du graphe, spécifiez `"/"`. Ce caractère désigne la racine. Cependant, il est déconseillé de l’utiliser. Suivez toujours le principe des privilèges minimum. |

## <a name="sample-configuration"></a>Exemple de configuration

Dans cet exemple, l’utilisateur a besoin d’un accès administratif à l’un des étages d’un espace de locataire.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Dans cet exemple, une application exécute des scénarios de test simulant des appareils et des capteurs.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Tous les utilisateurs qui font partie d’un domaine obtiennent un accès en lecture aux espaces, aux capteurs et aux utilisateurs. Cet accès inclut les objets connexes correspondants.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Utilisez GET pour obtenir une attribution de rôle.

```plaintext
HTTP GET YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| **Nom** | **Dans** | **Obligatoire** |    **Type** |  **Description** |
| --- | --- | --- | --- | --- |
| YOUR_PATH | path | True | Chaîne |    Chemin complet de l’espace |

Utilisez DELETE pour supprimer une attribution de rôle.

```plaintext
HTTP DELETE YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ID
```

| **Nom** | **Dans** | **Obligatoire** | **Type** | **Description** |
| --- | --- | --- | --- | --- |
| YOUR_ROLE_ID | path | True | Chaîne | ID d’attribution de rôle |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurité Azure Digital Twins, lisez [Authentification des API](./security-authenticating-apis.md).
