---
title: Configurer une application pour accéder à des API web (préversion) | Azure
description: Apprenez à configurer une application inscrite auprès de la plateforme d’identité Microsoft pour inclure des URI de redirection, des informations d’identification ou des autorisations d’accès à des API web.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ef499e49fc4d1a0dc79dfc4efb818f7330b57b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995196"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>Démarrage rapide : Configurer une application cliente pour accéder aux API web (préversion)

Une application cliente web/confidentielle doit établir des informations d’identification sécurisées afin de pouvoir participer à un flux d’octroi d’autorisations qui requiert une authentification. La méthode d’authentification par défaut prise en charge par le portail Azure est l’ID Client + la clé secrète.

En outre, avant qu’un client puisse accéder à une API web exposée par une application de ressources (comme l’API Microsoft Graph), l’infrastructure de consentement permet au client d’obtenir l’autorisation nécessaire en fonction des autorisations demandées. Par défaut, toutes les applications peuvent choisir des autorisations à partir de l’API Microsoft Graph. [L’autorisation « Connexion et lecture du profil utilisateur » de API Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) est sélectionnée par défaut. Vous pouvez choisir à partir de [deux types d’autorisations](developer-glossary.md#permissions) pour chaque API web souhaitée :

* **Permissions d’application** : votre application cliente doit accéder à l’API web directement en tant que telle (aucun contexte utilisateur). Ce type d’autorisation requiert le consentement de l’administrateur et n’est également pas disponible pour les applications clientes publiques (bureau et mobiles).
* **Permissions déléguées** : votre application cliente doit accéder à l’API web en tant qu’utilisateur connecté, mais avec un accès limité par l’autorisation sélectionnée. Ce type d'autorisation peut être accordé par un utilisateur, à moins que l'autorisation nécessite le consentement de l'administrateur.

  > [!NOTE]
  > L’ajout d’une autorisation déléguée à une application n’accorde pas automatiquement un consentement aux utilisateurs du client. Les utilisateurs doivent donner manuellement leur consentement pour les autorisations déléguées ajoutées lors de l’exécution, à moins que l’administrateur ne donne son consentement au nom de tous les utilisateurs.

Dans ce guide de démarrage rapide, nous allons vous montrer comment configurer votre application pour :

* [Ajouter des URI de redirection à votre application](#add-redirect-uris-to-your-application)
* [Ajouter des informations d’identification à votre application web](#add-credentials-to-your-web-application)
* [Ajouter des autorisations pour accéder aux API web](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Prérequis

Pour commencer, assurez-vous de remplir ces conditions préalables :

* Découvrez les [autorisations et consentement](v2-permissions-and-consent.md) pris en charge qu’il est important de comprendre lors de la création d’applications devant être utilisées par d’autres utilisateurs ou applications.
* Disposez d’un locataire auprès duquel des applications sont inscrites.
  * Si vous n’avez pas d’applications inscrites, [découvrez comment inscrire des applications à l’aide de la plateforme d’identité Microsoft](quickstart-register-app.md).
* Intégrez l’expérience de préversion pour les inscriptions d’applications dans le portail Azure. Les étapes figurant dans ce démarrage rapide correspondent à la nouvelle interface utilisateur et ne fonctionnent que si vous participez à l’expérience de préversion.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Connectez-vous au portail Azure puis sélectionnez l’application

Avant de pouvoir configurer l’application, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, sélectionnez ensuite **Inscriptions d’applications (préversion)**.
1. Recherchez et sélectionnez l’application que vous souhaitez configurer. Une fois l’application sélectionnée, vous pourrez voir sa **présentation** ou sa page d’inscription principale.
1. Suivez les étapes pour configurer votre application pour accéder aux API web : 
    * [Ajouter des URI de redirection à votre application](#add-redirect-uris-to-your-application)
    * [Ajouter des informations d’identification à votre application web](#add-credentials-to-your-web-application)
    * [Ajouter des autorisations pour accéder aux API web](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Ajouter des URI de redirection à votre application

[![Ajouter des URI de redirection personnalisés pour des applications web et client public](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Pour ajouter un URI de redirection à votre application :

1. Sélectionnez la section **Authentification** sur la page de **présentation** de l’application.

1. Pour ajouter un URI de redirection personnalisé pour des applications web et client public, procédez comme suit :

   1. Recherchez la section **URI de redirection**.
   1. Sélectionnez le type d’application que vous créez : **Web** ou **Client public (mobile et bureau)**.
   1. Entrez l’URI de redirection de votre application.
      * Pour les applications web, indiquez l’URL de base de votre application. Par exemple, `http://localhost:31544` peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent utiliser cette URL pour se connecter à une application web cliente.
      * Pour les applications publiques, indiquez l’URI utilisé par Azure AD pour retourner les réponses de jeton. Saisissez une valeur spécifique à votre application, par exemple https://MyFirstApp.

1. Pour choisir parmi les URI de redirection suggérés pour les clients publics (mobile, bureau), procédez comme suit :

    1. Recherchez la section des **URI de redirection suggérés pour les clients publics (mobile, bureau)**.
    1. Sélectionnez le ou les URI de redirection approprié(s) pour votre application en cochant les cases correspondantes.

## <a name="add-credentials-to-your-web-application"></a>Ajouter des informations d’identification à votre application web

[![Ajouter des certificats et des clés secrètes client](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Pour ajouter des informations d’identification à votre application web :

1. Sélectionnez la section **Certificats et secrets** sur la page de **présentation** de l’application.

1. Pour ajouter un certificat, procédez comme suit :

    1. Sélectionnez **Charger un certificat**.
    1. Sélectionnez le fichier que vous voulez charger. Il doit s’agir d’un fichier de type .cer, .pem ou .crt.
    1. Sélectionnez **Ajouter**.

1. Pour ajouter une clé secrète client, procédez comme suit :

    1. Sélectionnez **Nouveau secret client**.
    1. Ajoutez une description pour votre clé secrète client.
    1. Sélectionnez une durée.
    1. Sélectionnez **Ajouter**.

> [!NOTE]
> Après avoir enregistré les modifications de configuration, la colonne située le plus à droite contient la valeur de la clé secrète client. **Veillez à copier la valeur** pour l’utiliser dans le code de votre application cliente, car elle ne sera plus accessible dès que vous aurez quitté cette page.

## <a name="add-permissions-to-access-web-apis"></a>Ajouter des autorisations pour accéder aux API web

[![Ajouter des autorisations d’API](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Pour ajouter des autorisations d’accéder aux API de ressources à partir de votre client :

1. Sélectionner la section **Autorisations API** sur la **page de présentation** de l’application.
1. Cliquez sur le bouton **Ajouter une autorisation**.
1. Par défaut, la vue vous permet de faire votre choix parmi des **API Microsoft**. Sélectionnez la section d’API qui vous intéresse :
    * **API Microsoft** : vous permet de sélectionner les autorisations pour des API Microsoft telles que Microsoft Graph.
    * **API utilisées par mon organisation** : vous permet de sélectionner les autorisations pour les API qui ont été exposées par votre organisation, ou des API que votre organisation a intégrées.
    * **Mes API** : vous permet de sélectionner les autorisations pour les API que vous avez exposées.
1. Une fois que vous avez sélectionné les API, vous accédez à la page **Demander des autorisations d’API**. Si l’API expose à la fois des autorisations déléguées et des autorisations d’applications, sélectionnez le type d’autorisation nécessaire pour votre application.
1. Lorsque vous avez terminé, sélectionnez **Ajouter des autorisations**. Vous revenez alors à la page **Autorisations d’API**, dans laquelle les autorisations ont été enregistrées et ajoutées à la table.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les autres démarrages rapides relatifs à la gestion des applications pour les applications :

* [Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md)
* [Configurer une application pour exposer les API web](quickstart-configure-app-expose-web-apis.md)
* [Modifier les comptes pris en charge par une application](quickstart-modify-supported-accounts.md)
* [Supprimer une application inscrite à l’aide de la plateforme d’identité Microsoft](quickstart-remove-app.md)

Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).

Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications avec Azure Active Directory, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).
