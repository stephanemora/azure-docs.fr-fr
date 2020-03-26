---
title: Configurer une application pour exposer des API web - Plateforme d’identités Microsoft | Azure
description: Découvrez comment configurer une application pour exposer une nouvelle autorisation/étendue ainsi qu’un rôle pour rendre l’application disponible sur les applications clientes.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/14/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: e005ba9c5458849863bd4668ffde1e0f6fb4bf91
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76704219"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>Démarrage rapide : Configurer une application pour exposer des API web

Vous pouvez développer une API web et la mettre à disposition des applications clientes en exposant l’[autorisation/étendue](developer-glossary.md#scopes) et les [rôles](developer-glossary.md#roles). Une API web correctement configurée peut être mise à disposition tout comme les autres API web Microsoft, notamment l'API Graph et les API Office 365.

Dans ce démarrage rapide, vous découvrirez comment configurer une application pour exposer une nouvelle étendue pour la rendre disponible sur les applications clientes.

## <a name="prerequisites"></a>Prérequis

Pour commencer, assurez-vous de remplir ces conditions préalables :

* Découvrez les [autorisations et consentement](v2-permissions-and-consent.md) pris en charge qu’il est important de comprendre lors de la création d’applications devant être utilisées par d’autres utilisateurs ou applications.
* Disposez d’un locataire auprès duquel des applications sont inscrites.
  * Si vous n’avez pas d’applications inscrites, [découvrez comment inscrire des applications à l’aide de la plateforme d’identité Microsoft](quickstart-register-app.md).

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

1. Suivez les étapes pour [vérifier que l’API web est exposée à d’autres applications](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Exposer une nouvelle étendue ou un nouveau rôle via le manifeste de l’application

[![Exposer une nouvelle étendue à l’aide de la collection oauth2Permissions du manifeste](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Pour exposer une nouvelle étendue via le manifeste de l'application vous devez...

1. sélectionner la section **Manifeste** sur la **page de présentation** de l’application. Un éditeur de manifeste web s’ouvre, vous permettant de **Modifier** le manifeste depuis le portail. Si vous le souhaitez, vous pouvez sélectionner **Télécharger** et modifier localement le manifeste, puis utiliser **Charger** afin de l’appliquer de nouveau à votre application.
    
    L’exemple suivant explique comment exposer une nouvelle étendue appelée `Employees.Read.All` sur la ressource/l’API, en ajoutant l’élément JSON suivant pour la collection `oauth2Permissions`.

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

   > [!NOTE]
   > La valeur `id` doit être générée par programme ou en utilisant un outil de génération de GUID comme [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). La valeur `id` représente un identificateur unique pour l’étendue comme exposée par l’API web. Une fois qu’un client est correctement configuré et possède les autorisations pour accéder à votre API web, Azure AD émet un jeton d’accès OAuth 20. Lorsque le client appelle l’API web, il présente le jeton d’accès dont la revendication de l’étendue (scp) a été configurée sur les autorisations demandées dans son inscription d’application.
   >
   > Vous pouvez exposer des étendues supplémentaires ultérieurement si nécessaire. Considérez que votre API web peut exposer plusieurs étendues associées à un éventail de fonctions différentes. Votre ressource peut contrôler l’accès à l’API web lors de l’exécution, en évaluant la/les revendication(s) de l’étendue (`scp`) dans le jeton d’accès OAuth 2.0 reçu.

1. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Votre API web est maintenant configurée pour être utilisée par d’autres applications de votre répertoire.
1. Suivez les étapes pour [vérifier que l’API web est exposée à d’autres applications](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Vérifier que l’API web est exposée à d’autres applications

1. Revenez à votre locataire Azure AD, sélectionnez **Inscriptions des applications**, puis recherchez et sélectionnez l’application cliente que vous souhaitez configurer.
1. Répétez l’étape décrite dans la section [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md).
1. Lorsque vous arrivez à l’étape intitulée [Sélectionner une API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis
), sélectionnez votre ressource. Vous devriez voir la nouvelle étendue, disponible pour les demandes d’autorisations clientes.

## <a name="more-on-the-application-manifest"></a>Informations complémentaires concernant le manifeste d’application

Le manifeste d’application sert de mécanisme de mise à jour de l’entité Application, qui définit tous les attributs de configuration d’identité d’une application Azure AD. Pour plus d’informations sur l’entité Application et son schéma, consultez la [documentation relative à l’entité Application de l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Cet article contient des informations de référence complètes sur les membres de l’entité Application permettant de spécifier des autorisations pour votre API, y compris :  

* Le membre appRoles, qui est une collection d’entités [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), utilisée pour définir les [autorisations d’application](developer-glossary.md#permissions) pour une API web.
* Le membre oauth2Permissions, qui est une collection d’entités [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), utilisée pour définir les [autorisations déléguées](developer-glossary.md#permissions) pour une API web.

Pour des informations plus générales sur les concepts de manifeste d’application, consultez la page [Connaître le manifeste d’application Azure Active Directory](reference-app-manifest.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres démarrages rapides relatifs à la gestion des applications pour les applications :

* [Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md)
* [Configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md)
* [Modifier les comptes pris en charge par une application](quickstart-modify-supported-accounts.md)
* [Supprimer une application inscrite à l’aide de la plateforme d’identité Microsoft](quickstart-remove-app.md)

Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).

Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications avec Azure Active Directory, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).
