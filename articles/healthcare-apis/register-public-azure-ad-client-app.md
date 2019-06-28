---
title: Inscrire une application cliente publique dans Azure Active Directory - API Azure pour FHIR
description: Cet article explique comment inscrire une application cliente publique dans Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 69504bc9ba0420b47a26519a0b112ff102171254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033703"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Inscrire une application cliente publique dans Azure Active Directory

Dans cet article, vous découvrez comment inscrire une application publique dans Azure Active Directory. Les inscriptions d’applications clientes sont des représentations Azure Active Directory des applications qui peuvent être utilisées pour l’authentification et la demande d’autorisations d’API au nom d’un utilisateur. Les clients publics sont des applications telles que des applications mobiles et des applications javascript monopage qui ne peut pas garder les secrets confidentiels. La procédure est similaire à [l’inscription d’un client confidentiel](register-confidential-azure-ad-client-app.md), mais étant donné que les clients publics ne peut pas être garder un secret d’application, il est inutile d’en ajouter un.

## <a name="app-registrations-in-azure-portal"></a>Inscriptions d’applications dans le portail Azure

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

2. Dans le panneau **Azure Active Directory**, cliquez sur **Inscriptions d'applications (préversion)** :

    ![Portail Azure. Nouvelle inscription d’application.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Cliquez sur **Nouvelle inscription**.

## <a name="application-registration-overview"></a>Vue d’ensemble de l’inscription d’application

1. Donnez un nom d’affichage à l’application.

2. Fournissez une URL de réponse. L’URL de réponse est utilisée pour retourner les codes d’authentification à l’application cliente. Vous pouvez ajouter plusieurs URL de réponse et les modifier ultérieurement.

    ![Portail Azure. Nouvelle inscription d’application publique.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Autorisations des API

Comme pour [l’application cliente confidentielle](register-confidential-azure-ad-client-app.md), vous devez sélectionner quelles autorisations d’API cette application peut demander au nom des utilisateurs :

1. Ouvrez les **autorisations de l’API** et sélectionnez votre [inscription d’application de ressource d’API FHIR](register-resource-azure-ad-client-app.md) :

    ![Portail Azure. Nouvelles autorisations d’API publique.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-API-PERMISSIONS.png)

2. Sélectionnez les étendues pour lesquelles vous voulez que l’application puisse effectuer une demande.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à inscrire une application cliente publique dans Azure Active Directory. Ensuite, déployez une API FHIR dans Azure.
 
>[!div class="nextstepaction"]
>[Déployer le serveur FHIR open source](fhir-oss-powershell-quickstart.md)
