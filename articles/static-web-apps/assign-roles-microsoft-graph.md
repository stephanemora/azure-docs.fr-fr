---
title: 'Tutoriel : Attribuer des rôles Azure Static Web Apps avec Microsoft Graph'
description: Découvrez comment utiliser une fonction serverless pour attribuer des rôles d’utilisateur personnalisés en fonction de l’appartenance aux groupes Active Directory.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: cshoe
keywords: autorisation static web apps, attribuer des rôles d’utilisateur, rôles personnalisés
ms.openlocfilehash: bf9089f7e645dd32bbfd5f30041f76f71a04a5ec
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858826"
---
# <a name="tutorial-assign-custom-roles-with-a-function-and-microsoft-graph"></a>Tutoriel : Attribuer des rôles personnalisés avec une fonction et Microsoft Graph

Cet article montre comment utiliser une fonction pour interroger [Microsoft Graph](https://developer.microsoft.com/graph) et attribuer des rôles personnalisés à un utilisateur en fonction de son appartenance aux groupes Active Directory.

Ce didacticiel vous apprend à effectuer les opérations suivantes :

- Déployer une application web statique.
- Créer une inscription d’application Azure Active Directory.
- Configurer l’authentification personnalisée avec Azure Active Directory.
- Configurer une [fonction serverless](authentication-authorization.md?tabs=function#role-management) qui interroge l’appartenance aux groupes Active Directory de l’utilisateur et retourne une liste de rôles personnalisés.

> [!NOTE]
> Ce tutoriel vous oblige à [utiliser une fonction pour attribuer des rôles](authentication-authorization.md?tabs=function#role-management). La gestion des rôles basée sur les fonctions est en préversion.

## <a name="prerequisites"></a>Prérequis

- **Compte Azure actif :** si vous n’en avez pas, vous pouvez [créer un compte gratuitement](https://azure.microsoft.com/free/).
- Vous devez disposer d’autorisations suffisantes pour créer une application Azure Active Directory.

## <a name="create-a-github-repository"></a>Créer un référentiel GitHub

1. Accédez à l’emplacement suivant pour créer un nouveau dépôt :
    - [https://github.com/staticwebdev/roles-function/generate](https://github.com/login?return_to=/staticwebdev/roles-function/generate)

1. Nommez votre dépôt **my-custom-roles-app**.

1. Sélectionnez **Create repository from template** (Créer un dépôt à partir du modèle).

## <a name="deploy-the-static-web-app-to-azure"></a>Déployer l’application web statique sur Azure

1. Dans une nouvelle fenêtre de navigateur, accédez au [portail Azure](https://portal.azure.com), puis connectez-vous avec votre compte Azure.

1. Dans le coin supérieur gauche, sélectionnez **Créer une ressource**.

1. Tapez **Static Web Apps** dans la zone de recherche.

1. Sélectionnez **Static Web Apps**.

1. Sélectionnez **Create** (Créer).

1. Configurez votre application Azure Static Web Apps avec les informations suivantes :

    | **Input** | **Valeur** | **Remarques** |
    | ---------- | ---------- | ---------- |
    | _Abonnement_ | Sélectionner votre abonnement Azure | |
    | _Groupe de ressources_ | Créez-en un autre nommé **my-custom-roles-app-group** | |
    | _Nom_ | **my-custom-roles-app**  | |
    | _Plan d’hébergement_ | **Standard** | La personnalisation de l’authentification et l’attribution de rôles à l’aide d’une fonction nécessitent le plan Standard |
    | _Région_ | Sélectionnez la région la plus proche de vous | |
    | _Détails du déploiement_ | Sélectionnez **GitHub** en tant que source | |

1. Sélectionnez le bouton **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Sélectionnez le nom de l’_organisation_ où vous avez créé le dépôt.

1. Sélectionnez **my-custom-roles-app** dans la liste déroulante _Dépôt_.

1. Sélectionnez **principale** dans la liste déroulante _Branche_.

1. Dans la section _Détails de la build_, ajoutez les détails de configuration de cette application.

    | **Input** | **Valeur** | **Remarques** |
    | ---------- | ---------- | ---------- |
    | _Options prédéfinies de build_ | **Personnalisée** | |
    | _Emplacement de l’application_ | **frontend** | Dossier du dépôt contenant l’application |
    | _Emplacement de l’API_ | **api** | Dossier du dépôt contenant l’API |
    | _Emplacement de sortie_ | | Cette application n’a aucune sortie de build |

1. Sélectionnez **Revoir + créer**. Sélectionnez ensuite **Créer** pour créer l’application web statique et lancer le premier déploiement.

1. Sélectionnez **Accéder à la ressource** pour ouvrir votre nouvelle application web statique.

1. Dans la section de vue d’ensemble, recherchez l’**URL** de votre application. Copiez cette valeur dans un éditeur de texte, car vous aurez besoin de cette URL pour configurer l’authentification Active Directory et tester l’application.

## <a name="create-an-azure-active-directory-application"></a>Créer une application Azure Active Directory

1. Dans le portail Azure, recherchez *Azure Active Directory*, puis accédez au service.

1. Dans la barre de menus, sélectionnez **Inscriptions d’applications**.

1. Sélectionnez **+ Nouvelle inscription** pour ouvrir la page *Inscrire une application*

1. Entrez un nom pour l’application. Par exemple, **MyStaticWebApp**.

1. Pour les *Types de comptes pris en charge*, sélectionnez **Comptes dans cet annuaire organisationnel**.

1. Pour les *URI de redirection*, sélectionnez **Web**, puis entrez le [rappel d’authentification](authentication-custom.md#authentication-callbacks) de connexion Azure Active Directory de votre application web statique. Par exemple : `<YOUR_SITE_URL>/.auth/login/aad/callback`.

    Remplacez `<YOUR_SITE_URL>` par l’URL de votre application web statique.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-app-registration.png" alt-text="Créer une inscription d’application":::

1. Sélectionnez **Inscription**.

1. Une fois l’inscription d’application créée, copiez l’**ID d’application (client)** et l’**ID d’annuaire (locataire)** de la section *Essentials* dans un éditeur de texte. Vous aurez besoin de ces valeurs pour configurer l’authentification Active Directory dans votre application web statique.

### <a name="enable-id-tokens"></a>Activer les jetons d’ID

1. Sélectionnez *Authentification* dans la barre de menus.

1. Dans la section *Implicit grant and hybrid flows* (Flux d’octroi implicite et flux hybride), sélectionnez **Jetons d’ID (utilisés pour les flux implicites et hybrides)** .

    :::image type="content" source="media/assign-roles-microsoft-graph/enable-id-tokens.png" alt-text="Activer les jetons d’ID":::
    
    Cette configuration est nécessaire à Static Web Apps pour authentifier vos utilisateurs.

1. Sélectionnez **Enregistrer**.

### <a name="create-a-client-secret"></a>Créer une clé secrète client

1. Sélectionnez *Certificats et secrets* dans la barre de menus.

1. Dans la section *Secrets client*, sélectionnez **+ Nouveau secret client**.

1. Entrez un nom pour la clé secrète client. Par exemple, **MyStaticWebApp**.

1. Conservez la valeur par défaut, _6 mois_, pour le champ *Expire*.

    > [!NOTE]
    > Vous devez changer le secret avant la date d’expiration en générant un nouveau secret et en mettant à jour votre application avec sa valeur.

1. Sélectionnez **Ajouter**.

1. Notez la **valeur** du secret client que vous avez créé. Vous aurez besoin de cette valeur pour configurer l’authentification Active Directory dans votre application web statique.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-client-secret.png" alt-text="Créer une clé secrète client":::

## <a name="configure-active-directory-authentication"></a>Configurer l’authentification Active Directory

1. Dans un navigateur, ouvrez le dépôt GitHub contenant l’application web statique que vous avez déployée. Accédez au fichier config de l’application dans *frontend/staticwebapp.config.json*. Il contient la section suivante :

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
              "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

    > [!NOTE]
    > Pour permettre l’obtention d’un jeton d’accès pour Microsoft Graph, le champ `loginParameters` doit être configuré avec `resource=https://graph.microsoft.com`.

1. Sélectionnez le bouton **Modifier** pour mettre à jour le fichier.

1. Mettez à jour la valeur *openIdIssuer* de `https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>` en remplaçant `<YOUR_AAD_TENANT_ID>` par l’ID d’annuaire (locataire) de votre annuaire Azure Active Directory.

1. Sélectionnez **Valider directement sur la branche primaire**, puis sélectionnez **Valider les modifications**.

1. Une action GitHub Actions exécute des déclencheurs pour mettre à jour l’application web statique.

1. Accédez à votre ressource d’application web statique dans le portail Azure.

1. Dans la barre de menus, sélectionnez **Configuration**.

1. Dans la section *Paramètres d’application*, ajoutez les paramètres suivants :

    | Name | Valeur |
    |------|-------|
    | `AAD_CLIENT_ID` | *Votre ID d’application (client) Active Directory* |
    | `AAD_CLIENT_SECRET` | *Votre valeur de secret client d’application Active Directory* |

1. Sélectionnez **Enregistrer**.

## <a name="verify-custom-roles"></a>Vérifier les rôles personnalisés

L’exemple d’application contient une fonction serverless (*api/GetRoles/index.js*) qui interroge Microsoft Graph pour déterminer si un utilisateur se trouve dans un groupe prédéfini. En fonction des appartenances aux groupes de l’utilisateur, la fonction attribue des rôles personnalisés à l’utilisateur. L’application est configurée pour restreindre certaines routes en fonction de ces rôles personnalisés.

1. Dans votre dépôt GitHub, accédez à la fonction *GetRoles* située dans *api/GetRoles/index.js*. Tout en haut, un objet `roleGroupMappings` mappe les rôles d’utilisateur personnalisés aux groupes Azure Active Directory.

1. Cliquez sur le bouton **Modifier**.

1. Mettez à jour l’objet avec les ID de groupe de votre locataire Azure Active Directory.

    Par exemple, si vous avez des groupes avec les ID `6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` et `b6059db5-9cef-4b27-9434-bb793aa31805`, vous devez mettre à jour l’objet comme suit :

    ```js
    const roleGroupMappings = {
      'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
      'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    La fonction *GetRoles* est appelée chaque fois qu’un utilisateur est correctement authentifié auprès d’Azure Active Directory. La fonction utilise le jeton d’accès de l’utilisateur pour interroger son appartenance aux groupes Active Directory à partir de Microsoft Graph. Si l’utilisateur est membre de groupes définis dans l’objet `roleGroupMappings`, les rôles personnalisés correspondants sont retournés par la fonction.
    
    Dans l’exemple ci-dessus, si un utilisateur est membre du groupe Active Directory ayant l’ID `b6059db5-9cef-4b27-9434-bb793aa31805`, le rôle `reader` lui est octroyé.

1. Sélectionnez **Valider directement sur la branche primaire**, puis sélectionnez **Valider les modifications**.

1. Une action GitHub Actions exécute des déclencheurs pour mettre à jour l’application web statique.

1. Une fois le déploiement effectué, vous pouvez vérifier vos changements en accédant à l’URL de l’application.

1. Connectez-vous à votre application web statique à l’aide d’Azure Active Directory.

1. Quand vous êtes connecté, l’exemple d’application affiche la liste des rôles qui vous sont attribués en fonction de l’appartenance aux groupes Active Directory de votre identité. En fonction de ces rôles, vous êtes autorisé ou non à accéder à certaines routes dans l’application.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.

1. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.

1. Sélectionnez le nom du groupe de ressources que vous avez utilisé dans ce tutoriel.

1. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Authentification et autorisation](./authentication-authorization.md)
