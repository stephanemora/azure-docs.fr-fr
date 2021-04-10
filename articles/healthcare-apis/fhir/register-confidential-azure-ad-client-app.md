---
title: Inscrire une application cliente confidentielle dans Azure AD - API Azure pour FHIR
description: Inscrire une application cliente confidentielle dans Azure Active Directory qui s’authentifie au nom d’un utilisateur et demande l’accès aux applications de ressources.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: matjazl
ms.openlocfilehash: fbc683c98eb0124e8d879eada276593ca9bf7042
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934706"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Inscrire une application cliente confidentielle dans Azure Active Directory

Dans ce didacticiel, vous allez apprendre à inscrire une application cliente confidentielle dans Azure Active Directory (Azure AD).  

L’inscription d’une application cliente est une représentation Azure ADe d’une application qui peut être utilisée pour s’authentifier au nom d’un utilisateur et demander l’accès aux [applications de ressources](register-resource-azure-ad-client-app.md). Une application cliente confidentielle est une application qui peut être approuvée pour garder un secret et présenter ce secret lors de la demande de jetons d’accès. Ces exemples d’applications confidentielles sont des applications côté serveur. 

Pour inscrire une nouvelle application cliente confidentielle, reportez-vous aux étapes ci-dessous. 

## <a name="register-a-new-application"></a>Inscrire une nouvelle application

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

1. Sélectionnez **Inscriptions d’applications**. 

    ![Portail Azure. Nouvelle inscription d’application.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Sélectionnez **Nouvelle inscription**.

1. Donnez à l’application un nom d’affichage accessible à l’utilisateur.

1. Pour les **types de comptes pris en charge**, sélectionnez qui peut utiliser l’application ou accéder à l’API.

1. Facultatif Fournissez un **URI de redirection**. Ces détails peuvent être modifiés ultérieurement, mais si vous connaissez l’URL de réponse de votre application, saisissez-la maintenant.

    ![Nouvelle inscription d’application cliente confidentielle.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

1. Sélectionnez **Inscription**.

## <a name="api-permissions"></a>Autorisations des API

Maintenant que vous avez inscrit votre application, vous devez sélectionner les autorisations d’API que cette application doit demander pour le compte des utilisateurs.

1. Sélectionnez **Autorisations de l’API**.

    ![Client confidentiel. Autorisations des API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Sélectionnez **Ajouter une autorisation**.

    Si vous utilisez l’API Azure pour FHIR, vous devez ajouter une autorisation aux API Azure Healthcare en recherchant **API Azure Healthcare** sous **API utilisées par mon organisation**. Le résultat de la recherche pour l’API de santé Azure ne retourne que si vous avez déjà [déployé l’API Azure pour FHIR](fhir-paas-powershell-quickstart.md).

    Si vous faites référence à une autre application de ressource, sélectionnez l’inscription de votre [application de ressource API FHIR](register-resource-azure-ad-client-app.md) que vous avez créée précédemment sous **mes API**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Client confidentiel. API Mon organisation" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Sélectionnez les étendues (autorisations) que l’application cliente confidentielle demandera au nom d’un utilisateur. Sélectionnez **user_impersonation**, puis **Ajouter des autorisations**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Client confidentiel. Autorisations déléguées":::


## <a name="application-secret"></a>Secret d’application

1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**. 

    ![Client confidentiel. Secret d’application](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

1. Entrez une **Description** pour votre clé secrète client. Sélectionnez la date d’expiration (en 1 an, en 2 ans ou jamais), puis cliquez sur **Ajouter**.

   ![Ajouter un secret client](media/how-to-aad/add-a-client-secret.png)

1. Une fois la chaîne secrète client créée, copiez sa **valeur** et son **ID**, puis stockez-les dans un emplacement sécurisé de votre choix.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Chaîne secrète du client."::: 

> [!NOTE]
>La chaîne secrète du client n’est visible qu’une seule fois dans la Portail Azure. Quand vous quittez la page de certificats & secrets, puis que vous y revenez, la chaîne de valeur devient masquée. Il est important de faire une copie de votre chaîne secrète client immédiatement après sa génération. Si vous n’avez pas de copie de sauvegarde de votre clé secrète client, vous devez répéter les étapes ci-dessus pour la régénérer.
 
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous vous êtes guidé à travers les étapes de l’inscription d’une application cliente confidentielle dans le Azure AD. Vous étiez également guidé à travers les étapes d’ajout d’autorisations d’API à l’API de santé Azure. Enfin, vous avez vu comment créer un secret d’application. En outre, vous pouvez apprendre à accéder à votre serveur FHIR à l’aide de la publication.
 
>[!div class="nextstepaction"]
>[Accéder à l’API Azure pour FHIR avec Postman](access-fhir-postman-tutorial.md)
