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
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075144"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Propriétés de AuthenticationResult dans MSAL.NET

Les méthodes pour acquérir des jetons retournent un `AuthenticationResult` (ou, pour les méthodes async, un `Task<AuthenticationResult>`.

Dans MSAL.NET, `AuthenticationResult` expose :

- `AccessToken` pour l’API Web pour accéder aux ressources. Ce paramètre est une chaîne, généralement un JWT en Base64, mais le client ne doit jamais se présenter à l’intérieur du jeton d’accès. Le format n’est pas garanti de demeurer stable et peuvent être chiffrées pour la ressource. Écriture de code de personnes en fonction du contenu des jetons access sur le client est un des plus grands sources d’erreurs et les sauts de logique de client. Voir aussi [jetons d’accès](../articles/active-directory/develop/access-tokens.md)
- `IdToken` pour l’utilisateur (ce paramètre est un JWT encodée). Consultez [les jetons d’ID](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` Indique la date/heure expiration du jeton
- `TenantId` contient le locataire dans lequel l’utilisateur a été trouvé. Pour les utilisateurs invités (scénarios d’Azure AD B2B), l’ID client est le locataire invité, pas le locataire unique.
Lorsque le jeton est remis pour un utilisateur, `AuthenticationResult` contient également des informations sur cet utilisateur. Pour les flux de client confidentiel où les jetons sont demandés sans utilisateur (pour l’application), ces informations de l’utilisateur sont null.
- Le `Scopes` pour lequel le jeton a été créée.
- Id unique de l’utilisateur.

### <a name="iaccount"></a>IAccount

MSAL.NET définit la notion de compte (via le `IAccount` interface). Cette modification avec rupture fournit la sémantique de droite : le fait que le même utilisateur peut avoir plusieurs comptes, dans Azure différents annuaires AD. Également MSAL.NET fournit de meilleures informations dans le cas de scénarios d’invité, comme les informations de compte de base sont fournies.
Le diagramme suivant illustre la structure de la `IAccount` interface :

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Le `AccountId` classe identifie un compte sur un client spécifique. Il possède les propriétés suivantes :

| Propriété | Description |
|----------|-------------|
| `TenantId` | Une représentation de chaîne GUID, qui est l’ID du client où se trouve le compte. |
| `ObjectId` | Représentation sous forme de chaîne pour un GUID, qui est l’ID de l’utilisateur propriétaire du compte dans le locataire. |
| `Identifier` | Identificateur unique pour le compte. `Identifier` est la concaténation de `ObjectId` et `TenantId` séparés par une virgule et le sont pas codées en base64. |

Le `IAccount` interface représente des informations sur un seul compte. Le même utilisateur peut être présent dans les différents clients, autrement dit, un utilisateur peut avoir plusieurs comptes. Ses membres sont :

| Propriété | Description |
|----------|-------------|
| `Username` | Une chaîne contenant la valeur peut être affichée au format de l’attribut UserPrincipalName (UPN), par exemple, john.doe@contoso.com. Cette chaîne peut être null, tandis que les HomeAccountId HomeAccountId.Identifier ne sont pas null. Cette propriété remplace la `DisplayableId` propriété du `IUser` dans les versions précédentes de MSAL.NET. |
| `Environment` | Chaîne contenant le fournisseur d’identité pour ce compte, par exemple, `login.microsoftonline.com`. Cette propriété remplace la `IdentityProvider` propriété du `IUser`, sauf que `IdentityProvider` devait également des informations sur le client (en plus de l’environnement de cloud), tandis que la valeur est uniquement l’hôte. |
| `HomeAccountId` | ID de compte du compte d’origine pour l’utilisateur. Cette propriété identifie de façon unique l’utilisateur entre les locataires Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Utilisation du jeton pour appeler une API protégée

Une fois le `AuthenticationResult` a été retourné par MSAL (dans `result`), vous devez ajouter à l’en-tête d’autorisation HTTP, avant d’effectuer l’appel à accéder à l’API Web protégée.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```