---
title: 'Démarrage rapide : Créer une application web Node.js - Linux'
description: Déployez votre premier programme Hello World Node.js dans Azure App Service en quelques minutes.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59a2c96987762e6b56cc7b453877cebe3124e443
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109055"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Déployer sur Azure App Service à partir de Visual Studio Code

1. Ouvrez le dossier de votre application dans Visual Studio Code.

    ```bash
    code .
    ```

1. Dans l’Explorateur **AZURE APP SERVICE**, sélectionnez **Se connecter à Azure...** , puis suivez les instructions. Quand vous êtes connecté, l’explorateur doit afficher le nom de votre abonnement Azure.

    ![Se connecter à Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
    <details>
    <summary>Résolution des problèmes de connexion à Azure</summary>
    
    Si vous rencontrez l’erreur **« Cannot find subscription with name [subscription ID] »** (L’abonnement avec le nom [ID d’abonnement] est introuvable) quand vous vous connectez à Azure, cela peut être dû au fait que vous vous trouvez derrière un proxy qui vous empêche d’atteindre l’API Azure. Configurez les variables d’environnement `HTTP_PROXY` et `HTTPS_PROXY` avec vos informations de proxy dans votre terminal à l’aide de `export`.
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Signaler un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. Dans l’explorateur **AZURE APP SERVICE**, sélectionnez l’icône représentant une flèche bleue pointant vers le haut pour déployer votre application sur Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Capture d’écran d’Azure App Service dans VS Code montrant l’icône de flèche bleue sélectionnée.":::

1. Choisissez le répertoire actuellement ouvert, `nodejs-docs-hello-world`.

1. Choisissez **Créer une application web** pour déployer sur App Service sur Linux par défaut.

1. Tapez un <abbr title="Les caractères valides dans un nom d’application sont les suivants : « a-z », « 0-9 » et « - ».">name</abbr> global unique pour votre application web et appuyez sur **Entrée**. 

1. Choisissez votre **version Node.js** (LTS est recommandé).

    Le canal de notification affiche les ressources Azure qui sont créées pour votre application.

1. Sélectionnez **Oui** lorsque vous êtes invité à mettre à jour votre configuration pour exécuter `npm install` sur le serveur cible. Votre application est alors déployée.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Capture d’écran de l’invite de mise à jour de votre configuration sur le serveur cible avec le bouton Oui sélectionné.":::

1. Choisissez **Oui** quand vous êtes invité à mettre à jour votre espace de travail afin que les déploiements ultérieurs ciblent automatiquement la même application web App Service. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Capture d’écran de l’invite de mise à jour de votre espace de travail avec le bouton Oui sélectionné.":::




1. Une fois le déploiement terminé, sélectionnez **Parcourir le site web** dans l’invite pour afficher l’application web que vous venez de déployer.

<br/>
<details>
<summary><strong>Dépannage</strong></summary>

Si vous n’avez pas pu effectuer ces étapes, vérifiez les points suivants :

* Assurez-vous que votre application utilise le port d’écoute fourni par la variable d’environnement PORT `process.env.PORT`.

* Si vous voyez s’afficher le message d’erreur **« Vous n’êtes pas autorisé à afficher ce répertoire ou cette page. »** , c’est que l’application n’a probablement pas réussi à démarrer correctement. Examinez la sortie du journal pour rechercher et corriger l’erreur. 

</details>

<br>

[Signaler un problème](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>


