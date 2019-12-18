---
title: Inscrire une API web appelant des API web – Plateforme d’identités Microsoft | Azure
description: Apprenez à créer une API web qui appelle des API web en aval (inscription d’application)
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
ms.openlocfilehash: cb5f4763e13935b99564bfcb6d8b6e7f463ed59e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919798"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>API web qui appelle des API web - inscription d’application

Une API web qui appelle des API web en aval a la même inscription qu’une API web protégée. Par conséquent, vous devez suivre les instructions fournies à la page [API web protégée - inscription d’application](scenario-protected-web-api-app-registration.md).

Cependant, puisque l’application web appelle maintenant des API web, elle devient une application cliente confidentielle. C’est pourquoi des informations d’inscription supplémentaires sont requises : l’application a besoin de partager des secrets (informations d’identification du client) avec la plateforme d’identités Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorisations des API

Les applications web appellent des API pour le compte de l’utilisateur pour lequel le jeton du porteur a été reçu. Elles doivent demander des autorisations déléguées. Pour plus d’informations, consultez [Ajouter des autorisations pour accéder aux API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-web-api-call-api-app-configuration.md)
