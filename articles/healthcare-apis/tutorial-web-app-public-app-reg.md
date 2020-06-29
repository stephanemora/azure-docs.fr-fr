---
title: Tutoriel d’application web – Configuration de l’application cliente
description: Ce tutoriel vous accompagne tout au long des étapes d’inscription d’une application publique en vue de préparer le déploiement d’une application web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870452"
---
# <a name="client-application-registration"></a>Inscription d’une application cliente
Dans le tutoriel précédent, vous avez déployé et configuré votre API Azure pour FHIR. Maintenant que votre API Azure pour FHIR est configurée, nous allons inscrire une application cliente publique. Vous pouvez parcourir entièrement le guide pratique qui explique comment [inscrire une application cliente publique](register-public-azure-ad-client-app.md) ou comment résoudre les problèmes, mais nous en avons extrait les principales étapes dans le tutoriel ci-dessous.

1. Accéder à Azure Active Directory
1. Sélectionnez **Inscription des applications** --> **Nouvelle inscription**.
1. Nommez votre application et configurez l’URI de redirection sur https://www.getpostman.com/oauth2/callback.


![Inscription d’une application cliente](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Paramètres de l’application cliente
Une fois l’application inscrite, copiez l’ID d’application (client) à partir de la page Vue d’ensemble. Vous aurez par la suite besoin de cette valeur au moment d’accéder au client.

![Copier l’ID d’application](media/tutorial-web-app/app-id.png)

Ensuite, définissez les options d’authentification appropriées. Sélectionnez **Authentification** sur le côté gauche. Cochez les cases **Jeton d’accès** et **Jeton d’ID**. Vous pouvez aussi configurer l’URI de redirection en préparation de la création de votre application web dans la quatrième partie de ce tutoriel. Pour cela, ajoutez https://\<WEB-APP-NAME>.azurewebsites.net à la liste des URI de redirection. Si vous avez choisi un nom différent au moment d’[écrire votre application web](tutorial-web-app-write-web-app.md), vous devez revenir en arrière et le mettre à jour.

![Paramètres d’authentification de l’application](media/tutorial-web-app/app-authentication.png)

Maintenant que vous avez configuré l’authentification appropriée, définissez les autorisations de l’API. 
1. Sélectionnez **Autorisations de l’API**, puis cliquez sur **Ajouter une autorisation**.
1. Sous **API utilisées par mon organisation**, effectuez une recherche sur Azure Healthcare API.
1. Sélectionnez **user_impersonation**, puis cliquez sur **Ajouter des autorisations**.

## <a name="next-steps"></a>Étapes suivantes
Vous disposez à présent d’une application cliente publique. Dans le prochain tutoriel, nous allons voir en détail comment tester et accéder à cette application via Postman.

>[!div class="nextstepaction"]
>[Tester l’application cliente dans Postman](tutorial-web-app-test-postman.md)
