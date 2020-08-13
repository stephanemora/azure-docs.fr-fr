---
title: 'Démarrage rapide : Inscrire des applications auprès de la plateforme des identités Microsoft | Azure'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment ajouter et inscrire une application auprès de la plateforme des identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115354"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Démarrage rapide : Inscrire une application avec la plateforme d’identités Microsoft

Dans ce guide de démarrage rapide, vous allez inscrire une application à l’aide de l’expérience **Inscriptions d’applications** dans le portail Azure. 

Vous pouvez intégrer votre application à la plateforme des identités Microsoft en l’inscrivant auprès d’un locataire Azure Active Directory. Les développeurs et les fournisseurs de logiciels en tant que service (SaaS) peuvent développer des services cloud commerciaux ou des applications métier qui peuvent être intégrés à la plateforme des identités Microsoft. L’intégration fournit un processus de connexion et d’autorisation sécurisé à ces services.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un [locataire Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Inscrire une nouvelle application à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous permet d’accéder à plusieurs locataires, sélectionnez-le dans le coin supérieur droit. Définissez votre session de portail sur le locataire Azure AD de votre choix.
1. Recherchez et sélectionnez **Azure Active Directory**. Sous **Gérer**, sélectionnez **Inscriptions des applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Dans **Inscrire une application**, entrez un nom d’application explicite à afficher aux utilisateurs.
1. Spécifiez qui peut utiliser l’application, comme suit :

    | Types de comptes pris en charge | Description |
    |-------------------------|-------------|
    | **Comptes dans cet annuaire organisationnel uniquement** | Sélectionnez cette option si vous générez une application métier. Cette option n’est pas disponible si vous n’inscrivez pas l’application dans un annuaire.<br><br>Cette option ne mappe qu’à un compte Azure AD à locataire unique.<br><br>Il s’agit de l’option par défaut, sauf si vous inscrivez l’application hors annuaire. Dans les cas où l’application est inscrite hors d’un répertoire, l’option par défaut est multi-locataire et comptes Microsoft personnels. |
    | **Comptes dans un annuaire organisationnel** | Sélectionnez cette option si vous voulez cibler tous les clients professionnels ou du domaine éducatif.<br><br>Cette option ne mappe qu’à un compte Azure AD multi-locataire.<br><br>Si vous avez inscrit l’application comme compte Azure AD monolocataire seulement, vous pouvez le mettre à jour avec un compte Azure AD multilocataire et inversement par le biais de la page **Authentification**. |
    | **Comptes dans un annuaire organisationnel et comptes personnels Microsoft** | Sélectionnez cette option pour cibler l’ensemble le plus large de clients.<br><br>Cette option mappe à des comptes Microsoft personnels et Azure AD multi-locataires.<br><br>Si vous avez inscrit l’application comme comptes Microsoft personnels et Azure AD multilocataires, vous ne pouvez pas modifier ce paramètre dans l’interface utilisateur. Vous devez utiliser l’éditeur de manifeste de l’application pour modifier les types de compte pris en charge. |

1. Sous **URI de redirection (facultatif)** , sélectionnez le type d’application que vous générez : **Web** ou **Client public (mobile bureau)** . Ensuite, entrez l’URI de redirection, ou URL de réponse, de votre application.

    * Pour les applications web, indiquez l’URL de base de votre application. Par exemple, `https://localhost:31544` peut être l’URL pour une application web en cours d’exécution sur votre ordinateur local. Les utilisateurs peuvent utiliser cette URL pour se connecter à une application web cliente.
    * Pour les applications de client public, indiquez l’URI utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique de votre application, par exemple, `myapp://auth`.

    Pour obtenir des exemples d’applications web ou natives, consultez les guides de démarrage rapide dans [Plateforme des identités Microsoft](./index.yml).

1. Lorsque vous avez terminé, sélectionnez **Inscrire**.

    ![Affiche l’écran d’inscription d’une nouvelle application dans le portail Azure](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD affecte à votre application un ID d’application, ou client, unique. Le portail ouvre la page **Vue d’ensemble** de votre application. Pour ajouter des fonctionnalités à votre application, vous pouvez sélectionner d’autres options de configuration, dont la personnalisation, les certificats et les secrets, les autorisations API et plus encore.

![Exemple de page de vue d’ensemble d’application nouvellement inscrite](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour accéder aux API web, consultez [Démarrage rapide : configurer une application cliente pour accéder aux API web](quickstart-configure-app-access-web-apis.md).

* Pour plus d’informations sur les autorisations, consultez [Autorisations et consentement dans le point de terminaison de la plateforme des identités Microsoft](v2-permissions-and-consent.md).

* Pour exposer des API web, consultez [Démarrage rapide : Configurer une application pour exposer des API web](quickstart-configure-app-expose-web-apis.md).

* Pour gérer les comptes pris en charge, consultez [Démarrage rapide : Modifiez les comptes pris en charge par une application](quickstart-modify-supported-accounts.md).

* Pour générer une application et ajouter des fonctionnalités, consultez les guides de démarrage rapide dans [Plateforme des identités Microsoft](./index.yml).

* Pour en savoir plus sur les deux objets Azure AD représentant une application inscrite et la relation entre ces objets, consultez [Objets application et principal du service dans Azure Active Directory (Azure AD)](app-objects-and-service-principals.md).

* Pour en savoir plus sur les directives de personnalisation que vous devez suivre lors du développement d’applications, consultez [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md).