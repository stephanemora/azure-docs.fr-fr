---
title: API REST Azure Active Directory - Authentification
description: Utiliser Azure Active Directory pour s’authentifier auprès d’Azure App Configuration à l’aide de l’API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423658"
---
# <a name="azure-active-directory-authentication"></a>Authentification Azure Active Directory

Les requêtes HTTP peuvent être authentifiées à l’aide du schéma d’authentification du **Porteur** avec un jeton obtenu à partir d’Azure Active Directory (Azure AD). Ces demandes doivent être transmises via le protocole TLS.

## <a name="prerequisites"></a>Prérequis

Le principal qui sera utilisé pour demander un jeton Azure AD doit être affecté à l’un des [rôles de configuration de l’application](./rest-api-authorization-azure-ad.md) applicables

Fournissez chaque demande avec tous les en-têtes HTTP requis pour l’authentification. Voici le minimum requis :

|  En-tête de requête | Description  |
| --------------- | ------------ |
| **Autorisation** | Informations d’authentification requises par le schéma du **Porteur**. Le format et les détails sont expliqués ci-dessous. |

**Exemple :**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Acquisition d’un jeton Azure Active Directory

Avant d’acquérir un jeton Azure AD, il est nécessaire d’identifier l’utilisateur à utiliser pour l’authentification, l’audience pour laquelle il demande le jeton et le point de terminaison Azure AD (autorité) à utiliser.

### <a name="audience"></a>Public visé

Le jeton Azure AD doit être demandé pour une audience appropriée. Pour Azure App Configuration, l’une des audiences suivantes doit être spécifiée lors de la demande d’un jeton. L’audience peut également être considérée comme la « ressource » pour laquelle le jeton est demandé.

- {configurationStoreName}.azconfig.io
- *.azconfig.io

> [!IMPORTANT]
> Lorsque l’audience demandée est {configurationStoreName}.azconfig. IO, elle doit correspondre exactement à l’en-tête de demande « Hôte » (sensible à la casse) utilisé pour envoyer la demande.

### <a name="azure-ad-authority"></a>Autorité Azure AD

L’autorité Azure AD est le point de terminaison utilisé pour acquérir un jeton Azure AD. Il se présente sous la forme `https://login.microsoftonline.com/{tenantId}` . Le segment `{tenantId}` fait référence à l’ID de locataire Azure Active Directory auquel appartient l’utilisateur/l’application qui tente de s’authentifier.

### <a name="authentication-libraries"></a>Bibliothèques d’authentification

Azure fournit un ensemble de bibliothèques nommées ADAL (Azure Active Directory Authentication Libraries) pour simplifier le processus d’acquisition d’un jeton Azure AD. Ces bibliothèques sont générées pour plusieurs langues. Vous trouverez la documentation [ici](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**Erreurs**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Raison :** l’en-tête de demande d’autorisation avec le schéma du Porteur n’est pas fourni.
**Solution :** fournissez un en-tête de requête HTTP ```Authorization``` valide

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Raison :** le jeton Azure AD n'est pas valide.
**Solution :** obtenez un jeton Azure AD auprès de l’autorité Azure AD et assurez-vous que l’audience appropriée est utilisée.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Raison :** le jeton Azure AD n'est pas valide.
**Solution :** Obtenez un jeton Azure AD auprès de l’autorité de Azure AD et assurez-vous que le locataire Azure AD est celui associé à l’abonnement auquel appartient le magasin de configuration. Cette erreur peut se produire si le principal appartient à plusieurs locataires Azure AD.
