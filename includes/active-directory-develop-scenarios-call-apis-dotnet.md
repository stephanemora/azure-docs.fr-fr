---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423756"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Propriétés AuthenticationResult dans MSAL.NET

Les méthodes pour acquérir des jetons renvoient une propriété `AuthenticationResult` (ou, pour les méthodes async, une propriété `Task<AuthenticationResult>`).

Dans MSAL.NET, `AuthenticationResult` expose :

- `AccessToken` pour que l’API web accède aux ressources. Ce paramètre est une chaîne, généralement une méthode JWT encodée en base64, mais le client ne doit jamais regarder à l’intérieur du jeton d’accès. La stabilité du format n’est pas garantie et ce dernier peut être chiffré pour la ressource. L’écriture humaine de code qui dépend du contenu des jetons d’accès sur le client constitue l’une des sources d’erreurs et des ruptures de logique client les plus importantes. Consulter aussi [Jetons d’accès](../articles/active-directory/develop/access-tokens.md)
- `IdToken` pour l’utilisateur (ce paramètre est un jeton JWT encodé). Voir [Jetons d’ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` exprime l’heure/la date d’expiration du jeton
- `TenantId` contient le locataire dans lequel l’utilisateur a été trouvé. Pour les utilisateurs invités (scénarios d’Azure AD B2B), l’ID de locataire correspond au locataire invité, pas au locataire unique.
Lorsque le jeton est remis pour un utilisateur, `AuthenticationResult` contient également les informations sur cet utilisateur. Pour les flux de clients confidentiels où les jetons sont demandés sans aucun utilisateur (pour l’application), ces informations sur l’utilisateur sont manquantes ou inconnues.
- Les `Scopes` pour lesquelles le jeton a été émis.
- L’ID unique de l’utilisateur.

### <a name="iaccount"></a>IAccount

MSAL.NET définit la notion de compte (via l’interface `IAccount`). Ce changement cassant fournit la bonne sémantique : le fait que le même utilisateur puisse avoir plusieurs comptes, dans des annuaires Azure AD différents. De plus, MSAL.NET fournit de meilleures informations dans les scénarios d’invité, car des informations de compte d’accueil sont fournies.
Le schéma suivant présente la structure de l’interface `IAccount` :

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

La classe `AccountId` identifie un compte dans un locataire spécifique. Il possède les propriétés suivantes :

| Propriété | Description |
|----------|-------------|
| `TenantId` | Une représentation de chaîne pour un identificateur global unique, qui correspond à l’ID du locataire où réside le compte. |
| `ObjectId` | Une représentation de chaîne pour un identificateur global unique, qui correspond à l’ID de l’utilisateur qui possède le compte dans le locataire. |
| `Identifier` | Identificateur unique pour ce compte. `Identifier` est la concaténation de `ObjectId` et de `TenantId` séparés par une virgule et ne sont pas encodés base64. |

L’interface `IAccount` représente les informations d’un seul compte. Le même utilisateur peut être présent dans différents locataires. Un utilisateur peut donc disposer de plusieurs comptes. Ses membres sont :

| Propriété | Description |
|----------|-------------|
| `Username` | Une chaîne contenant la valeur affichée au format UserPrincipalName (UPN), par exemple, john.doe@contoso.com. Cette chaîne peut être null, alors que HomeAccountId et HomeAccountId.Identifier ne le sont pas. Cette propriété remplace la propriété `DisplayableId` de `IUser` dans les versions précédentes de MSAL.NET. |
| `Environment` | Une chaine contenant le fournisseur d’identité de ce compte, par exemple, `login.microsoftonline.com`. Cette propriété remplace la propriété `IdentityProvider` de `IUser`, sauf que `IdentityProvider` disposait aussi des informations sur le locataire (en plus de l’environnement cloud), alors qu’ici, la valeur n’est que l’hôte. |
| `HomeAccountId` | AccountId du compte d’accueil de l’utilisateur. Cette propriété identifie de façon unique l’utilisateur entre les locataires Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Utilisation du jeton pour appeler une API protégée

Une fois `AuthenticationResult` retourné par MSAL (dans `result`), vous devez l’ajouter à l’en-tête d’autorisation HTTP, avant de passer l’appel pour accéder à l’API Web protégée.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```