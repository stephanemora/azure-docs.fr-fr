---
title: Changements importants dans Azure Active Directory | Microsoft Docs
description: Découvrez les modifications apportées aux protocoles Azure AD, qui peuvent avoir un impact sur votre application.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401299"
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

Aucun n’est planifié pour l’instant. 

## <a name="march-2019"></a>Mars 2019

### <a name="looping-clients-will-be-interrupted"></a>Les clients de bouclage sera interrompue

**Date d’effet** : 25 mars 2019

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : Tous les flux

Les applications clientes peuvent parfois mener émettant des centaines de la même demande de connexion pendant une courte période de temps.  Ces demandes peuvent être ou non réussies, mais toutes contribuent à l’expérience utilisateur médiocre et charges de travail renforcées pour le fournisseur d’identité, accroître la latence pour tous les utilisateurs et réduire la disponibilité du fournisseur d’identité.  Ces applications sont en dehors des limites d’utilisation normale et doivent être mis à jour pour se comporter correctement.  

Les clients qui émettent des demandes dupliquées plusieurs fois recevront un `invalid_grant` erreur : `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`. 

La plupart des clients ne devrez pas modifier le comportement pour éviter cette erreur.  Seuls les clients mal configurées (ceux sans mise en cache de jeton ou ceux présentant des boucles d’invite déjà) seront affectées par cette erreur.  Les clients sont suivis sur une base par instance localement (par le biais de cookie) sur les facteurs suivants :

* Indicateur de l’utilisateur, le cas échéant

* Étendues ou la ressource demandée

* ID client

* URI de redirection

* Mode et le type de réponse

Applications effectuant des demandes multiples (15 +) sur une courte période de temps (5 minutes) recevront un `invalid_grant` message d’erreur expliquant qu’ils sont en boucle.  Les jetons des durées de vie suffisamment longue durées (minimum de 10 minutes, 60 minutes par défaut), par conséquent, répéter les demandes sur cette période de temps demandés ne sont pas nécessaires.  

Toutes les applications doivent gérer `invalid_grant` en affichant une invite interactive, plutôt qu’en mode silencieux demande un jeton.  Pour éviter cette erreur, les clients doivent s’assurer qu’ils sont correctement la mise en cache les jetons qu’ils reçoivent.


## <a name="october-2018"></a>Octobre 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Impossible de réutiliser les codes d’autorisation

**Date d’effet** : 15 novembre 2018

**Points de terminaison impactés** : V1.0 et v2.0

**Protocole impacté** : [Flux de code](v2-oauth2-auth-code-flow.md)

À compter du 15 novembre 2018, Azure AD n’acceptera plus les codes d’authentification utilisés pour les applications. Cette modification de sécurité a pour but d’assurer la conformité d’Azure AD avec la spécification OAuth et s’appliquera aux points de terminaison v1 et v2.

Si votre application réutilise des codes d’autorisation pour obtenir des jetons pour différentes ressources, nous vous recommandons d’utiliser le code pour obtenir un jeton d’actualisation, puis d’utiliser ce jeton d’actualisation pour acquérir des jetons supplémentaires pour d’autres ressources. Les codes d’autorisation ne peuvent être utilisés qu’une seule fois, tandis que les jetons d’actualisation peuvent être utilisés plusieurs fois sur différentes ressources. Toute nouvelle application qui tente de réutiliser un code d’authentification pendant le flux de code OAuth obtiendra une erreur invalid_grant.

Pour plus d’informations sur les jetons d’actualisation, voir [Actualisation des jetons d’accès](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  Si vous utilisez la bibliothèque ADAL ou MSAL, ceci est géré pour vous par la bibliothèque : remplacez la deuxième instance de « AcquireTokenByAuthorizationCodeAsync » par « AcquireTokenSilentAsync ». 

## <a name="may-2018"></a>Mai 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Impossible d’utiliser les jetons d’ID pour le flux OBO

**Date** : 1er mai 2018

**Points de terminaison impactés** : V1.0 et v2.0

**Protocoles impactés** : Flux implicite et [flux OBO](v1-oauth2-on-behalf-of-flow.md)

Depuis le 1er mai 2018, id_tokens ne peut pas être utilisé en tant qu’assertion dans un flux OBO pour les nouvelles applications. Désormais, des jetons d’accès servent à sécuriser les API, même entre un client et le niveau intermédiaire de la même application. Les applications inscrites avant le 1er mai 2018 continueront à fonctionner et à pouvoir échanger des id_tokens pour un jeton d’accès. Toutefois, cela n’est pas considéré comme une meilleure pratique.

Pour contourner cette modification, vous pouvez procéder comme suit :

1. Créez une API web pour votre application, avec une ou plusieurs étendues. Ce point d’entrée explicite permet d’obtenir un niveau de sécurité et de contrôle plus détaillé.
1. Dans le manifeste de votre application, le [Portail Azure](https://portal.azure.com) ou le [portail d’inscription d’application](https://apps.dev.microsoft.com), assurez-vous que l’application est autorisée à émettre des jetons d’accès via le flux implicite. Cela est contrôlé par la clé `oauth2AllowImplicitFlow`.
1. Quand votre application cliente demande un id_token via `response_type=id_token`, demandez également un jeton d’accès (`response_type=token`) pour l’API web créée précédemment. Par conséquent, lorsque vous utilisez le point de terminaison v2.0, le paramètre `scope` doit ressembler à `api://GUID/SCOPE`. Sur le point de terminaison v1.0, le paramètre `resource` doit être l’URI d’application de l’API web.
1. Transmettez ce jeton d’accès au niveau intermédiaire à la place d’id_token.  
