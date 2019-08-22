---
title: Configurer l’authentification par compte Microsoft - Azure App Service
description: Découvrez comment configurer l'authentification par compte Microsoft pour votre application App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 729c235ee51a904714358221389703632fd8296c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033628"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser un compte Microsoft comme fournisseur d’authentification. 

## <a name="register-microsoft-account"></a>Inscription de votre application avec un compte Microsoft
1. Connectez-vous au [portail Azure] et accédez à votre application. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Accédez au panneau [**Inscriptions d’applications**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) et connectez-vous avec votre compte Microsoft si nécessaire.

1. Cliquez sur **Nouvelle inscription**, puis tapez un nom d’application.

1. Dans **URI de redirection**, sélectionnez **web**, puis tapez `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`. Remplacez *\<app-domain-name>* par le nom de domaine de votre application.  Par exemple : `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Utilisez le schéma HTTPS dans l’URL.

1. sélectionnez **Inscrire**. 

1. Copier l’**ID d’application (client)** . Vous en aurez besoin ultérieurement. 
   
7. Dans le volet de navigation gauche de la nouvelle inscription d'application, sélectionnez **Certificats et secrets** > **Nouveau secret client**. Fournissez une description, sélectionnez la durée de validité, puis sélectionnez **Ajouter**.

1. Copiez la valeur qui apparaît dans la page **Certificats et secrets**. Une fois que vous quittez cette page, le mot de passe ne s’affiche plus.

    > [!IMPORTANT]
    > Le mot de passe est une information d’identification de sécurité importante. Ne partagez le mot de passe avec personne et ne le distribuez pas dans une application cliente.

## <a name="secrets"></a>Ajout des informations de compte Microsoft à votre application App Service
1. Dans le [portail Azure], accédez à votre application. À partir de la barre de navigation gauche, cliquez sur **Authentification Autorisation**.

2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, sélectionnez **Activé**.

3. Sous **Fournisseurs d’authentification**, sélectionnez **compte Microsoft**. Collez l’ID d’application (client) et le secret client obtenus précédemment et activez (facultatif) les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

4. (Facultatif) Pour restreindre l’accès aux utilisateurs du compte Microsoft, définissez **Action à exécuter quand une demande n’est pas authentifiée** pour **Se connecter avec un compte Microsoft**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers le compte Micrososft pour être authentifiées.

> [!CAUTION]
> Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications souhaitant une page d’accès publique disponible, comme dans de nombreuses applications à page unique. Pour de telles applications **Autoriser les demandes anonymes (aucune action)** peut être préféré. L’application démarre alors elle-même manuellement la connexion, comme décrit [ici](overview-authentication-authorization.md#authentication-flow).

5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser un compte Microsoft pour l’authentification dans votre application.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portail Azure]: https://portal.azure.com/
