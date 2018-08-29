---
title: 'Azure Active Directory v2.0 : limites et restrictions du point de terminaison | Microsoft Docs'
description: Une liste des limitations et restrictions associées au point de terminaison v2.0 Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 9245e85781482a3aa1e45333d8e8a748983675b6
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246495"
---
# <a name="should-i-use-the-v20-endpoint"></a>Dois-je utiliser le point de terminaison v2.0 ?

Quand vous créez des applications qui s’intègrent à Azure Active Directory (Azure AD), vous devez déterminer si les protocoles d’authentification et le point de terminaison v2.0 répondent à vos besoins. Le point de terminaison d’origine d’Azure AD est toujours intégralement pris en charge. À certains égards, il est plus riche en fonctionnalités que le point de terminaison v2.0. Toutefois, le point de terminaison v2.0 [présente des avantages significatifs](azure-ad-endpoint-comparison.md) pour les développeurs.

Voici une suggestion simplifiée pour les développeurs à ce stade :

* Si vous devez prendre en charge des comptes personnels Microsoft dans votre application, utilisez le point de terminaison v2.0. Mais avant cela, tenez compte des limitations abordées dans cet article.
* Si votre application ne doit prendre en charge que des comptes professionnels et scolaires Microsoft, n’utilisez pas le point de terminaison v2.0. Dans ce cas, consultez le [guide de développement Azure AD](azure-ad-developers-guide.md).

Le point de terminaison v2.0 va évoluer et les restrictions répertoriées ici seront éliminées. Ainsi, vous n’aurez qu’à utiliser le point de terminaison v2.0. En attendant, cet article est là pour vous aider à déterminer si le point de terminaison v2.0 répond à vos besoins. Nous continuerons à mettre à jour cet article pour refléter l’état actuel du point de terminaison v2.0. Consultez-le régulièrement pour réévaluer vos besoins par rapport aux fonctionnalités de la version 2.0.

Si vous disposez d’une application associée à Azure AD qui ne recourt pas au point de terminaison v2.0, il n’est pas nécessaire de repartir de zéro. À l’avenir, nous vous fournirons un moyen d’utiliser vos applications Azure AD existantes avec le point de terminaison v2.0.

## <a name="restrictions-on-app-types"></a>Restrictions concernant les types d’applications

Actuellement, les types d’applications suivants ne sont pas pris en charge par le point de terminaison v2.0. Pour obtenir une description des types d’applications pris en charge, consultez [Types d’applications pour le point de terminaison Azure Active Directory v2.0](v2-app-types.md).

### <a name="standalone-web-apis"></a>API Web autonome

Vous pouvez utiliser le point de terminaison v2.0 pour [générer une API web sécurisée avec OAuth 2.0](v2-app-types.md#web-apis). Toutefois, cette API web peut recevoir uniquement les jetons d’une application ayant le même ID d’application. Vous ne pouvez pas accéder à une API web à partir d’un client qui a un ID d’application différent. Ce client ne pourra pas demander ou obtenir d’autorisation d’accès à votre API web.

Pour savoir comment créer une API web qui accepte des jetons d’un client ayant le même ID d’application, consultez les exemples d’API web de point de terminaison v2.0 de la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="restrictions-on-app-registrations"></a>Restrictions sur les inscriptions d’application

À l’heure actuelle, pour chaque application que vous souhaitez intégrer au point de terminaison v2.0, vous devez créer une inscription d’application dans le nouveau [portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Les applications de compte Microsoft ou Azure AD existantes ne sont pas compatibles avec le point de terminaison v2.0. Les applications qui sont inscrites dans un portail autre que le portail d’inscription des applications ne sont pas compatibles avec le point de terminaison v2.0. À l’avenir, nous prévoyons de fournir un moyen d’utiliser des applications existantes en tant qu’applications v2.0. Il n’existe aucun chemin de migration permettant à une application existante de fonctionner avec le point de terminaison v2.0.

De plus, les inscriptions d’applications que vous créez dans le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ont les caractéristiques suivantes :

* Seuls deux secrets d’application sont autorisés par ID d’application.
* Une application inscrite par un utilisateur dans un compte Microsoft personnel ne peut être affichée et gérée que par un compte de développeur. Elle ne peut pas être partagée entre plusieurs développeurs. Si vous souhaitez partager votre inscription d’application avec plusieurs développeurs, vous pouvez créer l’application en vous connectant au portail d’inscription avec un compte Azure AD.
* Il existe plusieurs restrictions quant au format de l’URI de redirection autorisé. Pour plus d’informations sur les URI de redirection, consultez la section suivante.

## <a name="restrictions-on-redirect-uris"></a>Restrictions concernant les URI de redirection

Les applications inscrites dans le portail d’inscription des applications sont limitées à un jeu restreint de valeurs d’URI de redirection. L’URI de redirection pour les services et applications web doit commencer par le schéma `https`, et toutes les valeurs d’URI de redirection doivent partager un seul domaine DNS. Par exemple, vous ne pouvez pas inscrire une application web contenant l’un de ces URI de redirection :

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Le système d’inscription compare le nom DNS complet de l’URI de redirection existante au nom DNS de l’URI de redirection que vous ajoutez. La demande d’ajout du nom DNS échoue si l’une des conditions suivantes est remplie :  

* Le nom DNS complet de la nouvelle URI de redirection ne correspond pas au nom DNS de l’URI de redirection existante.
* Le nom DNS complet de la nouvelle URI de redirection n’est pas un sous-domaine de l’URI de redirection existante.

Par exemple, si l’application a cet URI de redirection :

`https://login.contoso.com`

Vous pouvez la compléter comme suit :

`https://login.contoso.com/new`

Dans ce cas, le nom DNS correspond exactement. Vous pouvez aussi définir l’URI suivant :

`https://new.login.contoso.com`

Dans ce cas, vous faites référence à un sous-domaine DNS de login.contoso.com. Si vous souhaitez disposer d’une application avec login-east.contoso.com et login-west.contoso.com en tant qu’URI de redirection, vous devez ajouter ces URI de redirection dans l’ordre suivant :

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Vous pouvez ajouter les deux derniers car il s’agit de sous-domaines du premier URI de redirection, contoso.com. Cette limitation sera supprimée dans une version ultérieure.

Notez également qu’une même application ne peut avoir que 20 URL de réponse.

Pour savoir comment inscrire une application dans le portail d’inscription des applications, consultez [Inscription d’une application avec le point de terminaison v2.0](quickstart-v2-register-an-app.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Restrictions concernant les bibliothèques et les SDK

Actuellement, la prise en charge des bibliothèques pour le point de terminaison v2.0 est limitée. Si vous souhaitez utiliser le point de terminaison v2.0 dans une application de production, vous disposez des options suivantes :

* Si vous générez une application web, vous pouvez en toute sécurité utiliser le middleware Microsoft mis à la disposition générale côté serveur pour vous connecter et procéder à la validation des jetons. Vous recourrez notamment au middleware OWIN Open ID Connect pour ASP.NET et au plug-in Node.js Passport. Pour obtenir des exemples de code qui utilisent le middleware Microsoft, consultez la section [Prise en main](active-directory-appmodel-v2-overview.md#getting-started).
* Si vous créez une application mobile ou de bureau, vous pouvez utiliser l’une de nos bibliothèques d’authentification Microsoft (MSAL). Bien qu’il s’agisse de versions préliminaires, ces bibliothèques sont utilisables dans des applications de production. Pour plus d’informations sur les conditions d’utilisation de la préversion et sur les bibliothèques disponibles, consultez la [documentation de référence sur les bibliothèques d’authentification](reference-v2-libraries.md).
* Vous pouvez intégrer les plateformes non couvertes par les bibliothèques Microsoft, dans le point de terminaison v2.0 en envoyant et en recevant directement des messages de protocole dans le code de votre application. Les protocoles v2.0 OpenID Connect et OAuth [sont explicitement documentés](active-directory-v2-protocols.md) pour vous aider à effectuer une telle intégration.
* Pour finir, vous pouvez utiliser les bibliothèques open source Open ID Connect et OAuth pour procéder à l’intégration avec le point de terminaison v2.0. Le protocole v2.0 devrait être compatible avec de nombreuses bibliothèques de protocole open source sans modification majeure. La disponibilité de ces types de bibliothèques varie en fonction de la langue et de la plateforme. Les sites web [Open ID Connect](http://openid.net/connect/) et [OAuth 2.0](http://oauth.net/2/) contiennent une liste à jour des implémentations les plus courantes. Pour plus d’informations, consultez [Azure Active Directory v2.0 et bibliothèques d’authentification](reference-v2-libraries.md), ainsi que la liste des bibliothèques clientes open source et des exemples qui ont été testés avec le point de terminaison v2.0.
  * Pour référence, le point de terminaison `.well-known` pour le point de terminaison v2.0 commun est `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.  Remplacez `common` par votre ID de locataire pour obtenir des données spécifiques à votre locataire.  

## <a name="restrictions-on-protocols"></a>Restrictions sur les protocoles

Le point de terminaison 2.0 ne prend pas en charge SAML ou WS-Federation, mais uniquement Open ID Connect et OAuth 2.0. Certaines fonctionnalités des protocoles OAuth n’ont pas été intégrées dans le point de terminaison v2.0.

Les fonctionnalités de protocole suivantes *ne sont pas disponibles* dans le point de terminaison v2.0 :

* Actuellement, la revendication `email` est retournée uniquement si une revendication facultative est configurée, et si scope=email a été spécifié dans la requête. Toutefois, ce comportement va changer avec la mise à jour du point de terminaison v2.0 en vue d’une meilleure conformité aux nomes Open ID Connect et OAuth 2.0.
* Le point de terminaison v2.0 ne prend pas en charge l’émission de revendications de rôle ou de groupe dans les jetons d’ID.
* Le point de terminaison v2.0 ne prend pas en charge l’[octroi des informations de mot de passe du propriétaire de la ressource OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3).

De plus, il ne prend en charge aucun protocole SAML ou WS-Federation.

Pour mieux comprendre l’étendue de la fonctionnalité de protocole prise en charge dans le point de terminaison v2.0, consultez notre page de [référence sur les protocoles OAuth 2.0 et OpenID Connect](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrictions concernant les comptes professionnels et scolaires

Si vous avez utilisé la bibliothèque ADAL (Active Directory Authentication Library) dans des applications Windows, vous avez peut-être tiré parti de l’authentification intégrée Windows, qui utilise l’octroi d’assertions SAML (Security Assertion Markup Language). Avec cet octroi, les utilisateurs de locataires Azure AD fédérés peuvent s’authentifier en mode silencieux auprès de leur instance d’Active Directory locale sans entrer leurs informations d’identification. À l’heure actuelle, l’octroi d’assertion SAML n’est pas pris en charge sur le point de terminaison v2.0.
