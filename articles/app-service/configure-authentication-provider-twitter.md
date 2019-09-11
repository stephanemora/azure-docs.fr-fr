---
title: Configurer l’authentification Twitter – Azure App Service
description: Découvrez comment configurer l'authentification Twitter pour votre application App Service.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: d49b8bf9c62813023c1a1e06e0f8fc0d7809f48d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232057"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Comment configurer votre application App Service de manière à utiliser la connexion via Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Twitter comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Twitter avec une adresse électronique et un numéro de téléphone vérifiés. Pour créer un compte Twitter, consultez la page <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"></a>Inscription de votre application avec Twitter
1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Twitter.
2. Accédez au site web [Twitter Developers] , connectez-vous avec vos identifiants Twitter, puis cliquez sur **Create New App**.
3. Entrez le **Nom** et une **Description** pour votre nouvelle application. Collez l’**URL** de votre application en guise de **Site web**. Ensuite, pour l'**URL de rappel**, entrez l’URL de votre application App Service et ajoutez le chemin d'accès `/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Assurez-vous d'utiliser le schéma HTTPS.
4. Au bas de la page, lisez et acceptez les termes du contrat. Ensuite, cliquez sur **Create your Twitter application**. Les détails de l’application s’affichent.
5. Cliquez sur l’onglet **Paramètres**, activez l’option **Autoriser la connexion à Twitter via cette application**, puis cliquez sur **Mettre à jour les paramètres**.
6. Sélectionnez l’onglet **Keys and Access Tokens** . Prenez note des valeurs de **Clé de consommateur (clé API)** et de **Secret de consommateur (clé secrète API)** .
   
   > [!NOTE]
   > La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.
   > 
   > 

## <a name="secrets"></a>Ajout des informations Twitter à votre application
1. Revenez au [portail Azure]et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.
2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.
3. Cliquez sur **Twitter**. Collez-y les valeurs d'ID d'application et de clé secrète d'application que vous avez obtenues précédemment. Cliquez ensuite sur **OK**.
   
   ![][1]
   
   Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
4. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Twitter, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Twitter**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Twitter pour être authentifiées.

> [!NOTE]
> Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications souhaitant une page d’accès publique disponible, comme dans de nombreuses applications à page unique. Pour de telles applications **Autoriser les demandes anonymes (aucune action)** peut être préféré. L’application démarre alors elle-même manuellement la connexion, comme décrit [ici](overview-authentication-authorization.md#authentication-flow).

5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Twitter pour l'authentification dans votre application.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Portail Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
