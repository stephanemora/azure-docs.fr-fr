---
title: Application Web par appels web API (inscription de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application web par appels web API (inscription de l’application)
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
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075189"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Application Web par appels web API - inscription de l’application

Une Web app appeler des API web a la même inscription comme une application Web de signature dans les utilisateurs. Vous devez donc suivre les instructions fournies dans [application Web qui se connecte les utilisateurs - inscription de l’application](scenario-web-app-sign-user-app-registration.md)

Cependant depuis l’application Web maintenant appels web API, il devient une application cliente confidentielle. C’est pourquoi un peu d’inscription supplémentaire requis : il a besoin de partager des secrets (informations d’identification du client) avec la plateforme d’identité Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorisations des API

Les applications Web appeler des API pour le compte de l’utilisateur connecté. Ils doivent demander des autorisations déléguées. Pour plus d’informations, consultez [ajouter des autorisations pour accéder aux API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-web-app-call-api-app-configuration.md)
