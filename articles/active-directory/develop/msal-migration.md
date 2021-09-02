---
title: Migration vers la Bibliothèque d’authentification Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Découvrez les différences entre la Bibliothèque d’authentification Microsoft (MSAL) et la Bibliothèque d’authentification Azure AD (ADAL), et apprenez à effectuer une migration vers MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 145302d3569c2fc9c5d5bd58a5f9b7ccefac0b8e
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122535117"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Migration d’applications vers la Bibliothèque d’authentification Microsoft (MSAL)

Si l’une de vos applications utilise la bibliothèque d’authentification Azure Active Directory (ADAL) pour les fonctionnalités d’authentification et d’autorisation, il est temps de les migrer vers la [bibliothèque d’authentification Microsoft (MSAL)](msal-overview.md#languages-and-frameworks).

- Toute la prise en charge et le développement Microsoft pour la Bibliothèque d'authentification Active Directory (ADAL), y compris les correctifs de sécurité, se terminent le 30 juin 2022.
- Aucune nouvelle fonctionnalité n’a été ajoutée à l’ADAL depuis le 30 juin 2020.

> [!WARNING]
> Si vous choisissez de ne pas migrer vers la MSAL avant la fin de la prise en charge de l’ADAL le 30 juin 2022, vous mettez en péril la sécurité de votre application. Les applications existantes qui utilisent l’ADAL continueront de fonctionner après la date de fin de prise en charge, mais Microsoft ne publiera plus de correctifs de sécurité pour l’ADAL.

## <a name="why-switch-to-msal"></a>Pourquoi basculer vers la MSAL ?

La MSAL offre plusieurs avantages par rapport à l’ADAL, notamment les fonctionnalités suivantes : 

|Fonctionnalités|MSAL|ADAL|
|---------|---------|---------|
|**Sécurité**|||
|Correctifs de sécurité au-delà du 30 juin 2022|![Correctifs de sécurité au-delà du 30 juin 2022 : la MSAL fournit la fonctionnalité][y]|![Correctifs de sécurité au-delà du 30 juin 2022  l’ADAL ne fournit pas la fonctionnalité][n]|
| Actualisez et révoquez de manière proactive les jetons en fonction de stratégie ou d’événements critiques pour Microsoft Graph et d’autres API qui prennent en charge l’[évaluation continue de l’accès (CAE)](app-resilience-continuous-access-evaluation.md).|![Actualisez et révoquez de manière proactive les jetons en fonction de stratégie ou d’événements critiques pour Microsoft Graph et d’autres API qui prennent en charge l’évaluation continue de l’accès (CAE) : la MSAL fournit la fonctionnalité][y]|![Actualisez et révoquez de manière proactive les jetons en fonction de stratégie ou d’événements critiques pour Microsoft Graph et d’autres API qui prennent en charge l’évaluation continue de l’accès (CAE) : l’ADAL ne fournit pas la fonctionnalité][n]|
| Normes conformes à OAuth v 2.0 et à OpenID Connect (OIDC) |![Normes conformes à OAuth v 2.0 et à OpenID Connect (OIDC) : la MSAL fournit la fonctionnalité][y]|![Normes conformes à OAuth v 2.0 et à OpenID Connect (OIDC) : l’ADAL ne fournit pas la fonctionnalité][n]|
|**Comptes d’utilisateur et expériences**|||
|Comptes Azure Active Directory (Azure AD)|![Comptes Azure Active Directory (Azure AD) : la MSAL fournit la fonctionnalité][y]|![Comptes Azure Active Directory (Azure AD) : l’ADAL fournit la fonctionnalité][y]|
| Compte Microsoft (MSA) |![Compte Microsoft (MSA) : la MSAL fournit la fonctionnalité][y]|![Compte Microsoft (MSA) : l’ADAL ne fournit pas la fonctionnalité][n]|
| Comptes Azure AD B2C |![Comptes Azure AD B2C : la MSAL fournit la fonctionnalité][y]|![Comptes Azure AD B2C : l’ADAL ne fournit pas la fonctionnalité][n]|
| Meilleure expérience d’authentification unique |![Meilleure expérience d’authentification unique : la MSAL fournit la fonctionnalité][y]|![Meilleure expérience d’authentification unique : l’ADAL ne fournit pas la fonctionnalité][n]|
|**Résilience**|||
| Renouvellement proactif des jetons |![Renouvellement proactif des jetons : la MSAL fournit la fonctionnalité][y]|![Renouvellement proactif des jetons : l’ADAL ne fournit pas la fonctionnalité][n]|
| Limitation |![Limitation : la MSAL fournit la fonctionnalité][y]|![Limitation : l’ADAL ne fournit pas la fonctionnalité][n]|

## <a name="ad-fs-support-in-msalnet"></a>Prise en charge AD FS dans MSAL.NET

Vous pouvez utiliser MSAL.NET, MSAL Java et MSAL Python pour récupérer des jetons à partir de services de fédération Active Directory (AD FS) 2019 ou version ultérieure. Les versions antérieures de AD FS, y compris AD FS 2016, ne sont pas prises en charge par la MSAL.

Si vous devez continuer à utiliser AD FS, vous devez effectuer une mise à niveau vers AD FS 2019 ou version ultérieure avant de mettre à jour vos applications de l’ADAL vers la MSAL.

## <a name="how-to-migrate-to-msal"></a>Comment effectuer une migration vers la MSAL

Avant de commencer la migration, vous devez identifier les applications qui utilisent l’ADAL pour l’authentification. Suivez les étapes de cet article pour obtenir une liste à l’aide du portail Azure :
- [Comment : obtenir la liste complète des applications à l’aide de l’ADAL dans votre locataire](howto-get-list-of-all-active-directory-auth-library-apps.md)

Après avoir identifié vos applications qui utilisent l’ADAL, migrez-les vers la MSAL en fonction de votre type d’application, comme illustré ci-dessous.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="migration-help"></a>Aide sur la migration

Si vous avez des questions sur la migration de votre application de l’ADAL vers la MSAL, voici quelques options :

- Postez votre question sur [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) en utilisant l’étiquette `[azure-ad-adal-deprecation]`.
- Ouvrez un problème dans le référentiel de GitHub de la bibliothèque. Consultez la section [Langages et infrastructures](msal-overview.md#languages-and-frameworks) de l’article de présentation de MSAL pour obtenir des liens vers les référentiel de chaque bibliothèque.

Si vous avez conclu un partenariat avec un éditeur de logiciels indépendant (ISV) dans le développement de votre application, nous vous recommandons de le contacter directement pour comprendre le parcours de migration vers la MSAL.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la MSAL, y compris des informations sur l’utilisation et sur les bibliothèques disponibles pour différents langages de programmation et types d’application, consultez :

- [Acquérir et mettre en cache des jetons à l’aide MSAL](msal-acquire-cache-tokens.md)
- [Options de configuration d’application](msal-client-application-configuration.md)
- [Bibliothèques d’authentification MSAL](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png