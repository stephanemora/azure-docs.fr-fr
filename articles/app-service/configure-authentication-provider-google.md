---
title: Configurer l’authentification Google – Azure App Service
description: Découvrez comment configurer l'authentification Google pour votre application App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232135"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Comment configurer votre application App Service de manière à utiliser la connexion via Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, accédez à [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"></a>Inscription de votre application avec Google
1. Consultez la documentation Google relative à [Google Sign-In pour les applications côté serveur](https://developers.google.com/identity/sign-in/web/server-side-flow) pour créer un ID client et une clé secrète client, avec les informations suivantes (sans qu'il soit nécessaire d’apporter des modifications au code) :
    - Pour **Origines JavaScript autorisées**, utilisez `https://<app-name>.azurewebsites.net` avec le nom de votre application dans *\<app-name>* .
    - Pour **URI de redirection autorisée**, utilisez `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Une fois l’ID client et la clé secrète client créés, copiez leurs valeurs.

    > [!IMPORTANT]
    > La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.


## <a name="secrets"></a>Ajout des informations Google à votre application
1. Dans le [portail Azure], accédez à votre application App Service. Dans le menu de gauche, sélectionnez **Authentification/Autorisation**.
2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.
3. Cliquez sur **Google**. Collez les valeurs d’ID de l’application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

   App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Pour plus d’informations, consultez [Autoriser ou refuser des utilisateurs](app-service-authentication-how-to.md#authorize-or-deny-users).
4. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Google, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Google**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Google pour être authentifiées.

    > [!NOTE]
    > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications souhaitant une page d’accès publique disponible, comme dans de nombreuses applications à page unique. Pour de telles applications **Autoriser les demandes anonymes (aucune action)** peut être préféré. L’application démarre alors elle-même manuellement la connexion, comme décrit [ici](overview-authentication-authorization.md#authentication-flow).
    
5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Google pour l'authentification dans votre application.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portail Azure]: https://portal.azure.com/

