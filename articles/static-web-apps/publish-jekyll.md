---
title: 'Tutoriel : Publier un site Jekyll dans Azure Static Web Apps'
description: Découvrez comment déployer une application Jekyll dans Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: cshoe
ms.openlocfilehash: 0f572d49867fe9149416664a405309253dd01af2
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202931"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Tutoriel : Publier un site Jekyll dans Azure Static Web Apps (préversion)

Cet article montre comment créer et déployer une application web [Jekyll](https://jekyllrb.com/) dans [Azure Static Web Apps](overview.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Créer un site web Jekyll
> - Configurer une ressource Azure Static Web Apps
> - Déployer l’application Jekyll sur Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Installer [Jekyll](https://jekyllrb.com/docs/installation/)
  - Vous pouvez utiliser le sous-système Windows pour Linux et suivre les instructions Ubuntu, si nécessaire.
- Compte Azure avec un abonnement actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://azure.microsoft.com/free/).
- Un compte GitHub. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://github.com/join).

## <a name="create-jekyll-app"></a>Créer une application Jekyll

Créez une application Jekyll en utilisant l’interface de ligne de commande (CLI) Jekyll :

1. À partir du terminal, exécutez l’interface CLI Jekyll pour créer une application.

   ```bash
   jekyll new static-app
   ```

1. Accédez à l’application nouvellement créée.

   ```bash
   cd static-app
   ```

1. Initialisez un nouveau dépôt Git.

   ```bash
    git init
   ```

1. Validez les modifications :

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Transmettre votre application à GitHub

Azure Static Web Apps utilise GitHub pour publier votre site web. Les étapes suivantes vous montrent comment créer un dépôt GitHub.

1. Créez un dépôt GitHub vide (ne créez pas de fichier README) sur [https://github.com/new](https://github.com/new) appelé **Jekyll-static-app**.

1. Ajoutez le référentiel GitHub en tant que référentiel distant pour votre référentiel local. Veillez à remplacer l’espace réservé `<YOUR_USER_NAME>` par votre nom d’utilisateur GitHub dans la commande suivante.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Transmettez votre référentiel local à GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Votre branche git peut avoir un nom autre que `main`. Remplacez `main` dans cette commande par la valeur correcte.

## <a name="deploy-your-web-app"></a>Déployez votre application web

Les étapes suivantes vous montrent comment créer une application Static Web Apps et la déployer dans un environnement de production.

### <a name="create-the-application"></a>Création de l'application

1. Accédez au [portail Azure](https://portal.azure.com).

1. Cliquez sur **Créer une ressource**.

1. Recherchez **Static Web Apps**.

1. Cliquez sur **Static Web Apps (Préversion)** .

1. Cliquez sur **Créer**.

1. Pour **Abonnement**, acceptez l’abonnement qui est listé ou sélectionnez-en un nouveau dans la liste déroulante.

1. Dans _Groupe de ressources_, sélectionnez **Nouveau**. Dans _Nom du nouveau groupe de ressources_, entrez **jekyll-static-app**, puis sélectionnez **OK**.

1. Ensuite, indiquez un nom pour votre application dans la zone _Nom_. Les caractères valides sont `a-z`, `A-Z`, `0-9` et `-`.

1. Dans _Region_ (Région), sélectionnez une région disponible proche de vous.

1. Pour _SKU_, sélectionnez **Free** (Gratuit).

1. Dans _Détails du déploiement_, sélectionnez **GitHub** pour _Source_.

1. Cliquez sur le bouton **Sign in with GitHub** (Se connecter avec GitHub).

1. Sélectionnez l’**organisation** sous laquelle vous avez créé le référentiel.

1. Sélectionnez **jekyll-static-app** comme _Dépôt_.

1. Comme _Branche_, sélectionnez **main**.

### <a name="build"></a>Build

Vous ajoutez ensuite les paramètres de configuration utilisés par le processus de génération pour générer votre application. Les paramètres suivants configurent le fichier de workflow d’action GitHub.

1. Pour les _options prédéfinies de build_, sélectionnez **Personnalisé**.

1. Définissez _App location_ (Emplacement de l’application) sur **/** .

1. Définissez l’_emplacement de sortie_ sur **_site**.

   Il n’est pas nécessaire de fournir une valeur pour _API location_ (Emplacement de l’API), puisque vous ne déployez pas d’API pour le moment.

   :::image type="content" source="./media/publish-jekyll/github-actions-inputs.png" alt-text="Entrées GitHub Actions":::

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, cliquez sur le bouton **Review + Create** (Vérifier + créer).

1. Pour démarrer la création de l’application Azure Static Web Apps et la configuration d’une action GitHub pour le déploiement, cliquez sur **Create** (Créer).

1. Attendez que l’action GitHub se termine.

1. Dans la fenêtre _Overview_ (Vue d’ensemble) du portail Azure de la ressource Azure Static Web Apps nouvellement créée, cliquez sur le lien _URL_ pour ouvrir l’application que vous avez déployée.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Application déployée":::

#### <a name="custom-jekyll-settings"></a>Paramètres Jekyll personnalisés

Quand vous générez une application web statique, un [fichier de workflow](./github-actions-workflow.md) est généré. Celui-ci contient les paramètres de configuration de la publication de l’application.

Pour configurer des variables d’environnement, comme `JEKYLL_ENV`, ajoutez une section `env` à l’action GitHub Azure Static Web Apps dans le workflow.

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v0.0.1-preview
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site_" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](custom-domain.md)
