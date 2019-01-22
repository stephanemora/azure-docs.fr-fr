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
ms.openlocfilehash: 88904b27e114c27dc7b1051c755d3c831c90554f
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232467"
---
# <a name="about-v20"></a>À propos de v2.0

Le point de terminaison et la plateforme v2.0 sont en préversion et améliorés en permanence. Les scénarios d’application monopage (SPA) JavaScript sont désormais complets en termes de fonctionnalités et nous vous invitons à utiliser MSAL.js pour générer des applications basées sur un navigateur et à nous faire part de vos commentaires pour que nous puissions mettre à jour l’état de préversion à disposition générale (GA).

> [!NOTE]
> MSAL Android, iOS et .NET comprennent toujours des fonctionnalités en cours de développement. Vous pouvez les utiliser pour générer des applications et nous envoyer vos commentaires.

L’expérience des [Inscriptions d’applications (préversion)](quickstart-register-app.md) du portail Azure a été considérablement mise à jour pour inclure désormais toutes vos applications générées avec la bibliothèque ADAL ou MSAL, et pour améliorer la convivialité.

Auparavant, les développeurs d’application qui souhaitaient prendre en charge à la fois les comptes Microsoft personnels et les comptes professionnels d’Azure Active Directory (Azure AD) devait opérer une intégration à deux systèmes distincts. Le point de terminaison et la plateforme v2.0 fournissent une version d’API d’authentification qui simplifie ce processus. Ils permettent une connexion à partir de ces deux types de compte à l’aide d’une intégration unique. Les applications qui utilisent le point de terminaison v2.0 peuvent également consommer les API REST à partir de l’[API Microsoft Graph](https://developer.microsoft.com/graph) à l’aide d’un des deux types de compte.

## <a name="getting-started"></a>Prise en main

Choisissez votre plateforme préférée dans la liste ci-dessous pour générer une application à l’aide des bibliothèques et frameworks open source Microsoft :

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>En savoir plus sur le point de terminaison et la plateforme v2.0

Découvrez ce que vous pouvez faire avec le point de terminaison Azure AD v2.0 :

* Découvrez les [types d’applications que vous pouvez générer avec le point de terminaison Azure AD v2.0](v2-app-types.md).
* Familiarisez-vous avec les [limites, restrictions et contraintes](active-directory-v2-limitations.md) du point de terminaison Azure AD v2.0.

## <a name="additional-resources"></a>Ressources supplémentaires

Informations approfondies sur v2.0 :

* [À propos de la plateforme d’identités Microsoft](about-microsoft-identity-platform.md)
* [Informations de référence sur les protocoles v2.0](active-directory-v2-protocols.md)
* [Référence sur les jetons d’accès](access-tokens.md)
* [Référence sur les jetons d’ID](id-tokens.md)
* [Informations de référence sur les bibliothèques d’authentification v2.0](reference-v2-libraries.md)
* [Autorisations et consentement dans v2.0](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)

> [!NOTE]
> Si vous ne devez connecter que des comptes professionnels et scolaires à partir d’Azure Active Directory, commencez par le [Guide du développeur Azure AD](v1-overview.md). Le point de terminaison v2.0 est destiné aux développeurs qui doivent explicitement connecter des comptes personnels Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
