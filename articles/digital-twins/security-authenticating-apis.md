---
title: Présentation de l’authentification API Azure Digital Twins | Microsoft Docs
description: Utilisation d’Azure Digital Twins pour se connecter et s’authentifier aux API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638016"
---
# <a name="connect-and-authenticate-to-apis"></a>Se connecter et s’authentifier aux API

Azure Digital Twins utilise Azure Active Directory (Azure AD) pour authentifier les utilisateurs et protéger les applications. Azure AD prend en charge l’authentification pour de multiples architectures d’applications modernes, toutes basées sur des protocoles industriels standard OAuth 2.0 ou OpenID Connect. En outre, Azure AD aide les développeurs qui créent des applications à locataire unique et métiers, ainsi que les développeurs qui souhaitent développer des applications multi-locataires.

Pour une vue d’ensemble d’Azure AD, consultez la [page des notions de base](https://docs.microsoft.com/azure/active-directory/fundamentals/index) et accédez à des guides pas à pas, à des concepts et à des guides de démarrage rapide.

Pour intégrer une application ou un service à Azure AD, le développeur doit d’abord enregistrer l’application auprès de Azure AD. Pour obtenir des instructions détaillées et des captures d’écran, rendez-vous [ici](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

Voici les [cinq principaux scénarios d’application](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) pris en charge par Azure AD :

* Application monopage (SPA) : un utilisateur doit se connecter à une application monopage sécurisée par Azure AD.
* Navigateur web vers application web : un utilisateur doit se connecter à une application web sécurisée par Azure AD.
* Application native vers API web : une application native qui s’exécute sur un téléphone, une tablette ou un PC doit authentifier un utilisateur pour obtenir des ressources d’une API web sécurisée par Azure AD.
* Application web vers API web : une application web doit obtenir des ressources d’une API web sécurisée par Azure AD.
* Application démon ou serveur vers API web : une application démon ou une application serveur sans interface utilisateur web doit obtenir des ressources d’une API web sécurisée par Azure AD.

La bibliothèque d’authentification Windows Azure propose diverses méthodes d’acquisition de jetons Active Directory. Pour une exploration plus approfondie de la bibliothèque et des exemples de code, rendez-vous [ici](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>Appel de Digital Twins à partir d’une API web de niveau intermédiaire

Lors de la conception de l’architecture de solutions Digital Twins, les développeurs choisissent généralement de créer une application ou une API de niveau intermédiaire appelant l’API Digital Twins en aval. Les utilisateurs devraient d’abord s’authentifier à l’application de niveau intermédiaire, puis un flux de jeton On-Behalf-Of serait utilisé lors de l’appel en aval. Pour des instructions détaillées sur la façon d’orchestrer le flux On-Behalf-Of, consultez [cette page](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Vous pouvez également consulter des exemples de code [ici](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).


## <a name="test-with-the-postman-client"></a>Test avec le client Postman

Afin de rendre les API Digital Twins opérationnelles, vous pouvez utiliser un client tel que Postman comme environnement d’API. Postman vous permet de créer rapidement des requêtes HTTP complexes. Les instructions ci-dessous expliquent comment obtenir le jeton Azure AD nécessaire pour appeler Digital Twins dans l’interface utilisateur de Postman.


1. Accédez à https://www.getpostman.com/ pour télécharger l’application
1. Suivez les étapes décrites [ici](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) pour créer une application Azure Active Directory (ou vous pouvez choisir de réutiliser une inscription existante). 
1. Sous Autorisations requises, ajoutez « Azure Digital Twins », puis sélectionnez Autorisations déléguées. N’oubliez pas de cliquer sur l’option Accorder des autorisations pour finaliser l’opération.
1. Ouvrez le manifeste de l’application et définissez oauth2AllowImplicitFlow sur true
1. Configurez l’URL de réponse sur [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback).
1. Sélectionnez l’onglet **Autorisation**, cliquez sur **OAuth 2.0**, puis sélectionnez **Get New Access Token** (Obtenir un nouveau jeton d’accès).

    |**Champ**  |**Valeur** |
    |---------|---------|
    | Type d’autorisation | Implicite. |
    | URL de rappel | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | URL d’authentification | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | ID de client | Utilisez l’ID de l’application Azure AD créée ou réaffectée à l’étape 1 |
    | Étendue | laisser vide |
    | État | laisser vide |
    | Authentification du client | Send as Basic Auth header (Envoyer en tant qu’en-tête d’authentification de base) |

1. Cliquez sur **Request Token** (Demander un jeton).

    >[!NOTE]
    >Si le message d’erreur « OAuth 2 couldn’t be completed » (Impossible d’exécuter OAuth 2), essayez ce qui suit :
    > * Fermez Postman, rouvrez-le et réessayez.
   
1. Faites défiler la page et cliquez sur **Use Token** (Utiliser le jeton).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurité Azure Digital Twins, consultez la section [Créer et gérer des attributions de rôle](./security-create-manage-role-assignments.md).
