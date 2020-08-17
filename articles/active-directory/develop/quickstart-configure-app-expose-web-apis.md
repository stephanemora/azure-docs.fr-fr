---
title: 'Démarrage rapide : Configurer une application pour exposer une API web | Azure'
titleSuffix: Microsoft identity platform
description: Cet démarrage rapide montre comment configurer une application pour exposer une nouvelle autorisation/étendue ainsi qu’un nouveau rôle pour rendre l’application disponible pour des applications clientes.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830289"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Démarrage rapide : Configurer une application pour exposer une API web

Vous pouvez développer une API web et la mettre à disposition des applications clientes en exposant l’[autorisation/étendue](developer-glossary.md#scopes) et les [rôles](developer-glossary.md#roles). Une API web correctement configurée peut être mise à disposition tout comme les autres API web Microsoft, notamment l'API Graph et les API Office 365.

Ce démarrage explique comment configurer une application pour exposer une nouvelle étendue afin de la rendre disponible pour des applications clientes.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Exécution du [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Connectez-vous au portail Azure puis sélectionnez l’application

Avant de pouvoir configurer l’application, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation de gauche, sélectionnez le service **Azure Active Directory**, puis **Inscriptions d’applications**.
1. Recherchez et sélectionnez l’application que vous souhaitez configurer. Une fois l’application sélectionnée, vous pourrez voir sa **présentation** ou sa page d’inscription principale.
1. Pour exposer une nouvelle étendue,choisissez la méthode que vous souhaitez utiliser, l’interface utilisateur ou le manifeste de l'application :
    * [Exposer une nouvelle étendue via l’interface utilisateur](#expose-a-new-scope-through-the-ui)
    * [Exposer une nouvelle étendue ou un nouveau rôle via le manifeste de l’application](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Exposer une nouvelle étendue via l’interface utilisateur

[![Montre comment exposer une API à l’aide de l’interface utilisateur](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Pour exposer une nouvelle étendue via l’interface utilisateur vous devez...

1. sélectionner la section **Exposer une API** sur la **page de présentation** de l’application.

1. sélectionner **Ajouter une étendue**.

1. Si vous n’avez pas défini d’**URI d’ID d’application**, vous verrez une invite pour pouvoir en entrer un. Entrez votre URI d’ID d’application ou utilisez celui qui vous a été fourni, puis sélectionnez **Enregistrer et continuer**.

1. Lorsque la page **Ajouter une étendue** s’affiche, entrez les informations de votre étendue :

    | Champ | Description |
    |-------|-------------|
    | **Nom de l'étendue** | Entrez un nom explicite pour votre étendue.<br><br>Par exemple : `Employees.Read.All`. |
    | **Qui peut donner son consentement** | Choisissez si cette étendue peut être consentie par des utilisateurs, ou si le consentement d’un administrateur est requis. Sélectionnez **administrateurs uniquement** pour des autorisations à privilèges élevés. |
    | **Nom d'affichage du consentement administrateur** | Entrez une description explicite pour votre étendue. Elle sera visible par les administrateurs.<br><br>Par exemple : `Read-only access to Employee records` |
    | **Description du consentement de l'administrateur** | Entrez une description explicite pour votre étendue. Elle sera visible par les administrateurs.<br><br>Par exemple : `Allow the application to have read-only access to all Employee data.` |

    Si les utilisateurs peuvent donner leur consentement à votre étendue, ajoutez également des valeurs pour les champs suivants :

    | Champ | Description |
    |-------|-------------|
    | **Nom d'affichage du consentement de l'utilisateur** | Entrez un nom explicite pour votre étendue. Il sera visible par les utilisateurs.<br><br>Par exemple : `Read-only access to your Employee records` |
    | **Description du consentement de l'utilisateur** | Entrez une description explicite pour votre étendue. Elle sera visible par les utilisateurs.<br><br>Par exemple : `Allow the application to have read-only access to your Employee data.` |

1. Définissez l’**État** et sélectionnez **Ajouter une étendue** lorsque vous avez terminé.

1. (Facultatif) Pour supprimer les demandes de consentement des utilisateurs de votre application pour les étendues que vous avez définies, vous pouvez préautoriser l’application cliente à accéder à votre API web. Vous devez préautoriser *uniquement* les applications clientes que vous approuvez, car vos utilisateurs n’auront pas la possibilité de refuser le consentement.
    1. Sous **Applications clientes autorisées**, sélectionnez **Ajouter une application cliente**.
    1. Entrez l’**ID d’application (client)** de l’application cliente que vous souhaitez préautoriser, par exemple celui d’une application web que vous avez inscrite précédemment.
    1. Sous **Étendues autorisées**, sélectionnez les étendues pour lesquelles vous souhaitez supprimer les invites de consentement, puis sélectionnez **Ajouter une application**.

    L’application cliente est désormais une application cliente préautorisée, et les utilisateurs ne seront pas invités à donner leur consentement quand ils s’y connectent.

1. Suivez les étapes pour [vérifier que l’API web est exposée à d’autres applications](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Exposer une nouvelle étendue ou un nouveau rôle via le manifeste de l’application

Le manifeste de l’application sert de mécanisme pour mettre à jour l’entité d’application qui définit les attributs d’une inscription d’application Azure AD.

[![Exposer une nouvelle étendue à l’aide de la collection oauth2Permissions du manifeste](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Pour exposer une nouvelle étendue en modifiant le manifeste de l’application :

1. sélectionner la section **Manifeste** sur la **page de présentation** de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de **Modifier** le manifeste depuis le portail. Si vous le souhaitez, vous pouvez sélectionner **Télécharger** et modifier localement le manifeste, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application.

    L’exemple suivant explique comment exposer une nouvelle étendue appelée `Employees.Read.All` sur la ressource/l’API, en ajoutant l’élément JSON suivant pour la collection `oauth2Permissions`.

    Générez la valeur `id` par programmation ou à l’aide d’un outil de génération de GUID tel que [guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Votre API web est maintenant configurée pour être utilisée par d’autres applications de votre répertoire.
1. Suivez les étapes pour [vérifier que l’API web est exposée à d’autres applications](#verify-the-web-api-is-exposed-to-other-applications).

Pour plus d’informations sur l’entité d’application et son schéma, consultez la documentation de référence sur le type de ressource [Application][ms-graph-application] de Microsoft Graph.

Pour plus d’informations sur le manifeste de l’application, y compris sa référence de schéma, consultez [Compréhension du manifeste de l’application Azure AD](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Vérifier que l’API web est exposée à d’autres applications

1. Revenez à votre locataire Azure AD, sélectionnez **Inscriptions des applications**, puis recherchez et sélectionnez l’application cliente que vous souhaitez configurer.
1. Répétez l’étape décrite dans la section [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md).
1. Quand vous arrivez à l’étape [Sélectionner une API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis), sélectionnez votre ressource (l’inscription d’application API web).
    * Si vous avez créé l’inscription d’application API web à l’aide du portail Azure, votre ressource d’API est indiquée dans l’onglet **Mes API**.
    * Si vous avez autorisé Visual Studio à créer l’inscription d’application API web durant la création du projet, votre ressource d’API est indiquée dans l’onglet **API utilisées par mon organisation**.

Après avoir sélectionné la ressource d’API web, vous devez voir la nouvelle étendue disponible pour les demandes d’autorisations clientes.

## <a name="using-the-exposed-scopes"></a>Utilisation des étendues exposées

Une fois qu’un client est correctement configuré avec des autorisations pour accéder à votre API web, Azure AD émet un jeton d’accès OAuth 20. Lorsque le client appelle l’API web, il présente le jeton d’accès dont la revendication d’étendue (`scp`) est définie sur les autorisations demandées dans son inscription d’application.

Vous pouvez exposer des étendues supplémentaires ultérieurement si nécessaire. Considérez que votre API web peut exposer plusieurs étendues associées à un éventail de fonctions différentes. Votre ressource peut contrôler l’accès à l’API web lors de l’exécution, en évaluant la/les revendication(s) de l’étendue (`scp`) dans le jeton d’accès OAuth 2.0 reçu.

Dans vos applications, la valeur d’étendue complète est une concaténation de l’**URI de l’ID d’application** de l’API web (la ressource) et du **nom de l’étendue**.

Par exemple, si l’URI de l’ID d’application de votre API web est `https://contoso.com/api`, et le nom de votre étendue est `Employees.Read.All`, l’étendue complète est :

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez exposé votre API web en configurant ses étendues, configurez l’inscription de votre application cliente avec l’autorisation d’accès à ces étendues.

> [!div class="nextstepaction"]
> [Configurer une inscription d’application pour l’accès à une API web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
