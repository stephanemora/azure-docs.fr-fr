---
title: Comprendre l’authentification API Azure Digital Twins | Microsoft Docs
description: Utiliser Azure Digital Twins pour se connecter et s’authentifier aux API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533824"
---
# <a name="connect-and-authenticate-to-apis"></a>Se connecter et s’authentifier aux API

Azure Digital Twins utilise Azure Active Directory (Azure AD) pour authentifier les utilisateurs et protéger les applications. Azure AD prend en charge l’authentification pour différentes architectures modernes. Toutes sont basées sur les protocoles standard OAuth 2.0 ou OpenID Connect. En outre, les développeurs peuvent utiliser Azure AD pour créer des applications à client unique et métier (LOB). Les développeurs peuvent également utiliser Azure AD pour développer des applications multilocataires.

Pour une vue d’ensemble d’Azure AD, consultez la [page des notions de base](https://docs.microsoft.com/azure/active-directory/fundamentals/index) et accédez à des guides pas à pas, à des concepts et à des guides de démarrage rapide.

Pour intégrer une application ou un service à Azure AD, le développeur doit d’abord enregistrer l’application auprès de Azure AD. Pour obtenir des instructions détaillées et des captures d’écran, consultez [ce démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

Voici les [cinq principaux scénarios d’application](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) pris en charge par Azure AD :

* Application monopage (SPA) : un utilisateur doit se connecter à une application monopage sécurisée par Azure AD.
* Navigateur web vers application web : un utilisateur doit se connecter à une application web sécurisée par Azure AD.
* Application native vers API web : une application native qui s'exécute sur un téléphone, une tablette ou un PC doit authentifier un utilisateur pour obtenir des ressources d'une API web sécurisée par Azure AD.
* Application web vers API web : une application web doit obtenir des ressources d’une API web sécurisée par Azure AD.
* Application démon ou serveur vers API web : une application démon ou une application serveur sans interface utilisateur web doit obtenir des ressources d'une API web sécurisée par Azure AD.

La bibliothèque d’authentification Windows Azure propose diverses méthodes d’acquisition de jetons Active Directory. Pour plus d’informations sur la bibliothèque et les exemples de code, consultez [cet article](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Appeler Digital Twins à partir d’une API web de niveau intermédiaire

Lorsque les développeurs créent l’architecture des solutions Digital Twins, ils créent généralement une application de couche intermédiaire ou une API. L'application ou l'API appelle alors l'API Digital Twins en aval. Pour prendre en charge cette architecture de solution web standard, faites en sorte que :

1. Les utilisateurs commencent par s’authentifier auprès de l’application de couche intermédiaire

1. Un jeton On-Behalf-Of OAuth 2.0 soit obtenu pendant l’authentification

1. Le jeton obtenu soit ensuite utilisé pour appeler ou s’authentifier auprès d’API plus en aval à l’aide du flux On-Behalf-Of

Pour obtenir des instructions sur la façon d’orchestrer le flux On-Behalf-Of, consultez [Flux On-Behalf-Of OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Vous pouvez aussi consulter des exemples de code dans [Calling a downstream web API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Étapes suivantes

Pour configurer et tester Azure Digital Twins en utilisant le flux d’octroi implicite OAuth 2.0, consultez [Configurer Postman](./how-to-configure-postman.md).

Pour en savoir plus sur la sécurité Azure Digital Twins, consultez la section [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md).