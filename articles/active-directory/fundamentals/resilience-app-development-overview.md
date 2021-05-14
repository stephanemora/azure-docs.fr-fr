---
title: Augmenter la résilience des applications d’authentification et d’autorisation que vous développez
titleSuffix: Microsoft identity platform
description: Présentation de notre guide sur la résilience pour le développement d’applications à l’aide d’Azure Active Directory et de la plateforme d’identités Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 2076b2087c7cd90c83e1fe31c024d2c253109ee3
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162182"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Augmenter la résilience des applications d’authentification et d’autorisation que vous développez

Microsoft Identity utilise une authentification et une autorisation modernes basées sur des jetons. Cela signifie qu'une application cliente acquiert des jetons auprès d'un fournisseur d'identité pour authentifier l'utilisateur et autoriser l'application à appeler des API protégées. Un service valide les jetons.

Un jeton est valide pendant un certain laps de temps avant que l’application ne doive en acquérir un nouveau. Dans de rares cas, un appel pour récupérer un jeton peut échouer en raison d’un problème comme une défaillance du réseau ou de l’infrastructure ou une interruption du service d’authentification. Dans ce document, nous décrivons les mesures qu’un développeur peut prendre pour renforcer la résilience de ses applications en cas d’échec de l’acquisition d’un jeton.

Ces articles fournissent des conseils sur l’amélioration de la résilience dans les applications à l’aide de la plateforme d’identités Microsoft et d’Azure Active Directory. Des conseils sont fournis pour les applications clientes et les applications de service qui fonctionnent pour le compte d'un utilisateur connecté ainsi que pour les applications démon qui fonctionnent pour leur propre compte. Ils contiennent les meilleures pratiques relatives à l’utilisation de jetons et à l’appel de ressources.

- [Renforcer la résilience des applications qui permettent aux utilisateurs de se connecter](resilience-client-app.md)
- [Renforcer la résilience des applications sans utilisateurs](resilience-daemon-app.md)
- [Renforcer la résilience de votre infrastructure de gestion des identités et des accès](resilience-in-infrastructure.md)
- [Renforcer la résilience de vos systèmes CIAM](resilience-b2c.md)
- [Créer des services résilients à l'actualisation des métadonnées](../develop/howto-build-services-resilient-to-metadata-refresh.md)
