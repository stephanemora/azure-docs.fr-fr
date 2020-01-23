---
title: Comprendre l’authentification API - Azure Digital Twins | Microsoft Docs
description: Apprenez à vous connecter et à vous authentifier à l'aide d'API via Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512996"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Se connecter et s'authentifier à l'aide d'API

Azure Digital Twins utilise Azure Active Directory (Azure AD) pour authentifier les utilisateurs et protéger les applications. Azure AD prend en charge l’authentification pour différentes architectures modernes. Toutes sont basées sur les protocoles standard OAuth 2.0 ou OpenID Connect. En outre, les développeurs peuvent utiliser Azure AD pour créer des applications à client unique et métier (LOB). Les développeurs peuvent également utiliser Azure AD pour développer des [applications multilocataires](how-to-multitenant-applications.md).

Pour une vue d’ensemble d’Azure AD, consultez la [page des notions de base](https://docs.microsoft.com/azure/active-directory/fundamentals/) et accédez à des guides pas à pas, à des concepts et à des guides de démarrage rapide.

> [!TIP]
> Suivez le [didacticiel](tutorial-facilities-setup.md) pour configurer et exécuter un exemple d'application Azure Digital Twins.

Pour intégrer une application ou un service à Azure AD, le développeur doit d’abord enregistrer l’application auprès de Azure AD. Pour obtenir des instructions détaillées et des captures d’écran, consultez [ce démarrage rapide](../active-directory/develop/quickstart-register-app.md).

Voici les [cinq principaux scénarios d’application](../active-directory/develop/v2-app-types.md) pris en charge par Azure AD :

* Application monopage (SPA) : un utilisateur doit se connecter à une application monopage sécurisée par Azure AD.
* Navigateur web vers application web : un utilisateur doit se connecter à une application web sécurisée par Azure AD.
* Application native vers API web : une application native qui s'exécute sur un téléphone, une tablette ou un PC doit authentifier un utilisateur pour obtenir des ressources d'une API web sécurisée par Azure AD.
* Application web vers API web : une application web doit obtenir des ressources d’une API web sécurisée par Azure AD.
* Application démon ou serveur vers API web : une application démon ou une application serveur sans interface utilisateur web doit obtenir des ressources d'une API web sécurisée par Azure AD.

> [!IMPORTANT]
> Azure Digital Twins prend en charge les deux bibliothèques d'authentification suivantes :
> * La version la plus récente de la bibliothèque [MSAL (Microsoft Authentication Library)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * La bibliothèque [Azure ADAL (Active Directory Authentication Library)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Appeler Digital Twins à partir d’une API web de niveau intermédiaire

Lorsque les développeurs créent l’architecture des solutions Digital Twins, ils créent généralement une application de couche intermédiaire ou une API. L'application ou l'API appelle alors l'API Digital Twins en aval. Pour prendre en charge cette architecture de solution web standard, faites en sorte que :

1. Les utilisateurs commencent par s’authentifier auprès de l’application de couche intermédiaire

1. Un jeton On-Behalf-Of OAuth 2.0 soit obtenu pendant l’authentification

1. Le jeton obtenu soit ensuite utilisé pour appeler ou s’authentifier auprès d’API plus en aval à l’aide du flux On-Behalf-Of

Pour obtenir des instructions sur la façon d’orchestrer le flux On-Behalf-Of, consultez le [flux OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Vous pouvez aussi consulter des exemples de code dans [Calling a downstream web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Étapes suivantes

Pour configurer et tester Azure Digital Twins en utilisant le flux d’octroi implicite OAuth 2.0, consultez [Configurer Postman](./how-to-configure-postman.md).

Pour en savoir plus sur la sécurité Azure Digital Twins, consultez la section [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md).
