---
title: Inscrire une application de service dans Azure AD - API Azure pour FHIR
description: Découvrez comment inscrire une application cliente de service dans Azure Active Directory.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 3eb942160e45c589377ee3ecd283ea2939a23934
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273397"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>inscrire une application cliente de service dans Azure Active Directory pour l’API Azure pour FHIR

Dans cet article, vous découvrez comment inscrire une application cliente de service dans Azure Active Directory. Les inscriptions d’applications clientes sont des représentations Azure Active Directory des applications qui peuvent être utilisées pour l’authentification et l’obtention de jetons. Un client de service est destiné à être utilisé par une application pour obtenir un jeton d’accès sans authentification interactive d’un utilisateur. Il disposera de certaines permissions d’application et utilisera un secret d’application (mot de passe) pour l’obtention de jetons d’accès.

Suivez cette procédure pour créer un client de service.

## <a name="app-registrations-in-azure-portal"></a>Inscriptions d’applications dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), accédez à **Azure Active Directory**.

2. Sélectionnez **Inscriptions d’applications**.

    ![Portail Azure. Nouvelle inscription d’application.](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. Sélectionnez **Nouvelle inscription**.

4. Attribuez un nom d’affichage au client de service. En général, les applications clientes de service n’utilisent pas d’URL de réponse.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Portail Azure. Nouvelle inscription d’application cliente de service.":::

5. Sélectionnez **Inscription**.

## <a name="api-permissions"></a>Autorisations des API

Les autorisations pour l’API Azure pour FHIR sont gérées par le biais de RBAC. Pour plus d’informations, consultez [configurer Azure RBAC pour FHIR](configure-azure-rbac.md).

>[!NOTE]
>Utilisez grant_type de client_credentials lors de la tentative d’otain d’un jeton d’accès pour l’API Azure pour FHIR à l’aide d’outils tels que poster. Pour plus d’informations, consultez [test de l’API FHIR sur Azure API pour FHIR](tutorial-web-app-test-postman.md).

## <a name="application-secret"></a>Secret d’application

Le client de service a besoin d’un secret (mot de passe) pour obtenir un jeton.

1. Cliquez sur **Certificats et secrets**.
2. Sélectionnez **Nouveau secret client**.

    ![Portail Azure. Clé secrète client de service](media/add-azure-active-directory/portal-aad-register-new-app-registration-service-client-secret.png)

3. Fournissez une description et la durée du secret (soit 1 an, soit 2 ans, soit jamais).

4. Après avoir été généré, le secret sera affiché une seule fois dans le portail. Notez-le et stockez-le en toute sécurité.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente de service dans Azure Active Directory. Testez ensuite l’accès à votre serveur FHIR à l’aide de Postman.
 
>[!div class="nextstepaction"]
>[Accéder au service FHIR à l’aide du poster](./../use-postman.md)
