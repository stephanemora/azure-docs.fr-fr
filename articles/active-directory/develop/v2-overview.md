---
title: À propos de v2.0 | Azure
description: En savoir plus sur le point de terminaison et la plateforme v2.0.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: adcb1b15ed52e6954846ea09be0a87a118222c10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989582"
---
# <a name="about-v20"></a>À propos de v2.0

Le point de terminaison et la plateforme v2.0 sont en préversion et améliorés en permanence. Les scénarios d’application monopage (SPA) JavaScript sont désormais complets en termes de fonctionnalités et nous vous invitons à utiliser MSAL.js pour générer des applications basées sur un navigateur et à nous faire part de vos commentaires pour que nous puissions mettre à jour l’état de préversion à disposition générale (GA).

> [!NOTE]
> MSAL Android, iOS et .NET comprennent toujours des fonctionnalités en cours de développement. Vous pouvez les utiliser pour générer des applications et nous envoyer vos commentaires.

L’expérience de développement du portail Azure a été considérablement mise à jour pour inclure désormais toutes vos applications générées avec la bibliothèque ADAL ou MSAL, et pour améliorer la convivialité.

Auparavant, les développeurs d’application qui souhaitaient prendre en charge à la fois les comptes Microsoft personnels et les comptes professionnels d’Azure Active Directory (Azure AD) devait opérer une intégration à deux systèmes distincts. Le point de terminaison et la plateforme v2.0 fournissent une version d’API d’authentification qui simplifie ce processus. Ils permettent une connexion à partir de ces deux types de compte à l’aide d’une intégration unique. Les applications qui utilisent le point de terminaison v2.0 peuvent également consommer les API REST à partir de l’[API Microsoft Graph](https://graph.microsoft.io) à l’aide d’un des deux types de compte.

## <a name="getting-started"></a>Prise en main

Choisissez votre plateforme préférée dans la liste ci-dessous pour créer une application à l’aide des bibliothèques et frameworks open source Microsoft. Vous pouvez également utiliser les protocoles OAuth 2.0 et OpenID Connect pour envoyer et recevoir des messages de protocole directement sans l’aide d’une bibliothèque d’authentification.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>En savoir plus sur le point de terminaison et la plateforme v2.0

Découvrez ce que vous pouvez faire avec le point de terminaison Azure AD v2.0 :

* Découvrez les [types d’applications que vous pouvez générer avec le point de terminaison Azure AD v2.0](v2-app-types.md).
* Familiarisez-vous avec les [limites, restrictions et contraintes](active-directory-v2-limitations.md) du point de terminaison Azure AD v2.0.
* Regardez cette vidéo pour avoir une vue d’ensemble du point de terminaison Azure AD v2.0 :

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Ressources supplémentaires

Informations approfondies sur v2.0 :

* [Informations de référence sur les protocoles v2.0](active-directory-v2-protocols.md)
* [Référence sur les jetons d’accès](access-tokens.md)
* [Référence sur `id_tokens` ](id-tokens.md)
* [Informations de référence sur les bibliothèques d’authentification v2.0](reference-v2-libraries.md)
* [Étendues et consentement dans v2.0](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Si vous ne devez connecter que des comptes professionnels et scolaires à partir d’Azure Active Directory, commencez par le [Guide du développeur Azure AD](azure-ad-developers-guide.md). Le point de terminaison v2.0 est destiné aux développeurs qui doivent explicitement connecter des comptes personnels Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
