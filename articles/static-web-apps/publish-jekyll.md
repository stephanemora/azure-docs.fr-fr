---
title: 'Tutoriel : Publier un site Jekyll dans Azure Static Web Apps'
description: Découvrez comment déployer une application Jekyll dans Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: cshoe
ms.openlocfilehash: 179fa0e247b2c875a4d32eac312d240ae768c009
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110190552"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps"></a>Tutoriel : Publier un site Jekyll dans Azure Static Web Apps

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

1. Accéder au [portail Azure](https://portal.azure.com)
1. Sélectionnez **Créer une ressource**
1. Recherchez **Applications web statiques**
1. Sélectionnez **Static Web Apps**
1. Sélectionnez **Créer**
1. Sous l’onglet _Informations de base_, entrez les valeurs suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Abonnement_ | Le nom de votre abonnement Azure. |
    | _Groupe de ressources_ | **jekyll-static-app**  |
    | _Nom_ | **jekyll-static-app** |
    | _Type de plan_ | **Gratuit** |
    | _Région de l’API Azure Functions et des environnements intermédiaires_ | Sélectionnez la région la plus proche de vous. |
    | _Source_ | **GitHub** |

1. Sélectionnez **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Entrez les valeurs GitHub suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Organisation_ | Sélectionnez l’organisation GitHub de votre choix. |
    | _Dépôt_ | Sélectionnez **jekyll-static-app**. |
    | _Branche_ | Sélectionnez **principal**. |

1. Dans la section _Détails de build_, sélectionnez **Personnalisé** dans la liste déroulante _Présélections de build_ et conservez les valeurs par défaut.

1. Dans la zone _Emplacement de l’application_, entrez **./** .

1. Laissez la zone _Emplacement de l’API_ vide.

1. Dans la zone _Emplacement de la sortie_, entrez **_site**.

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, sélectionnez le bouton **Vérifier + créer**.

1. Pour démarrer la création de l’application web statique App Service et le provisionnement d’une action GitHub pour le déploiement, sélectionnez **Créer**.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

1. Sur l’écran de la ressource, cliquez sur le lien _URL_ pour ouvrir l’application que vous avez déployée. Vous devrez peut-être patienter une ou deux minutes, le temps que l’action GitHub soit terminée.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Application déployée":::

#### <a name="custom-jekyll-settings"></a>Paramètres Jekyll personnalisés

Quand vous générez une application web statique, un [fichier de workflow](./github-actions-workflow.md) est généré. Celui-ci contient les paramètres de configuration de la publication de l’application.

Pour configurer des variables d’environnement, comme `JEKYLL_ENV`, ajoutez une section `env` à l’action GitHub Azure Static Web Apps dans le workflow.

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v1
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](custom-domain.md)
