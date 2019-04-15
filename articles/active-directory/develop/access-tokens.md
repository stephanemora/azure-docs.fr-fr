---
title: Référence des jetons access de plateforme d’identité Microsoft | Azure
description: En savoir plus sur les jetons d’accès émis par la version 1.0 d’Azure AD et les points de terminaison (v2.0) de plateforme Microsoft identity.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07e140ef9f561625bb89498c6b6591734e8a9b10
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563757"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Jetons d’accès plateforme Microsoft identity

Les jetons d’accès permettent aux clients d’appeler en toute sécurité des API protégées par Azure. Jetons d’accès plateforme Microsoft identity sont [Jwt](https://tools.ietf.org/html/rfc7519), les objets JSON signés par Azure au codage Base64. Les clients doivent traiter les jetons d’accès comme des chaînes opaques, car leur contenu n’est destiné qu’à la ressource. Pour la validation et le débogage, les développeurs peuvent décoder les JWT via un site comme [jwt.ms](https://jwt.ms). Votre client peut obtenir un jeton d’accès à partir du point de terminaison v1.0 ou le point de terminaison v2.0 à l’aide d’une variété de protocoles.

Lorsque votre client demande un jeton d’accès, Azure AD retourne également des métadonnées sur le jeton d’accès pour la consommation de votre application. Ces informations incluent l’heure d’expiration de jeton d’accès et les étendues pour lesquelles il est valide. Ces données permettent à votre application effectuer la mise en cache intelligente des jetons d’accès sans avoir à analyser le jeton d’accès lui-même.

Si votre application est une ressource (API Web) à laquelle les clients peuvent demander l’accès, les jetons d’accès fournissent des informations utiles pour l’authentification et l’autorisation, comme l’utilisateur, le client, l’émetteur, les autorisations, etc.

Consultez les sections suivantes pour savoir comment une ressource peut valider et utiliser les revendications dans un jeton d’accès.

> [!IMPORTANT]
> Jetons d’accès sont créés selon le *audience* du jeton, ce qui signifie que l’application qui détient les étendues dans le jeton.  Voici comment un paramètre de ressource `accessTokenAcceptedVersion` dans le [manifeste de l’application](reference-app-manifest.md#manifest-reference) à `2` permet à un client appelant le point de terminaison v1.0 recevoir un jeton d’accès v2.0.  De même, c’est pourquoi de la modification du jeton d’accès [revendications facultatives](active-directory-optional-claims.md) pour votre client ne modifie pas le jeton d’accès reçu quand un jeton est demandé pour `user.read`, qui est détenu par la ressource MS Graph.  
> Pour la même raison, lors du test de votre application cliente avec un compte personnel (comme hotmail.com ou outlook.com), vous découvrirez peut-être que le jeton d’accès reçu par votre client est une chaîne opaque. Il s’agit, car la ressource sollicitée a demandé les tickets hérités MSA (compte Microsoft) qui sont chiffrées et ne peut pas être reconnus par le client.

## <a name="sample-tokens"></a>Exemples de jeton

Les jetons v1.0 et v2.0 se ressemblent et un grand nombre de leurs revendications sont identiques. Vous trouverez ici un exemple de chacun d’eux.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd
```

Afficher ce jeton v1.0 dans [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Afficher ce jeton v2.0 dans [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Revendications dans les jetons d’accès

Les TJW sont divisés en trois parties :

* **En-tête** : fournit des informations sur la façon de [valider le jeton](#validating-tokens), notamment des informations sur le type du jeton et la façon dont il a été signé.
* **Charge utile** : contient toutes les données importantes concernant l’utilisateur ou l’application qui tente d’appeler votre service.
* **Signature** : ressource de base utilisée pour valider le jeton.

Chaque partie est séparée par un point (`.`) et encodée séparément en base64.

Les revendications ne sont présentes que lorsqu’elles sont renseignées par une valeur. Par conséquent, votre application ne doit pas prendre une dépendance sur une revendication qui est présent. Exemples : `pwd_exp` (tous les clients n’ont pas besoin de mots de passe pour expirer) ou `family_name` ([ les flux d’informations d’identification client](v1-oauth2-client-creds-grant-flow.md) sont au nom d’applications qui ne portent pas de noms). Les revendications utilisées pour la validation des jetons d’accès seront toujours présentes.

> [!NOTE]
> Certaines revendications permettent à Azure AD de sécuriser les jetons en cas de réutilisation. Celles-ci portent la mention « Opaque » dans la description pour indiquer qu’elles ne sont pas destinées à l’utilisation publique. Ces revendications peuvent apparaître ou non dans un jeton, et de nouvelles revendications peuvent être ajoutées sans préavis.

### <a name="header-claims"></a>Revendications de l’en-tête

|Revendication | Format | Description |
|--------|--------|-------------|
| `typ` | Chaîne : toujours « JWT » | Indique que le jeton est un JWT.|
| `nonce` | Chaîne | Identificateur unique utilisé pour se protéger contre les attaques par relecture de jetons. Votre ressource peut enregistrer cette valeur pour se protéger contre les relectures. |
| `alg` | Chaîne | Indique l’algorithme utilisé pour signer le jeton, par exemple « RS256 » |
| `kid` | Chaîne | Indique l’empreinte de la clé publique utilisée pour signer le jeton. Émis dans les jetons d’accès v1.0 et v2.0. |
| `x5t` | Chaîne | Fonctions identiques (en utilisation et en valeur) à `kid`. `x5t` est une revendication héritée émise uniquement dans les jetons d’accès v1.0 à des fins de compatibilité. |

### <a name="payload-claims"></a>Revendications de la charge utile

| Revendication | Format | Description |
|-----|--------|-------------|
| `aud` | Chaîne, URI ID d’application | Identifie le destinataire du jeton. Dans les jetons d’accès, l’audience est l’ID attribué à votre application dans le portail Azure. Votre application doit valider cette valeur et rejeter le jeton si la valeur ne correspond pas. |
| `iss` | Chaîne, URI STS | Identifie le service d’émission de jeton de sécurité (STS) qui construit et retourne le jeton, ainsi que le client Azure AD dans lequel l’utilisateur a été authentifié. Si le jeton émis est un jeton v2.0 (voir la revendication `ver`), l’URI se termine dans `/v2.0`. La partie GUID qui indique que l’utilisateur est un utilisateur consommateur d’un compte Microsoft est `9188040d-6c67-4c5b-b112-36a304b66dad`. Votre application doit utiliser la partie GUID de la revendication pour restreindre l’ensemble des clients qui peuvent se connecter à l’application, le cas échéant. |
|`idp`| Chaîne, généralement un URI STS | Enregistre le fournisseur d’identité qui a authentifié le sujet du jeton. Cette valeur est identique à la valeur de la revendication de l’émetteur sauf si le compte d’utilisateur n’est pas dans le même locataire que l’émetteur (invités par exemple). Si la revendication n’est pas présente, cela signifie que la valeur de `iss` peut être utilisé à la place.  Pour les comptes personnels utilisés dans un contexte organisationnel (par exemple, un compte personnel invité dans un locataire Azure AD), la revendication `idp` peut être « live.com » ou un URI STS contenant le locataire de compte Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |  
| `iat` | int, horodatage UNIX | « Issued At » (Délivré le) indique quand l'authentification de ce jeton a eu lieu. |
| `nbf` | int, horodatage UNIX | La revendication « nbf » (pas avant) indique le délai avant lequel le JWT ne doit pas être accepté pour être traité. |
| `exp` | int, horodatage UNIX | La revendication « exp » (délai d’expiration) indique le délai d’expiration à partir duquel le JWT ne doit pas être accepté pour être traité. Il est à noter qu’une ressource peut également rejeter le jeton avant ce délai, par exemple lorsqu’un changement d’authentification est requis ou qu’une révocation de jeton a été détectée. |
| `acr` | Chaîne, « 0 » ou « 1 » | Revendication « Classe du contexte d’authentification ». La valeur « 0 » indique que l'authentification de l'utilisateur final ne répondait pas aux exigences de la norme ISO/IEC 29115. |
| `aio` | Chaîne opaque | Revendication interne utilisée par Azure AD pour enregistrer des données afin de réutiliser les jetons. Ressources ne doivent pas utiliser cette revendication. |
| `amr` | Tableau de chaînes JSON | Uniquement dans les jetons v1.0. Identifie comment le sujet du jeton a été authentifié. Pour plus d’informations, consultez [l’amr réclamer la section](#the-amr-claim). |
| `appid` | Chaîne, GUID | Uniquement dans les jetons v1.0. ID de l’application du client utilisant le jeton. L'application peut agir pour elle-même ou pour le compte d'un utilisateur. L'ID d'application représente généralement un objet d’application, mais elle peut également représenter un objet du principal du service dans Azure AD. |
| `appidacr` | « 0 », « 1 » ou « 2 » | Uniquement dans les jetons v1.0. Indique comment le client a été authentifié. Pour un client public, la valeur est « 0 ». Si l’ID client et la clé secrète client sont utilisés, la valeur est « 1 ». Si un certificat client a été utilisé pour l’authentification, la valeur est « 2 ». |
| `azp` | Chaîne, GUID | Uniquement dans les jetons v2.0. ID de l’application du client utilisant le jeton. L'application peut agir pour elle-même ou pour le compte d'un utilisateur. L'ID d'application représente généralement un objet d’application, mais elle peut également représenter un objet du principal du service dans Azure AD. |
| `azpacr` | « 0 », « 1 » ou « 2 » | Uniquement dans les jetons v2.0. Indique comment le client a été authentifié. Pour un client public, la valeur est « 0 ». Si l’ID client et la clé secrète client sont utilisés, la valeur est « 1 ». Si un certificat client a été utilisé pour l’authentification, la valeur est « 2 ». |
| `groups` | Tableau de GUID JSON | Fournit les ID d’objet qui représentent les appartenances aux groupes du sujet. Ces valeurs sont uniques (voir l'ID objet) et peuvent être utilisées en toute sécurité pour la gestion des accès, telle que l'autorisation d'accéder à une ressource. Les groupes inclus dans la revendication des groupes sont configurés pour chaque application, via la propriété `groupMembershipClaims` du [manifeste d’application](reference-app-manifest.md). Une valeur Null exclut tous les groupes, une valeur « SecurityGroup » inclut uniquement les appartenances aux groupes de sécurité Active Directory et une valeur « All » inclut les groupes de sécurité et les listes de Distribution Office 365. <br><br>Consultez la revendication `hasgroups` ci-dessous pour plus d’informations sur l’utilisation de la revendication `groups` avec l’octroi implicite. <br>Pour les autres flux, si le nombre de groupes de que l’utilisateur appartient dépasse une limite (150 pour SAML, 200 pour JSON), une revendication de dépassement est ajoutée aux sources de revendication qui pointent vers le point de terminaison Graph contenant la liste des groupes de l’utilisateur. |
| `hasgroups` | Booléen | Le cas échéant, toujours `true`, ce qui indique que l’utilisateur appartient à au moins un groupe. Utilisé à la place de la revendication `groups` pour JWT dans les flux d’octroi implicites si la revendication des groupes complets étend le fragment URI au-delà des limites de longueur d’URL (actuellement, 6 groupes ou plus). Indique que le client doit utiliser Graph pour déterminer les groupes de l’utilisateur (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Objet JSON | Pour les demandes de jeton qui ne sont pas de longueur limitée (consultez `hasgroups` ci-dessus), mais il est toujours trop volumineux pour le jeton, un lien vers la liste des groupes complets pour l’utilisateur est inclus. Pour les jetons JWT en tant que revendication distribuée, pour SAML en tant que nouvelle revendication à la place de la revendication `groups`. <br><br>**Exemple de valeur JWT** : <br> `"groups":"src1"` <br> `"_claim_sources` : `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `preferred_username` | Chaîne | Nom d’utilisateur principal qui représente l’utilisateur. Il peut s’agir d’une adresse e-mail, d’un numéro de téléphone ou d’un nom d’utilisateur générique sans format spécifié. Sa valeur est mutable et peut changer au fil du temps. Dans la mesure où elle est mutable, cette valeur ne doit pas être utilisée pour prendre des décisions d’autorisation.  Elle peut cependant être utilisée pour les conseils relatifs au nom d’utilisateur. Le `profile` étendue est requise pour recevoir cette revendication. |
| `name` | Chaîne | Fournit une valeur contrôlable de visu qui identifie le sujet du jeton. La valeur n’est pas garantie pour être unique, elle est mutable et il est conçu pour être utilisé uniquement à des fins d’affichage. Le `profile` étendue est requise pour recevoir cette revendication. |
| `oid` | Chaîne, GUID | Identificateur immuable pour un objet dans la plateforme d’identité Microsoft, dans cet exemple, un compte d’utilisateur. Vous pouvez également utiliser cela pour effectuer des vérifications d’autorisation en toute sécurité et en tant que clé dans les tables de base de données. Cet ID identifie de manière unique l’utilisateur entre les applications ; deux applications différentes se connectant au même utilisateur auront la même valeur dans la revendication `oid`. Ainsi, `oid` peut être utilisé lors de la formulation de requêtes auprès de services Microsoft en ligne, tels que Microsoft Graph. Microsoft Graph renverra cet ID en tant que propriété `id` pour un compte d’utilisateur donné. Étant donné que le `oid` permet à plusieurs applications de faire correspondre les utilisateurs, les `profile` étendue est requise pour recevoir cette revendication. Si un seul utilisateur existe dans plusieurs locataires, l’utilisateur contient un ID d’objet différent dans chaque locataire, elles sont considérées comme des comptes différents, même si l’utilisateur se connecte à chaque compte avec les informations d’identification. |
| `rh` | Chaîne opaque | Revendication interne utilisée par Azure pour revalider des jetons. Ressources ne doivent pas utiliser cette revendication. |
| `scp` | Chaîne, liste d’étendues séparées par des espaces | Ensemble des étendues exposées par votre application pour lesquelles l’application client a requis (et reçu) un consentement. Votre application doit vérifier la validité de ces étendues et prendre des décisions d’autorisation en fonction de leur valeur. Uniquement inclus pour les [jetons utilisateur](#user-and-application-tokens). |
| `roles` | Tableau de chaînes, une liste d’autorisations | Ensemble des autorisations exposées par votre application que l'application requérante est autorisée à appeler. Pour [jetons d’application](#user-and-application-tokens), elle est utilisée pendant la [informations d’identification client](v1-oauth2-client-creds-grant-flow.md) flux à la place des étendues de l’utilisateur.  Pour [jetons utilisateur](#user-and-application-tokens) ce champ est renseigné avec les rôles de l’utilisateur a été affecté sur l’application cible. |
| `sub` | Chaîne, GUID | Principal sur lequel portent les assertions d’informations du jeton, comme l’utilisateur d’une application. Cette valeur est immuable et ne peut pas être réassignée ou réutilisé. Vous pouvez également utiliser cela pour effectuer des vérifications d’autorisation en toute sécurité, telles que lorsque le jeton est utilisé pour accéder à une ressource et peut être utilisé en tant que clé dans les tables de base de données. Étant donné que le sujet est toujours présent dans les jetons émis par Azure AD, nous vous recommandons d’utiliser cette valeur dans un système d’autorisation général. Le sujet est, toutefois, un identificateur par paire ; il est unique à un ID d’application particulière. Par conséquent, si un utilisateur se connecte à deux applications différentes à l’aide de deux ID clients différents, ces applications reçoivent deux valeurs différentes pour la revendication de l’objet. Ceci peut être souhaitable ou non en fonction de vos besoins d’architecture et de confidentialité. |
| `tid` | Chaîne, GUID | Représente le client Azure AD d’où provient l’utilisateur. Pour les comptes professionnels et scolaires, le GUID correspond à l’ID de client immuable de l’organisation à laquelle appartient l’utilisateur. Pour les comptes personnels, la valeur est `9188040d-6c67-4c5b-b112-36a304b66dad`. Le `profile` étendue est requise pour recevoir cette revendication. |
| `unique_name` | Chaîne | Uniquement dans les jetons v1.0. Fournit une valeur contrôlable de visu qui identifie le sujet du jeton. Cette valeur n’est pas garantie pour être unique au sein d’un client et doit être utilisée uniquement à des fins d’affichage. |
| `uti` | Chaîne opaque | Revendication interne utilisée par Azure pour revalider des jetons. Ressources ne doivent pas utiliser cette revendication. |
| `ver` | Chaîne, soit `1.0` ou `2.0` | Indique la version du jeton d’accès. |

#### <a name="v10-basic-claims"></a>Revendications de base v1.0

Les revendications suivantes figureront dans les jetons v1.0 le cas échéant, mais ne sont pas incluses dans les jetons v2.0 par défaut. Si vous utilisez v2.0 et avoir une de ces revendications, demander à l’aide de [revendications facultatives](active-directory-optional-claims.md).

| Revendication | Format | Description |
|-----|--------|-------------|
| `ipaddr`| Chaîne | Adresse IP à partir de laquelle l’utilisateur s’est authentifié. |
| `onprem_sid`| Chaîne, au format [SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Lorsque l’utilisateur dispose d’une authentification locale, cette revendication fournit son SID. Vous pouvez utiliser `onprem_sid` pour l’autorisation dans des applications héritées.|
| `pwd_exp`| int, horodatage UNIX | Indique la date d’expiration du mot de passe de l’utilisateur. |
| `pwd_url`| Chaîne | URL vers laquelle les utilisateurs peuvent être redirigés pour réinitialiser leur mot de passe. |
| `in_corp`| booléenne | Indique si le client se connecte à partir du réseau d’entreprise. Si elles ne sont pas, la revendication n’est pas incluse. |
| `nickname`| Chaîne | Autre nom de l’utilisateur, distinct du nom de famille et du prénom.|
| `family_name` | Chaîne | Fournit le nom de famille de l’utilisateur tel que défini sur l’objet utilisateur. |
| `given_name` | Chaîne | Fournit le prénom de l’utilisateur tel que défini sur l’objet utilisateur. |
| `upn` | Chaîne | Nom d’utilisateur. Il peut s’agir d’un numéro de téléphone, d’une adresse électronique ou d’une chaîne non formatée. Ne doit être utilisé qu’à des fins d’affichage et pour fournir des indications sur le nom d’utilisateur dans les scénarios de réauthentification. |

#### <a name="the-amr-claim"></a>Revendication `amr`

Les identités de Microsoft peuvent s’authentifier de différentes façons, qui peuvent être pertinentes pour votre application. La revendication `amr` est un tableau pouvant contenir plusieurs éléments, par exemple `["mfa", "rsa", "pwd"]`, pour les authentifications qui utilisent à la fois un mot de passe et l’application Authenticator.

| Valeur | Description |
|-----|-------------|
| `pwd` | Authentification par mot de passe : mot de passe Microsoft d’un utilisateur ou clé secrète client d’une application. |
| `rsa` | L’authentification reposait sur la preuve d’une clé RSA, par exemple avec l’[application Microsoft Authenticator](https://aka.ms/AA2kvvu). Cela inclut si l’authentification a été effectuée par un jeton Web JSON auto-signé avec un service appartenant à X509 certificat. |
| `otp` | Code secret à usage unique utilisant un e-mail ou un SMS. |
| `fed` | Une assertion d’authentification fédérée (par exemple JWT ou SAML) a été utilisée. |
| `wia` | Authentification Windows intégrée |
| `mfa` | L’authentification multifacteur a été utilisée. Lorsqu’elle est disponible, les autres méthodes d’authentification sont également incluses. |
| `ngcmfa` | Équivalente à `mfa`, utilisée pour le provisionnement de certains types avancés d’informations d’identification. |
| `wiaormfa`| L’utilisateur a utilisé Windows ou un identifiant MFA pour s’authentifier. |
| `none` | Aucune authentification n’a été effectuée. |

## <a name="validating-tokens"></a>Validation des jetons

Pour valider un jeton id_token ou access_token, votre application doit valider à la fois la signature du jeton et les revendications. Pour valider les jetons d’accès, votre application doit également valider l’émetteur, l’audience et les jetons de signature. Ces éléments doivent être validés d’après les valeurs du document de découverte OpenID. Par exemple, la version indépendant du locataire du document se trouve à [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Le middleware Azure AD intègre des fonctionnalités de validation des jetons d’accès, et vous pouvez parcourir nos [exemples](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) pour en trouver un dans la langue de votre choix. Pour plus d’informations sur la validation explicite d’un jeton JWT, consultez l’[exemple de validation manuelle JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).

Nous fournissons des bibliothèques et des exemples de code qui montrent comment gérer facilement la validation des jetons. Les informations ci-dessous sont fournies pour ceux qui souhaitent comprendre le processus sous-jacent. Plusieurs bibliothèques open source de tiers sont également disponibles pour la validation de JWT - au moins une option de presque chaque plateforme et de la langue ici. Pour plus d’informations sur les exemples de code et les bibliothèques d’authentification Azure AD, reportez-vous aux sections [Bibliothèques d’authentification v1.0](active-directory-authentication-libraries.md) et [Bibliothèques d’authentification v2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Validation de la signature

Un jeton JWT contient trois segments séparés par le caractère `.` . Le premier segment est appelé **l’en-tête**, le second le **corps** et le troisième la **signature**. Le segment de signature peut être utilisé pour valider l’authenticité du jeton afin qu’il soit approuvé par votre application.

Les jetons émis par Azure AD sont signés à l’aide d’algorithmes de chiffrement asymétrique standard, tels que RSA 256. L’en-tête du JWT contient des informations sur la clé et la méthode de chiffrement utilisées pour signer le jeton :

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

La revendication `alg` indique l’algorithme utilisé pour signer le jeton, tandis que la revendication `kid` indique la clé publique utilisée pour signer le jeton.

À tout moment, Azure AD peut signer un jeton id_token à l'aide de l'un des ensembles de paires de clés publique-privée. Étant donné qu'Azure AD alterne le jeu de clés possible de façon périodique, votre application doit être écrite de manière à gérer automatiquement ces changements de clés. Pour vérifier les mises à jour apportées aux clés publiques utilisées par Azure AD, spécifiez une fréquence raisonnable d’environ 24 heures.

Vous pouvez acquérir les données clées de signature nécessaires pour valider la signature à l’aide de la [document de métadonnées OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) situé dans :

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Testez cette [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) dans un navigateur !

Ce document de métadonnées :

* Est un objet JSON contenant plusieurs informations utiles, telles que l’emplacement des différents points de terminaison requis pour effectuer l’authentification OpenID Connect.
* Comprend également un `jwks_uri` qui indique l’emplacement de l’ensemble des clés publiques utilisées pour signer les jetons. Le document JSON situé sous `jwks_uri` contient toutes les informations sur les clés publiques utilisées à cet instant donné. Votre application peut utiliser la revendication `kid` dans l’en-tête JWT pour sélectionner la clé publique utilisée dans ce document pour signer un jeton donné. Il peut ensuite faire la validation des signatures à l’aide de la clé publique appropriée et l’algorithme indiqué.

> [!NOTE]
> Le point de terminaison v1.0 retourne à la fois les revendications `x5t` et `kid`, tandis que le point de terminaison v2.0 répond avec uniquement la revendication `kid`. Nous vous recommandons, à l’avenir, d’utiliser la revendication `kid` pour valider votre jeton.

Effectuer la validation des signatures est en dehors de la portée de ce document : il existe de nombreuses bibliothèques open source disponibles pour vous aider à le faire si nécessaire.  Toutefois, la plateforme Microsoft Identity a un seul jeton de signature extension aux normes - personnalisés de clés de signature.  

Si votre application a des clés de signature personnalisées à la suite à l’aide de la [mappage de revendications](active-directory-claims-mapping.md) fonctionnalité, vous devez ajouter un `appid` interroger le paramètre qui contient l’ID d’application pour obtenir un `jwks_uri` pointant vers votre application de signature de la clé d’informations, qui doit être utilisé pour la validation. Par exemple : `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contient un `jwks_uri` de `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorisation basée sur les revendications

Cette étape est déterminée par la logique métier de votre application, certaines méthodes d’autorisation courantes sont décrites ci-dessous.

* Vérifier le `scp` ou `roles` de revendication vérifier que toutes les étendues présentes correspondent à celles exposées par votre API, et permettent au client d’effectuer l’action demandée.
* Assurez-vous que le client appelant est autorisé à appeler votre API avec l’attribut `appid`.
* Valider l’état de l’authentification du client appelant à l’aide de `appidacr` -il ne doit pas être 0 si les clients publics ne sont pas autorisés à appeler votre API.
* Vérification par rapport à une liste de passé `nonce` de revendications pour vérifier le jeton n’est pas en cours de réexécution.
* Vérifiez que `tid` correspond à un client autorisé à appeler votre API.
* Utilisez la revendication `acr` pour vérifier que l’utilisateur a effectué la MFA. Cela doit être exécuté à l’aide d’un [accès conditionnel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Si vous avez demandé le `roles` ou `groups` revendications dans le jeton d’accès, vérifiez que l’utilisateur est dans le groupe autorisé à effectuer cette action.
  * Pour les jetons récupérés à l’aide du flux implicite, vous devrez probablement interroger le [Microsoft Graph](https://developer.microsoft.com/graph/) de ces données, car il est souvent trop grand pour être contenu dans le jeton.

## <a name="user-and-application-tokens"></a>Jetons d’utilisateur et d’application

Votre application peut recevoir des jetons au nom d’un utilisateur (le flux habituel) ou directement d’une application (via le [flux des informations d’identification du client](v1-oauth2-client-creds-grant-flow.md)). Ces jetons réservés aux applications indiquent que l’appel provient d’une application et qu’aucun utilisateur ne lui est associé. Ces jetons sont traités en grande partie de la même façon, à quelques différences près :

* Les jetons réservés aux applications n’ont pas de revendication `scp`, mais une revendication `roles`. C’est à cet endroit que l’autorisation de l’application est enregistrée (contrairement aux autorisations déléguées). Pour en savoir plus sur les autorisations déléguées et les autorisations d’application, consultez l’autorisation et le consentement dans [v1.0](v1-permissions-and-consent.md)et [v2.0](v2-permissions-and-consent.md).
* De nombreuses revendications propres aux êtres humains sont manquantes, comme `name`.

## <a name="token-revocation"></a>Révocation de jetons

Actualiser les jetons peuvent être invalidés ou révoqués à tout moment, pour différentes raisons. classées dans deux grandes catégories : les délais d’expiration et les révocations.

### <a name="token-timeouts"></a>Délais d’expiration des jetons

* MaxInactiveTime : Si le jeton d’actualisation n’a pas été utilisé dans le délai défini par MaxInactiveTime, le jeton d’actualisation ne sera plus valid.
* MaxSessionAge : si MaxAgeSessionMultiFactor ou MaxAgeSessionSingleFactor ont été définis sur une valeur autre que la valeur par défaut (Until-revoked), une réauthentification est nécessaire après l’écoulement du délai défini dans MaxAgeSession*.
* Exemples :
  * Le client a un MaxInactiveTime de cinq jours et l’utilisateur est en vacances pour une semaine et par conséquent, Azure AD n’a pas vu une nouvelle demande de jeton à partir de l’utilisateur des 7 derniers jours. La prochaine fois que l’utilisateur demande un nouveau jeton, ils trouvent leur jeton d’actualisation a été révoqué, et ils doivent entrer à nouveau leurs informations d’identification.
  * Une application sensible a un MaxAgeSessionSingleFactor d’un jour. Si un utilisateur se connecte le lundi et le mardi (après que 25 heures se sont écoulées), ils devrez s’authentifier de nouveau.

### <a name="revocation"></a>Révocation

|   | Cookie basé sur un mot de passe | Jeton basé sur un mot de passe | Cookie non basé sur un mot de passe | Jeton non basé sur un mots de passe | Jeton client confidentiel |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Le mot de passe expire | Reste actif | Reste actif | Reste actif | Reste actif | Reste actif |
| Mot de passe modifié par l’utilisateur | Révoqué | Révoqué | Reste actif | Reste actif | Reste actif |
| L’utilisateur effectue SSPR | Révoqué | Révoqué | Reste actif | Reste actif | Reste actif |
| L’administrateur réinitialise le mot de passe | Révoqué | Révoqué | Reste actif | Reste actif | Reste actif |
| L’utilisateur révoque ses jetons d’actualisation [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Révoqué | Révoqué | Révoqué | Révoqué | Révoqué |
| L’administrateur révoque tous les jetons d’actualisation pour le locataire [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Révoqué | Révoqué |Révoqué | Révoqué | Révoqué |
| [Authentification unique](v1-protocols-openid-connect-code.md#single-sign-out) sur le Web | Révoqué | Reste actif | Révoqué | Reste actif | Reste actif |

> [!NOTE]
> Une connexion « non basée sur mot de passe » est une connexion où l’utilisateur n’a pas saisi un mot de passe. Par exemple, l’utilisation de votre visage avec Windows Hello, une clé FIDO ou un code PIN.
>
> Il existe un problème connu avec le jeton d’actualisation principal de Windows. Si le jeton d’actualisation principal est obtenu via un mot de passe, puis que l’utilisateur se connecte via Hello, cela ne modifie pas l’origine du jeton d’actualisation principal, et il sera révoqué si l’utilisateur modifie son mot de passe.
>
> Jetons d’actualisation ne sont pas invalidées ou révoqués lorsqu’il est utilisé pour extraire un nouveau jeton d’accès et le jeton d’actualisation.  

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [`id_tokens` dans Azure AD](id-tokens.md).
* En savoir plus sur l’autorisation et le consentement dans [v1.0](v1-permissions-and-consent.md) et [v2.0](v2-permissions-and-consent.md).
