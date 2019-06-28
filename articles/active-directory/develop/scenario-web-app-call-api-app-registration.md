---
title: Application web qui appelle des API web (inscription d’application) - Plateforme d’identité Microsoft
description: Apprenez à générer une application web qui appelle des API web (inscription d’application)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075189"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Application API qui appelle des API web - Inscription d’application

Une application web qui appelle des API web a la même inscription qu’une application web qui connecte des utilisateurs. Vous devez donc suivre les instructions fournies dans [Application Web qui connecte des utilisateurs - Inscription d’application](scenario-web-app-sign-user-app-registration.md)

Cependant, puisque l’application web appelle maintenant des API web, elle devient une application cliente confidentielle. C’est pourquoi quelques informations d’inscription supplémentaires sont requises : l’application a besoin de partager des secrets (informations d’identification du client) avec la plateforme d’identité Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorisations des API

Les applications Web appellent des API pour le compte de l’utilisateur connecté. Elles doivent demander des autorisations déléguées. Pour plus d’informations, consultez [Ajouter des autorisations pour accéder aux API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-web-app-call-api-app-configuration.md)
