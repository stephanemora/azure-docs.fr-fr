---
title: Prefer MSAL | Azure
description: Incluez le fichier indiquant qu’il est préférable d’utiliser MSAL.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 11/17/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7fd409b69ac89e38d6a27e3871117617f208777a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96511189"
---
Cet article explique comment programmer directement par rapport au protocole dans votre application pour demander des jetons à Azure AD.  Dans la mesure du possible, nous vous recommandons d’utiliser les bibliothèques d’authentification Microsoft (MSAL) prises en charge au lieu d’[acquérir des jetons et d’appeler des API web sécurisées](..\authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Jetez également un coup d’œil aux [exemples d’applications qui utilisent MSAL](..\sample-v2-code.md).
