---
title: Configuration d’identité Azure Active Directory de l’API Azure pour FHIR
description: Découvrez les principes d’identité, d’authentification et d’autorisation des serveurs Azure FHIR.
services: healthcare-apis
author: caitlinv39
ms.reviewer: mihansen
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 53adf974a3af4a2cc3e5c89156fe4b50571c7b79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870909"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Configuration d’identité Azure Active Directory de l’API Azure pour FHIR

Quand vous utilisez des données médicales, il est important de vérifier que ces données sont sécurisées et qu’elles ne sont pas accessibles aux utilisateurs ou applications non autorisés. Les serveurs FHIR utilisent [OAuth 2.0](https://oauth.net/2/) pour garantir la sécurité des données. L’[API Azure pour FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) est sécurisée à l’aide d’[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/), qui est un exemple de fournisseur d’identité OAuth 2.0. Cet article fournit une vue d’ensemble du principe d’autorisation relatif à un serveur FHIR ainsi que les étapes nécessaires à l’obtention d’un jeton d’accès à un serveur FHIR. Bien que ces étapes s’appliquent à tous les serveurs FHIR et tous les fournisseurs d’identité, nous allons passer en revue l’API Azure pour FHIR dans le cadre d’un serveur FHIR, et nous allons aborder Azure AD en tant que fournisseur d’identité dans cet article.

## <a name="access-control-overview"></a>Présentation du contrôle d’accès

Pour qu’une application cliente puisse accéder à l’API Azure pour FHIR, elle doit présenter un jeton d’accès. Le jeton d’accès est une collection de propriétés (revendications) encodée au format [Base64](https://en.wikipedia.org/wiki/Base64) et signée, qui transmet des informations sur l’identité du client ainsi que les rôles et privilèges accordés au client.

Il existe plusieurs façons d’obtenir un jeton, mais l’API Azure pour FHIR ne se soucie pas de la façon dont le jeton est obtenu tant qu’il s’agit d’un jeton correctement signé avec les revendications appropriées. 

Si nous prenons l’exemple du [flux du code d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code), l’accès à un serveur FHIR passe par les quatre étapes ci-dessous :

![Autorisation FHIR](media/azure-ad-hcapi/fhir-authorization.png)

1. Le client envoie une requête au point de terminaison `/authorize` d’Azure AD. Azure AD redirige le client vers une page de connexion où l’utilisateur s’authentifie à l’aide des informations d’identification appropriées (par exemple un nom d’utilisateur et un mot de passe, ou une authentification à 2 facteurs). Pour plus d’informations, consultez les détails relatifs à l’[obtention d’un code d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#request-an-authorization-code). Une fois l’authentification réussie, un *code d’autorisation* est retourné au client. Azure AD autorise uniquement le retour de ce code d’autorisation à une URL de réponse inscrite, configurée au moment de l’inscription de l’application cliente (voir ci-dessous).
1. L’application cliente échange le code d’autorisation contre un *jeton d’accès* au niveau du point de terminaison `/token` d’Azure AD. Au moment où elle demande un jeton, l’application cliente peut être amenée à fournir un secret client (mot de passe de l’application). Pour plus d’informations, consultez les détails relatifs à l’[obtention d’un jeton d’accès](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#use-the-authorization-code-to-request-an-access-token).
1. Le client envoie une requête à l’API Azure pour FHIR, par exemple `GET /Patient` pour rechercher tous les patients. Au moment où il effectue la requête, il inclut le jeton d’accès dans un en-tête de requête HTTP, par exemple `Authorization: Bearer eyJ0e...`, où `eyJ0e...` représente le jeton d’accès encodé au format Base64.
1. L’API Azure pour FHIR vérifie que le jeton contient les revendications appropriées (propriétés contenues dans le jeton). Si tout est correct, la requête est soumise et un bundle FHIR est retourné avec les résultats appropriés au client.

Il est important de noter que l’API Azure pour FHIR n’est pas impliquée dans la validation des informations d’identification de l’utilisateur, et qu’elle n’émet pas le jeton. L’authentification et la création de jeton sont effectuées par Azure AD. L’API Azure pour FHIR valide simplement le fait que le jeton est signé correctement (il est authentique) et qu’il dispose des revendications appropriées.

## <a name="structure-of-an-access-token"></a>Structure d’un jeton d’accès

Le développement d’applications FHIR implique souvent le débogage des problèmes d’accès. Si un client se voit refuser l’accès à l’API Azure pour FHIR, il est utile de comprendre la structure du jeton d’accès et la façon dont il peut être décodé pour inspecter le contenu (les revendications) du jeton. 

Les serveurs FHIR attendent généralement un [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, parfois prononcé « jot »). Il se compose de trois parties :

1. Un en-tête, qui peut ressembler à :
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. La charge utile (les revendications), par exemple :
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Une signature, qui est calculée en concaténant d’une part le contenu encodé au format Base64 de l’en-tête et de la charge utile, et en calculant d’autre part le code de hachage de chiffrement de ces valeurs en fonction de l’algorithme (`alg`) spécifié dans l’en-tête. Un serveur peut obtenir des clés publiques auprès du fournisseur d’identité et vérifier que le jeton a été émis par un fournisseur d’identité spécifique, et qu’il n’a pas été falsifié.

Le jeton complet se compose des versions encodées au format Base64 (en fait encodées en tant qu’URL au format Base64) de ces trois segments. Les trois segments sont concaténés et séparés par un `.` (point).

Voici un exemple de jeton ci-dessous :

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Le jeton peut être décodé et inspecté à l’aide d’outils tels que [https://jwt.ms](https://jwt.ms). Le résultat du décodage du jeton est le suivant :

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Obtention d’un jeton d’accès

Comme indiqué ci-dessus, il existe plusieurs façons d’obtenir un jeton auprès d’Azure AD. Elles sont décrites en détail dans la [documentation destinée aux développeurs Azure AD](https://docs.microsoft.com/azure/active-directory/develop/).

Azure AD comporte deux versions distinctes des points de terminaison OAuth 2.0, appelés `v1.0` et `v2.0`. Ces deux versions sont des points de terminaison OAuth 2.0. Les désignations `v1.0` et `v2.0` font référence aux différentes façons dont Azure AD implémente cette norme. 

Quand vous utilisez un serveur FHIR, vous pouvez vous servir des points de terminaison `v1.0` ou `v2.0`. Le choix peut dépendre des bibliothèques d’authentification que vous utilisez dans votre application cliente.

Les sections pertinentes de la documentation Azure AD sont les suivantes :

* Point de terminaison `v1.0` :
    * [Flux du code d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code).
    * [Flux des informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow).
* Point de terminaison `v2.0` :
    * [Flux du code d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).
    * [Flux des informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).

Il existe d’autres variantes (par exemple le flux On-Behalf-Of) pour obtenir un jeton. Pour plus d’informations, consultez la documentation Azure AD. Quand vous utilisez l’API Azure pour FHIR, il existe également des raccourcis qui permettent d’obtenir un jeton d’accès (à des fins de débogage) [via Azure CLI](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert certains concepts de base liés à la sécurisation de l’accès à l’API Azure pour FHIR via Azure AD. Pour savoir comment déployer une instance de l’API Azure pour FHIR, passez au guide de démarrage rapide sur le déploiement.

>[!div class="nextstepaction"]
>[Déployer l’API Azure pour FHIR](fhir-paas-portal-quickstart.md)