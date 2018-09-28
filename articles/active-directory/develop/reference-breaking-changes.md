---
title: Changements importants dans Azure Active Directory | Microsoft Docs
description: Modifications apportées aux protocoles Azure AD qui peuvent avoir un impact sur votre application.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 14217d03cdb56c5c641ab8f8c3fc0748e8e815a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987755"
---
# <a name="whats-new-for-authentication"></a>Quelles sont les nouveautés en matière d’authentification ? 

|
>Pour être averti de la mise à disposition de mises à jour sur cette page, ajoutez cette [URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) à votre lecteur de flux RSS.

Le système d’authentification modifie et ajoute des fonctionnalités en permanence, afin d’améliorer la sécurité et la conformité aux normes. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Dernières fonctionnalités
- Problèmes connus
- Changements de protocole
- Fonctionnalités dépréciées

> [!TIP] 
> Cette page est mise à jour régulièrement. Donc, consultez-la souvent. Sauf indication contraire, ces modifications sont uniquement mises en place pour les applications récemment inscrites.  

## <a name="upcoming-changes"></a>Changements à venir

### <a name="authorization-codes-can-no-longer-be-reused"></a>Impossible de réutiliser les codes d’autorisation

**Date d’entrée en vigueur** : 10 octobre 2018 **Points de terminaison concernés** : à la fois v1.0 et v2.0 **Protocole concerné** : [flux de code](v2-oauth2-auth-code-flow.md)

À compter du 10 octobre 2018, Azure AD n’acceptera plus les codes d’authentification déjà utilisés pour les nouvelles applications. Les applications créées avant le 10 octobre 2018 pourront toujours réutiliser les codes d’authentification. Cette modification de sécurité a pour but d’assurer la conformité d’Azure AD avec la spécification OAuth et s’appliquera aux points de terminaison v1 et v2.

Si votre application réutilise des codes d’autorisation pour obtenir des jetons pour différentes ressources, nous vous recommandons d’utiliser le code pour obtenir un jeton d’actualisation, puis d’utiliser ce jeton d’actualisation pour acquérir des jetons supplémentaires pour d’autres ressources. Les codes d’autorisation ne peuvent être utilisés qu’une seule fois, tandis que les jetons d’actualisation peuvent être utilisés plusieurs fois sur différentes ressources. Toute nouvelle application qui tente de réutiliser un code d’authentification pendant le flux de code OAuth obtiendra une erreur invalid_grant.

Pour plus d’informations sur les jetons d’actualisation, voir [Actualisation des jetons d’accès](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

## <a name="may-2018"></a>Mai 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Impossible d’utiliser les jetons d’ID pour le flux OBO

**Date** : 1er mai 2018 **Points de terminaison concernés** : à la fois v1.0 et v2.0 **Protocoles concernés** : flux implicite et [flux OBO](v1-oauth2-on-behalf-of-flow.md)

Depuis le 1er mai 2018, id_tokens ne peut pas être utilisé en tant qu’assertion dans un flux OBO pour les nouvelles applications.  Désormais, des jetons d’accès servent à sécuriser les API, même entre un client et le niveau intermédiaire de la même application.  Les applications inscrites avant le 1er mai 2018 continueront à fonctionner et à pouvoir échanger id_tokens pour un jeton d’accès. Toutefois, cela n'est pas considéré comme une meilleure pratique.

Pour contourner cette modification, vous pouvez procéder comme suit :

1. Créez une API web pour votre application de niveau intermédiaire, avec une ou plusieurs étendues.  Cela permettra de sécurité et contrôle plus précis.
1. Dans le manifeste de l’application, le [Azure Portal](https://portal.azure.com) ou le [portail d’inscription d’application](https://apps.dev.microsoft.com), assurez-vous que l’application est autorisée à émettre des jetons d’accès via le flux implicite. Cela est contrôlé via la clé `oauth2AllowImplicitFlow`.
1. Quand votre application cliente demande un id_token via `response_type=id_token`, demandez également un jeton d’accès (`response_type=token`) pour l’API web créée précédemment.  Par conséquent, lorsque vous utilisez le point de terminaison v2.0, le paramètre `scope` doit ressembler à `api://GUID/SCOPE`.  Sur le point de terminaison v1.0, le paramètre `resource` doit être l’URI d’application de l’API web.
1. Transmettez ce jeton d’accès au niveau intermédiaire à la place d’id_token.  