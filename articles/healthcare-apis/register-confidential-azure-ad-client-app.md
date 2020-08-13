---
title: Inscrire une application cliente confidentielle dans Azure AD - API Azure pour FHIR
description: Inscrire une application cliente confidentielle dans Azure Active Directory qui s’authentifie au nom d’un utilisateur et demande l’accès aux applications de ressources.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852546"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Inscrire une application cliente confidentielle dans Azure Active Directory

Dans ce tutoriel, vous découvrirez comment inscrire une application cliente confidentielle dans Azure Active Directory. 

L’inscription d’une application cliente est une représentation Azure Active Directory d’une application qui peut être utilisée pour authentifier au nom d’un utilisateur et demander l’accès aux [applications de ressources](register-resource-azure-ad-client-app.md). Une application cliente confidentielle est une application qui peut être approuvée pour garder un secret et présenter ce secret lors de la demande de jetons d’accès. Ces exemples d’applications confidentielles sont des applications côté serveur.

Pour inscrire une nouvelle application confidentielle dans le portail, suivez les étapes ci-dessous.

## <a name="app-registrations-in-azure-portal"></a>Inscriptions d’applications dans le portail Azure

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

2. Dans le panneau **Azure Active Directory**, cliquez sur **Inscriptions d’applications** :

    ![Portail Azure. Nouvelle inscription d’application.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Cliquez sur **Nouvelle inscription**.

## <a name="register-a-new-application"></a>Inscrire une nouvelle application

1. Donnez un nom d’affichage à l’application.

2. Fournissez une URL de réponse. Ces détails peuvent être modifiés ultérieurement, mais si vous connaissez l’URL de réponse de votre application, saisissez-la maintenant.

    ![Nouvelle inscription d’application cliente confidentielle.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Autorisations des API

Ajoutez ensuite des autorisations d’API :

1. Accédez aux **autorisations d’API** :

    ![Client confidentiel. Autorisations des API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Cliquez sur **Ajouter une autorisation**.

3. Sélectionnez l’API de ressource appropriée :

    Pour l’API Azure pour FHIR (service managé), cliquez sur **API utilisées par mon organisation** et recherchez « API Azure Healthcare ». Pour le serveur Open Source FHIR pour Azure, sélectionnez votre [Inscription d’application de ressource d’API FHIR](register-resource-azure-ad-client-app.md) :

    ![Client confidentiel. Mes API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Sélectionnez les étendues (autorisations) que l’application confidentielle doit pouvoir demander au nom d’un utilisateur :

    ![Client confidentiel. Autorisations déléguées](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Secret d’application

1. Créez un secret d’application (clé secrète client) :

    ![Client confidentiel. Secret d’application](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Fournissez une description et la durée du secret.

3. Une fois généré, il ne s’affichera qu’une seule fois dans le portail. Notez-le et stockez-le en toute sécurité.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment inscrire une application cliente confidentielle dans Azure Active Directory. Vous êtes maintenant prêt à déployer l’[API Azure pour FHIR](fhir-paas-powershell-quickstart.md).

Une fois que vous avez déployé l’API Azure pour FHIR, vous pouvez passer en revue les paramètres disponibles supplémentaires.
 
>[!div class="nextstepaction"]
>[Déployer l’API Azure pour FHIR](fhir-paas-powershell-quickstart.md)