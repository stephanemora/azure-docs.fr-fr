---
title: Qu’est-ce que l’authentification unique ?
description: Découvrez l’authentification unique pour les applications d’entreprise dans Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q1
ms.openlocfilehash: bb4a38d46a241c902d0718104d9a4030bf84607f
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858750"
---
# <a name="what-is-single-sign-on-in-azure-active-directory"></a>Qu’est-ce que l’authentification unique dans Azure Active Directory ?

Cet article vous fournit des informations sur les options de l’authentification unique (SSO) qui sont à votre disposition, ainsi qu’une introduction à la planification d’un déploiement d’authentification unique lors de l’utilisation d’Azure Active Directory (Azure AD). L’authentification unique est une méthode d’authentification qui permet aux utilisateurs de se connecter avec un ensemble d’informations d’identification à plusieurs systèmes logiciels indépendants. Avec l’authentification unique, un utilisateur n’a pas besoin de se connecter à chaque application dont il se sert. Avec l’authentification unique, les utilisateurs peuvent accéder à toutes les applications dont ils ont besoin sans avoir à s’authentifier avec d’autres informations d’identification. 

De nombreuses applications existent déjà dans Azure AD que vous pouvez utiliser avec l’authentification unique. Vous avez plusieurs options pour l’authentification unique en fonction des besoins de l’application et de la façon dont elle est implémentée. Prenez le temps de planifier votre déploiement de l’authentification unique avant de créer des applications dans Azure AD. La gestion des applications peut être simplifiée à l’aide du portail Mes applications.

## <a name="single-sign-on-options"></a>Options d’authentification unique

Le choix d’une méthode d’authentification unique dépend de la façon dont l’application est configurée pour l’authentification. Les applications cloud peuvent utiliser des options basées sur la fédération comme OpenID Connect, OAuth et SAML. L’application peut également utiliser une authentification unique basée sur mot de passe, une authentification unique basée sur un lien, ou l’authentification unique peut avoir été désactivée.

- **Fédération** - Quand vous configurez l’authentification unique pour qu’elle fonctionne entre plusieurs fournisseurs d’identités, cela s’appelle une fédération. Une implémentation SSO basée sur les protocoles de fédération améliore la sécurité, la fiabilité, les expériences utilisateur et l’implémentation elle-même. 

    Avec l’authentification unique fédérée, Azure AD authentifie l’utilisateur dans l’application en se servant de son compte Azure AD. Cette méthode est prise en charge pour les applications SAML 2.0, WS-Federation ou OpenID Connect. L’authentification unique fédérée est le mode d’authentification unique le plus riche. Utilisez l’authentification unique fédérée avec Azure AD quand une application la prend en charge, au lieu d’une authentification unique basée sur mot de passe et les services de fédération Active Directory (AD FS).

    Dans certains cas, l’option SSO n’est pas présente pour une application d’entreprise. Si l’application a été inscrite en utilisant **Inscriptions d’applications** dans le portail, la fonctionnalité d’authentification unique est configurée pour utiliser OpenID Connect et OAuth par défaut. Dans ce cas, l’option d’authentification unique n’apparaît pas dans le volet de navigation sous Applications d’entreprise.

    L’authentification unique n’est pas disponible lorsqu’une application est hébergée dans un autre locataire. L’authentification unique n’est pas non plus disponible si votre compte n’a pas les autorisations nécessaires (Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service). Les autorisations peuvent également être à l’origine d’un scénario dans lequel vous pouvez ouvrir l’authentification unique, mais vous ne pourrez pas l’enregistrer.

    > [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]

- **Mot de passe** - Pour l’authentification unique, les applications locales peuvent utiliser les méthodes suivantes : basée sur mot de passe, authentification Windows intégrée, basée sur en-tête, basée sur lien. Les choix pour les applications locales fonctionnent quand les applications sont configurées pour le proxy d’application.

    Avec l’authentification par mot de passe, les utilisateurs se connectent à l’application avec un nom d’utilisateur et un mot de passe la première fois qu’ils y accèdent. Après la première authentification, Azure AD fournit le nom d’utilisateur et le mot de passe à l’application. L’authentification unique par mot de passe permet de sécuriser le stockage et la lecture des mots de passe des applications avec une extension de navigateur web ou une application mobile. Cette option utilise le processus de connexion existant fourni par l’application, permet à l’administrateur de gérer les mots de passe et n’a pas besoin que l’utilisateur connaisse le mot de passe.

- **Liée** - L’authentification liée peut fournir une expérience utilisateur homogène lorsque vous migrez des applications sur un certain laps de temps. Si vous migrez des applications vers Azure AD, vous pouvez utiliser l’authentification unique basée sur des liens pour publier rapidement des liens vers toutes les applications que vous voulez migrer. Les utilisateurs peuvent trouver tous les liens dans les portails Mes applications ou Microsoft 365.

    Une fois qu’un utilisateur s’est authentifié avec une application liée, un compte doit être créé avant que l’utilisateur obtienne l’accès par authentification unique. Le provisionnement de ce compte peut se produire automatiquement, ou manuellement par un administrateur. Vous ne pouvez pas appliquer de stratégies d’accès conditionnel ou d’authentification multifacteur à une application liée, car une application liée ne fournit pas de fonctionnalités d’authentification unique via Azure AD. Quand vous configurez une application liée, vous ajoutez juste un lien qui apparaît pour lancer l’application.

- **Désactivée** - Lorsque l’authentification unique est désactivée, elle n’est pas disponible pour l’application. Quand l’authentification unique est désactivée, les utilisateurs peuvent avoir besoin de s’authentifier deux fois. Les utilisateurs s’authentifient d’abord auprès d’Azure AD, puis ils se connectent à l’application.

    Désactivez l’authentification unique quand :

    - Vous n’êtes pas prêt à intégrer cette application à l’authentification unique Azure AD
    - Vous testez d’autres aspects de l’application
    - Une application locale ne demande pas aux utilisateurs de s’authentifier, mais vous le voulez. Si l’authentification unique est désactivée, l’utilisateur doit s’authentifier.

    Si vous avez configuré l’application pour l’authentification unique SAML lancée par le fournisseur de services et que vous changez le mode SSO en le définissant sur Désactivé, cela n’empêche par les utilisateurs de se connecter à l’application en dehors du portail Mes applications. Pour cela, vous devez [désactiver la possibilité pour les utilisateurs de se connecter](disable-user-sign-in-portal.md).

## <a name="plan-sso-deployment"></a>Planifier le déploiement de l’authentification unique

Les applications web sont hébergées par différentes entreprises et mises à disposition en tant que service. Voici quelques exemples connus d’applications web : Microsoft 365, GitHub et Salesforce. Il en existe des milliers d’autres. Les utilisateurs accèdent aux applications web à l’aide d’un navigateur web sur leur ordinateur. L’authentification unique permet aux utilisateurs de naviguer entre les différentes applications web sans avoir à se connecter plusieurs fois.

La façon d’implémenter l’authentification unique dépend de là où l’application est hébergée. L’hébergement est important en raison de la façon dont le trafic réseau est routé pour accéder à l’application. Les utilisateurs n’ont pas besoin d’utiliser Internet pour accéder aux applications locales (hébergées sur un réseau local). Si l’application est hébergée dans le cloud, les utilisateurs ont besoin d’Internet pour l’utiliser. Les applications hébergées dans le cloud sont également appelées applications SaaS (Software as a Service).

Pour les applications cloud, les protocoles de fédération sont utilisés. Vous pouvez également utiliser l’authentification unique pour les applications locales. Vous pouvez utiliser le Proxy d’application afin de configurer l’accès pour votre application locale. Pour plus d’informations, consultez Accès à distance aux applications locales via le service Proxy d’application Azure AD.

## <a name="my-apps"></a>Mes applications

Si vous utilisez une application, vous ne vous souciez probablement pas beaucoup des détails de l’authentification unique. Vous voulez juste utiliser les applications qui vous rendent productif sans avoir à taper constamment votre mot de passe. Vous pouvez rechercher et gérer vos applications dans le portail Mes applications. 

## <a name="next-steps"></a>Étapes suivantes

- [Activer l’authentification unique](add-application-portal-setup-sso.md)
