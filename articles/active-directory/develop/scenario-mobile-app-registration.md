---
title: Application mobile par appels web API - configuration d’application code | Plateforme d’identité Microsoft
description: Découvrez comment créer une application mobile par appels web API (configuration de code de l’application)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eac9d1dfce79ac4ad9d49a6cfe6b7dee7f6681a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075159"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Application mobile par appels web API - inscription de l’application

Cet article contient les instructions de l’inscription d’application pour la création d’une application mobile.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Les types de compte pris en charge dans les applications mobiles dépendent de l’expérience que vous souhaitez activer et les utilisateurs de que votre application cible.

## <a name="platform-configuration-and-redirect-uris"></a>Configuration de la plateforme et l’URI de redirection  

Lorsque vous générez une application mobile, l’étape d’inscription les plus critiques est l’URI de redirection. Cela peut être défini via la [configuration de plateforme dans le panneau d’authentification](https://aka.ms/MobileAppReg).

Cette expérience permettra à votre application obtenir l’authentification unique (SSO) via le Microsoft Authenticator (et le portail d’entreprise Intune sur Android), ainsi que la prise en charge des stratégies de gestion des appareils.

Si vous préférez configurer manuellement l’URI de redirection, vous pouvez le faire via le manifeste d’Application. Le format recommandé est le suivant :

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Le hachage de signature Android peut être généré à l’aide des clés via la commande KeyTool debug ou release.

## <a name="api-permissions"></a>Autorisations des API

Les applications mobiles appeler des API pour le compte de l’utilisateur connecté. Votre application doit demander des autorisations déléguées, également appelées étendues. En fonction de l’expérience souhaitée, cela est possible statiquement via le portail Azure ou dynamiquement au moment de l’exécution. Statiquement l’inscription d’autorisations permet aux administrateurs d’approuver facilement votre application et est recommandée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [L’acquisition d’un jeton](scenario-mobile-acquire-token.md)
