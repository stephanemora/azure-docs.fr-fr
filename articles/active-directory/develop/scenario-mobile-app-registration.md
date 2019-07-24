---
title: Application mobile appelant des API web - configuration du code de l’application | Plateforme d’identités Microsoft
description: Découvrez comment créer une application mobile qui appelle des API web (configuration du code de l’application)
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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962408"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Application mobile qui appelle des API web - inscription d’application

Cet article contient les instructions d’inscription d’application pour la création d’une application mobile.

## <a name="supported-account-types"></a>Types de comptes pris en charge

Les types de comptes pris en charge dans les applications mobiles dépendent de l’expérience que vous souhaitez activer et des utilisateurs ciblés par votre application.

## <a name="platform-configuration-and-redirect-uris"></a>Configuration de la plateforme et URI de redirection  

Lorsque vous générez une application mobile, l’étape d’inscription la plus critique concerne l’URI de redirection. Il peut être défini par le biais de la [configuration de plateforme dans le panneau Authentification](https://aka.ms/MobileAppReg).

Cette expérience permet à votre application d’obtenir une authentification unique (SSO) au moyen de Microsoft Authenticator (et du portail d’entreprise Intune sur Android), ainsi que la prise en charge des stratégies de gestion des appareils.

Si vous préférez configurer manuellement l’URI de redirection, vous pouvez le faire au moyen du manifeste de l’application. Le format recommandé est le suivant :

- ***iOS*** : `msauth.<BUNDLE_ID>://auth`
- ***Android*** : `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Le hachage de signature Android peut être généré à l’aide des clés de mise en production ou de débogage par le biais de la commande KeyTool.

## <a name="api-permissions"></a>Autorisations des API

Les applications mobiles appellent des API pour le compte de l’utilisateur connecté. Votre application doit demander des permissions déléguées, également appelées « étendues ». En fonction de l’expérience souhaitée, il est possible de le faire de manière statique sur le Portail Azure, ou de manière dynamique au moment de l’exécution. Grâce à l’inscription statique des permissions, les administrateurs peuvent approuver facilement votre application. Cette méthode est recommandée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Acquisition d’un jeton](scenario-mobile-acquire-token.md)
