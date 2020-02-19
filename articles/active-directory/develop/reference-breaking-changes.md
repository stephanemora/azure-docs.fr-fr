---
title: Changements importants dans Azure Active Directory | Microsoft Docs
description: Découvrez les modifications apportées aux protocoles Azure AD, qui peuvent avoir un impact sur votre application.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6ed72e5c94191411572c6ab67533141e2fe47d6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185819"
---
# <a name="whats-new-for-authentication"></a>Quelles sont les nouveautés en matière d’authentification ? 

>Recevez des notifications sur les mises à jour de cette page. Il suffit d’ajouter [cette URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) à votre lecteur de flux RSS.

Le système d’authentification modifie et ajoute des fonctionnalités en permanence, afin d’améliorer la sécurité et la conformité aux normes. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Dernières fonctionnalités
- Problèmes connus
- Changements de protocole
- Fonctionnalités dépréciées

> [!TIP] 
> Cette page est mise à jour régulièrement. Donc, consultez-la souvent. Sauf indication contraire, ces modifications sont uniquement mises en place pour les applications récemment inscrites.  

## <a name="upcoming-changes"></a>Changements à venir

Aucun n’est planifié pour l’instant.  Pour connaître les changements qui sont en production ou qui vont y être, consultez les changements ci-dessous. 

## <a name="february-2020"></a>Février 2020 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Les fragments vides sont ajoutés à chaque redirection HTTP à partir du point de terminaison de connexion. 

**Date d’effet** : 8 février 2020

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : Les flux OAuth et OIDC qui utilisent response_type=query : cela couvre le [flux du code d’autorisation](v2-oauth2-auth-code-flow.md) dans certains cas, ainsi que le [flux implicite](v2-oauth2-implicit-grant-flow.md). 

Quand une réponse d’authentification est envoyée à partir de login.microsoftonline.com à une application via la redirection HTTP, le service ajoute un fragment vide à l’URL de réponse.  Cela évite une classe d’attaques de redirection en garantissant que le navigateur efface tous les fragments existants dans la demande d’authentification.  Aucune application ne doit dépendre de ce comportement. 


## <a name="august-2019"></a>Août 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>La sémantique de formulaire POST sera appliquée plus rigoureusement, les espaces et les guillemets seront ignorés

**Date d’effet** : 2 septembre 2019

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : Partout où POST est utilisé ([informations d’identification du client](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [utilisation de code d’autorisation](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) et [utilisation de jeton d’actualisation](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token))

À partir de la semaine du 2 septembre, les demandes d’authentification qui utilisent la méthode POST seront validées à l’aide de normes HTTP plus strictes.  Plus précisément, les espaces et les guillemets doubles (“) ne seront plus supprimés des valeurs du formulaire de demande. Ces modifications ne devraient pas bloquer les clients existants et permettront de s’assurer que les demandes envoyées à Azure AD sont gérées de manière fiable à chaque fois. À l’avenir (voir ci-dessus), nous prévoyons également de rejeter les paramètres dupliqués et d’ignorer la marque d’ordre d'octet dans les demandes. 

Exemple :

Aujourd’hui, `?e=    "f"&g=h` est analysé de la même façon que `?e=f&g=h`, donc `e` == `f`.  Avec ce changement, il est maintenant analysé comme `e` == `    "f"`. Il est peu probable que ce soit un argument valide, et la demande devrait maintenant échouer. 


## <a name="july-2019"></a>Juillet 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Les jetons d’application pour applications à locataire unique sont émis uniquement si l’application cliente existe dans le locataire de ressources

**Date d’effet** : 26 juillet 2019

**Points de terminaison impactés** : [V1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) et [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protocole impacté** : [Informations d’identification du client (jetons d’application uniquement)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Une modification de sécurité a été mise en ligne le 26 juillet qui modifie la façon dont sont émis les jetons d’application (via l’octroi d’informations d’identification du client). Auparavant, les applications étaient autorisées à obtenir des jetons pour appeler une autre application, quelle que soit la présence dans le locataire ou les rôles consentis pour cette application.  Ce comportement a été mis à jour de sorte que pour les ressources (parfois appelées API Web) définies sur un locataire unique (valeur par défaut), l’application cliente doit exister dans le locataire de la ressource.  Notez que le consentement existant entre le client et l’API n’est toujours pas nécessaire, et que les applications doivent toujours effectuer leurs propres vérifications d’autorisation pour s’assurer qu’une revendication `roles` est présente et contient la valeur attendue pour l’API.

Le message d’erreur de ce scénario indique actuellement : 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Pour résoudre ce problème, utilisez l’expérience de consentement de l’administrateur pour créer le principal du service de l’application cliente dans votre locataire, ou créez-le manuellement.  Cette exigence garantit que le locataire a donné l’autorisation d’exécuter l’application au sein du locataire.  

#### <a name="example-request"></a>Exemple de requête

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` Dans cet exemple, le locataire de ressource (autorité) est contoso.com, l’application de ressource est une application à locataire unique appelée `gateway.contoso.com/api` pour le locataire Contoso, et l’application cliente est `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Si l’application cliente a un principal de service dans Contoso.com, cette demande peut continuer.  Toutefois, si ce n’est pas le cas, la demande échoue avec l’erreur ci-dessus.  

Si l’application de la passerelle Contoso était une application mutualisée, la requête continue, peu importe si l’application cliente a un principal de service ou non dans Contoso.com.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Les URI de redirection peuvent désormais contenir des paramètres de chaîne de requête

**Date d’effet** : 22 juillet 2019

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : Tous les flux

Selon la norme [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2), les applications Azure AD peuvent désormais inscrire et utiliser des URI de redirection (réponse) avec des paramètres de requêtes statiques (par exemple, https://contoso.com/oauth2?idp=microsoft) pour les requêtes OAuth 2.0).  Les URI de redirection dynamiques sont toujours interdites, car elles représentent un risque pour la sécurité, et cela ne peut pas être utilisé pour conserver les informations d’état sur une demande d’authentification. Pour cela, utilisez le paramètre `state`.

Le paramètre de requête statique est soumis à la correspondance de chaînes pour les URI de redirection comme toute autre partie de l’URI de redirection. Si aucune chaîne n’est inscrite correspondant à la valeur redirect_uri décodée par l’URI, la demande est rejetée.  Si l’URI est trouvée dans l’inscription de l’application, la chaîne entière sera utilisée pour rediriger l’utilisateur, y compris le paramètre de requête statique. 

Remarque : à ce jour (fin juillet 2019), l’inscription d’application UX dans le portail Azure bloque toujours les paramètres de requête.  Toutefois, vous pouvez modifier le manifeste d’application manuellement pour ajouter des paramètres de requête et effectuer le test dans votre application.  


## <a name="march-2019"></a>Mars 2019

### <a name="looping-clients-will-be-interrupted"></a>Le bouclage des clients sera interrompu

**Date d’effet** : 25 mars 2019

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : Tous les flux

Les applications clientes peuvent parfois ne pas se comporter de manière adéquate, ce qui provoque l’envoi de centaines de demandes pour la même connexion pendant un bref laps de temps.  Ces demandes peuvent réussir ou échouer, mais elles nuisent toutes à l’expérience utilisateur et alourdissent les charges de travail pour l’IDP, ce qui augmente la latence pour tous les utilisateurs et réduit la disponibilité de l’IDP.  Ces applications fonctionnent hors des limites d’une utilisation normale et doivent être mises à jour pour qu’elles se comportent correctement.  

Les clients qui émettent plusieurs fois des demandes dupliquées recevront une erreur `invalid_grant` : `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

La plupart des clients n’auront pas besoin de modifier le comportement pour éviter cette erreur.  Seuls les clients mal configurés (ceux ne disposant pas d’une mise en cache des jetons ou présentant déjà des boucles d’invites) seront affectés par cette erreur.  Les clients sont suivis localement (via des cookies) pour chaque instance individuelle en fonction des facteurs suivants :

* Conseils pour les utilisateurs, le cas échéant

* Étendues ou ressources demandées

* ID client

* URI de redirection

* Mode et type de réponse

Les applications émettant de nombreuses demandes (15 ou plus) dans un bref laps de temps (5 minutes) recevront l’erreur `invalid_grant` leur expliquant qu’ils rencontrent un problème de bouclage.  Les jetons demandés disposent d’une durée de vie suffisamment longue (au moins 10 minutes, 60 minutes par défaut), il n’est donc pas nécessaire de réitérer des demandes pendant la période indiquée.  

Toutes les applications doivent gérer l’erreur `invalid_grant` en affichant une invite interactive au lieu de demander un jeton en mode silencieux.  Pour éviter cette erreur, les clients doivent s’assurer que les jetons qu’ils reçoivent sont correctement mis en cache.


## <a name="october-2018"></a>Octobre 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Impossible de réutiliser les codes d’autorisation

**Date d’effet** : 15 novembre 2018

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : [Flux de code](v2-oauth2-auth-code-flow.md)

À compter du 15 novembre 2018, Azure AD n’acceptera plus les codes d’authentification utilisés pour les applications. Cette modification de sécurité a pour but d’assurer la conformité d’Azure AD avec la spécification OAuth et s’appliquera aux points de terminaison v1 et v2.

Si votre application réutilise des codes d’autorisation pour obtenir des jetons pour différentes ressources, nous vous recommandons d’utiliser le code pour obtenir un jeton d’actualisation, puis d’utiliser ce jeton d’actualisation pour acquérir des jetons supplémentaires pour d’autres ressources. Les codes d’autorisation ne peuvent être utilisés qu’une seule fois, tandis que les jetons d’actualisation peuvent être utilisés plusieurs fois sur différentes ressources. Toute nouvelle application qui tente de réutiliser un code d’authentification pendant le flux de code OAuth obtiendra une erreur invalid_grant.

Pour plus d’informations sur les jetons d’actualisation, voir [Actualisation des jetons d’accès](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Si vous utilisez la bibliothèque ADAL ou MSAL, ceci est géré pour vous par la bibliothèque : remplacez la deuxième instance de « AcquireTokenByAuthorizationCodeAsync » par « AcquireTokenSilentAsync ». 

## <a name="may-2018"></a>Mai 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Impossible d’utiliser les jetons d’ID pour le flux OBO

**Date** : 1er mai 2018

**Points de terminaison impactés** : V1.0 et v2.0

**Protocoles impactés** : Flux implicite et [flux On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md)

Depuis le 1er mai 2018, id_tokens ne peut pas être utilisé en tant qu’assertion dans un flux OBO pour les nouvelles applications. Désormais, des jetons d’accès servent à sécuriser les API, même entre un client et le niveau intermédiaire de la même application. Les applications inscrites avant le 1er mai 2018 continueront à fonctionner et à pouvoir échanger des id_tokens pour un jeton d’accès. Toutefois, cela n’est pas considéré comme une meilleure pratique.

Pour contourner cette modification, vous pouvez procéder comme suit :

1. Créez une API web pour votre application, avec une ou plusieurs étendues. Ce point d’entrée explicite permet d’obtenir un niveau de sécurité et de contrôle plus détaillé.
1. Dans le manifeste de votre application, le [Portail Azure](https://portal.azure.com) ou le [portail d’inscription d’application](https://apps.dev.microsoft.com), assurez-vous que l’application est autorisée à émettre des jetons d’accès via le flux implicite. Cela est contrôlé par la clé `oauth2AllowImplicitFlow`.
1. Quand votre application cliente demande un id_token via `response_type=id_token`, demandez également un jeton d’accès (`response_type=token`) pour l’API web créée précédemment. Par conséquent, lorsque vous utilisez le point de terminaison v2.0, le paramètre `scope` doit ressembler à `api://GUID/SCOPE`. Sur le point de terminaison v1.0, le paramètre `resource` doit être l’URI d’application de l’API web.
1. Transmettez ce jeton d’accès au niveau intermédiaire à la place d’id_token.  
