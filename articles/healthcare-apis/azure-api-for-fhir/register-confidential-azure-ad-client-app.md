---
title: Inscrire une application cliente confidentielle dans Azure AD - API Azure pour FHIR
description: Inscrire une application cliente confidentielle dans Azure Active Directory qui s’authentifie au nom d’un utilisateur et demande l’accès aux applications de ressources.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 2851f230a19bfbafb514e9fb22fd3f37de1f610b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272544"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>inscrire une application cliente confidentielle dans Azure Active Directory pour l’API Azure pour FHIR

Dans ce didacticiel, vous allez apprendre à inscrire une application cliente confidentielle dans Azure Active Directory (Azure AD).  

l’inscription d’une application cliente est une représentation Azure ADe d’une application qui peut être utilisée pour s’authentifier au nom d’un utilisateur et demander l’accès aux [applications de ressources](register-resource-azure-ad-client-app.md). Une application cliente confidentielle est une application qui peut être approuvée pour garder un secret et présenter ce secret lors de la demande de jetons d’accès. Ces exemples d’applications confidentielles sont des applications côté serveur. 

Pour inscrire une nouvelle application cliente confidentielle, reportez-vous aux étapes ci-dessous. 

## <a name="register-a-new-application"></a>Inscrire une nouvelle application

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

1. Sélectionnez **Inscriptions d’applications**. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-new-app-registration.png" alt-text="Portail Azure. Nouvelle inscription d’application.":::

1. Sélectionnez **Nouvelle inscription**.

1. Donnez à l’application un nom d’affichage accessible à l’utilisateur.

1. Pour les **types de comptes pris en charge**, sélectionnez qui peut utiliser l’application ou accéder à l’API.

1. Facultatif Fournissez un **URI de redirection**. Ces détails peuvent être modifiés ultérieurement, mais si vous connaissez l’URL de réponse de votre application, saisissez-la maintenant.

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client.png" alt-text="Nouvelle inscription d’application cliente confidentielle.":::

1. Sélectionnez **Inscription**.

## <a name="api-permissions"></a>Autorisations des API

Les autorisations pour l’API Azure pour FHIR sont gérées par le biais de RBAC. Pour plus d’informations, consultez [configurer Azure RBAC pour FHIR](configure-azure-rbac.md).

>[!NOTE]
>Utilisez grant_type de client_credentials lors de la tentative d’otain d’un jeton d’accès pour l’API Azure pour FHIR à l’aide d’outils tels que poster. Pour plus d’informations, consultez [test de l’API FHIR sur Azure API pour FHIR](tutorial-web-app-test-postman.md).


## <a name="application-secret"></a>Secret de l’application

1. Sélectionnez **Certificats et secrets**, puis sélectionnez **Nouveau secret client**. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-secret.png" alt-text="Client confidentiel. Secret de l’application.":::

1. Entrez une **Description** pour votre clé secrète client. Sélectionnez le menu déroulant **expirations** pour choisir un délai d’expiration, puis cliquez sur **Ajouter**.

   :::image type="content" source="media/add-azure-active-directory/add-a-client-secret.png" alt-text="Ajoutez une clé secrète client.":::

1. Une fois la chaîne secrète client créée, copiez sa **valeur** et son **ID**, puis stockez-les dans un emplacement sécurisé de votre choix.

   :::image type="content" source="media/add-azure-active-directory/client-secret-string-password.png" alt-text="Chaîne secrète du client."::: 

> [!NOTE]
>La chaîne secrète du client n’est visible qu’une seule fois dans la Portail Azure. Quand vous quittez la page de certificats & secrets, puis que vous y revenez, la chaîne de valeur devient masquée. Il est important de faire une copie de votre chaîne secrète client immédiatement après sa génération. Si vous n’avez pas de copie de sauvegarde de votre clé secrète client, vous devez répéter les étapes ci-dessus pour la régénérer.
 
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous vous êtes guidé à travers les étapes de l’inscription d’une application cliente confidentielle dans le Azure AD. Vous étiez également guidé à travers les étapes d’ajout d’autorisations d’API à l’API de santé Azure. Enfin, vous avez vu comment créer un secret d’application. En outre, vous pouvez apprendre à accéder à votre serveur FHIR à l’aide de la publication.
 
>[!div class="nextstepaction"]
>[Accéder au service FHIR à l’aide du poster](./../use-postman.md)
