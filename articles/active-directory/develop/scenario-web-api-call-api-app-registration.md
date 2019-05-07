---
title: Web API qui appelle en aval des API web (inscription de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une API qui appelle en aval des API web (inscription de l’application) web
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075384"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>API que les appels de web API - inscription de l’application Web

Une API web qui appelle des API web en aval a la même inscription comme une API web protégée. Par conséquent, vous devez suivre les instructions fournies dans [API Web protégée par - inscription de l’application](scenario-protected-web-api-app-registration.md).

Cependant, depuis l’application web maintenant appels web API, il devient une application cliente confidentielle. C’est pourquoi il existe des informations d’inscription supplémentaires requises : l’application a besoin de partager des secrets (informations d’identification du client) avec la plateforme d’identité Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorisations des API

Les applications Web appeler des API pour le compte de l’utilisateur pour lequel le jeton du porteur a été reçu. Ils doivent demander des autorisations déléguées. Pour plus d’informations, consultez [ajouter des autorisations pour accéder aux API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-web-api-call-api-app-configuration.md)
