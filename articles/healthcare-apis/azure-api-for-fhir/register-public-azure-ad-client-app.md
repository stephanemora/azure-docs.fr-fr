---
title: Inscrire une application cliente publique dans Azure AD - API Azure pour FHIR
description: Cet article explique comment inscrire une application cliente publique dans Azure Active Directory, en préparation du déploiement de l’API FHIR dans Azure.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: e89d577385e41fc1e2dcd0b58afafc8ccf5371c8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278307"
---
# <a name="register-a-public-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>inscrire une application cliente publique dans Azure Active Directory pour l’API Azure pour FHIR

Dans cet article, vous découvrez comment inscrire une application publique dans Azure Active Directory.  

Les inscriptions d’applications clientes sont des représentations Azure Active Directory des applications qui peuvent être utilisées pour l’authentification et la demande d’autorisations d’API au nom d’un utilisateur. Les clients publics sont des applications telles que des applications mobiles et des applications JavaScript monopages qui ne peuvent pas garder les secrets confidentiels. La procédure est similaire à [l’inscription d’un client confidentiel](register-confidential-azure-ad-client-app.md), mais étant donné que les clients publics ne peut pas être garder un secret d’application, il est inutile d’en ajouter un.

Le guide de démarrage rapide fournit des informations générales sur la façon d’[inscrire une application auprès de la plateforme d’identités Microsoft](../../active-directory/develop/quickstart-register-app.md).

## <a name="app-registrations-in-azure-portal"></a>Inscriptions d’applications dans le portail Azure

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

2. Dans le panneau **Azure Active Directory**, cliquez sur **Inscriptions d’applications** :

    ![Portail Azure. Nouvelle inscription d’application.](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. Cliquez sur **Nouvelle inscription**.

## <a name="application-registration-overview"></a>Vue d’ensemble de l’inscription d’application

1. Donnez un nom d’affichage à l’application.

2. Fournissez une URL de réponse. L’URL de réponse est utilisée pour retourner les codes d’authentification à l’application cliente. Vous pouvez ajouter plusieurs URL de réponse et les modifier ultérieurement.

    ![Portail Azure. Nouvelle inscription d’application publique.](media/add-azure-active-directory/portal-aad-register-new-app-registration-pub-client-name.png)


Pour configurer votre application [de bureau](../../active-directory/develop/scenario-desktop-app-registration.md), [mobile](../../active-directory/develop/scenario-mobile-app-registration.md) ou [monopage](../../active-directory/develop/scenario-spa-app-registration.md) en tant qu’application publique :

1. Dans le [portail Azure](https://portal.azure.com), dans **Inscriptions d’applications**, sélectionnez votre application, puis sélectionnez **Authentification**.

2. Sélectionnez **Paramètres avancés** > **Type de client par défaut**. Pour **Considérer l’application comme un client public**, sélectionnez **Oui**.

3. Pour une application monopage, sélectionnez **Jetons d’accès** et **Jetons d’ID** afin d’activer le flux implicite.

   - Si votre application connecte des utilisateurs, sélectionnez **Jetons d’ID**.
   - Si votre application doit également appeler une API web protégée, sélectionnez **Jetons d’accès**.

## <a name="api-permissions"></a>Autorisations des API

Les autorisations pour l’API Azure pour FHIR sont gérées par le biais de RBAC. Pour plus d’informations, consultez [configurer Azure RBAC pour FHIR](configure-azure-rbac.md).

>[!NOTE]
>Utilisez grant_type de client_credentials lors de la tentative d’otain d’un jeton d’accès pour l’API Azure pour FHIR à l’aide d’outils tels que poster. Pour plus d’informations, consultez [test de l’API FHIR sur Azure API pour FHIR](tutorial-web-app-test-postman.md).

## <a name="validate-fhir-server-authority"></a>Valider l’autorité du serveur FHIR
Si l’application que vous avez inscrite dans cet article et votre serveur FHIR se trouvent dans le même locataire Azure AD, vous pouvez passer aux étapes suivantes.

Si vous configurez votre application cliente dans un autre locataire Azure AD que celui de votre serveur FHIR, vous devez mettre à jour l’**l’autorité**. Dans l’API Azure pour FHIR, vous définissez l’autorité sous Paramètres --> Authentification. Définissez votre autorité sur' ' https://login.microsoftonline.com/ \<TENANT-ID> '.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente publique dans Azure Active Directory. Testez ensuite l’accès à votre serveur FHIR à l’aide de Postman.
 
>[!div class="nextstepaction"]
>[Accéder au service FHIR à l’aide du poster](./../use-postman.md)
