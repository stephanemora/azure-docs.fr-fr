---
title: Comprendre l’authentification API Azure Digital Twins | Microsoft Docs
description: Utiliser Azure Digital Twins pour se connecter et s’authentifier aux API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016214"
---
# <a name="connect-and-authenticate-to-apis"></a>Se connecter et s’authentifier aux API

Azure Digital Twins utilise Azure Active Directory (Azure AD) pour authentifier les utilisateurs et protéger les applications. Azure AD prend en charge l’authentification pour différentes architectures modernes. Toutes sont basées sur les protocoles standard OAuth 2.0 ou OpenID Connect. En outre, les développeurs peuvent utiliser Azure AD pour créer des applications à client unique et métier (LOB). Les développeurs peuvent également utiliser Azure AD pour développer des applications multilocataires.

Pour une vue d’ensemble d’Azure AD, consultez la [page des notions de base](https://docs.microsoft.com/azure/active-directory/fundamentals/index) et accédez à des guides pas à pas, à des concepts et à des guides de démarrage rapide.

Pour intégrer une application ou un service à Azure AD, le développeur doit d’abord enregistrer l’application auprès de Azure AD. Pour obtenir des instructions détaillées et des captures d’écran, consultez [ce démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

Voici les [cinq principaux scénarios d’application](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) pris en charge par Azure AD :

* Application monopage (SPA) : un utilisateur doit se connecter à une application monopage sécurisée par Azure AD.
* Navigateur web vers application web : un utilisateur doit se connecter à une application web sécurisée par Azure AD.
* Application native vers API web : une application native qui s’exécute sur un téléphone, une tablette ou un PC doit authentifier un utilisateur pour obtenir des ressources d’une API web sécurisée par Azure AD.
* Application web vers API web : une application web doit obtenir des ressources d’une API web sécurisée par Azure AD.
* Application démon ou serveur vers API web : une application démon ou une application serveur sans IU web doit obtenir des ressources d’une API web sécurisée par Azure AD.

La bibliothèque d’authentification Windows Azure propose diverses méthodes d’acquisition de jetons Active Directory. Pour plus d’informations sur la bibliothèque et les exemples de code, consultez [cet article](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Appeler Digital Twins à partir d’une API web de niveau intermédiaire

Lorsque les développeurs créent l’architecture des solutions Digital Twins, ils créent généralement une application de couche intermédiaire ou une API. L'application ou l'API appelle alors l'API Digital Twins en aval. Les utilisateurs s’authentifient d’abord à l’application de niveau intermédiaire, puis un flux de jeton On-Behalf-Of est utilisé lors de l’appel en aval. Pour des instructions sur la façon d’orchestrer le flux On-Behalf-Of, consultez [cette page](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Vous pouvez également afficher des exemples de code sur [cette page](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Test avec le client Postman

Pour rendre les API Digital Twins opérationnelles, vous pouvez utiliser un client tel que Postman comme environnement d’API. Postman vous permet de créer rapidement des requêtes HTTP complexes. Les étapes suivantes montrent comment obtenir un jeton Azure AD, qui est nécessaire pour appeler Digital Twins dans l’interface utilisateur de Postman.


1. Accédez à https://www.getpostman.com/ pour télécharger l’application.
1. Suivez les étapes de [ce démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) pour créer une application Azure AD. Vous pouvez aussi réutiliser une inscription existante. 
1. Sous **Autorisations requises**, entrez « Azure Digital Twins » et sélectionnez **Permissions déléguées**. Sélectionnez ensuite **Accorder des autorisations**.
1. Ouvrez le manifeste de l’application et définissez **oauth2AllowImplicitFlow** sur true.
1. Configurez une URL de réponse sur [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Sélectionnez l’onglet **Autorisation**, **OAuth 2.0**, puis **Get New Access Token** (Obtenir un nouveau jeton d’accès).

    |**Champ**  |**Valeur** |
    |---------|---------|
    | Type d’autorisation | Implicite. |
    | URL de rappel | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | URL d’authentification | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | ID client | Utilisez l’ID de l’application Azure AD créée ou réaffectée à l’étape 2. |
    | Étendue | Laisser vide. |
    | État | Laisser vide. |
    | Authentification du client | Envoyer en tant qu’en-tête d’authentification de base. |

1. Sélectionnez **Jeton de demande**.

    >[!NOTE]
    >Si vous recevez le message d’erreur « OAuth 2 couldn’t be completed » (Impossible d’exécuter OAuth 2), essayez ce qui suit :
    > * Fermez Postman, rouvrez-le et réessayez.
   
1. Faites défiler la page et sélectionnez **Use Token** (Utiliser le jeton).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurité Azure Digital Twins, consultez la section [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md).
