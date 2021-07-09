---
title: 'Tutoriel : Publier un site Hugo dans Azure Static Web Apps'
description: Découvrez comment déployer une application Hugo surAzure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: aapowell
ms.openlocfilehash: ccc9089493dd8502997d7095c368c0edb83d2b70
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110586506"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps"></a>Tutoriel : Publier un site Hugo dans Azure Static Web Apps

Cet article montre comment créer et déployer une application web [Hugo](https://gohugo.io/) dans [Azure Static Web Apps](overview.md). Vous obtenez ainsi une nouvelle application Azure Static Web Apps avec la fonctionnalité GitHub Actions associée, qui vous permet de contrôler la manière dont l’application est générée et publiée.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Créer une application Hugo
> - Configurer une application Azure Static Web Apps
> - Déployer une application Hugo sur Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://azure.microsoft.com/free/).
- Un compte GitHub. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://github.com/join).

## <a name="create-a-hugo-app"></a>Créer une application Hugo

Créer une application Hugo à l’aide de l’interface de ligne de commande (CLI) Hugo :

1. Suivez le [Guide d’installation](https://gohugo.io/getting-started/installing/) pour Hugo sur votre système d’exploitation.

1. Ouvrez un terminal.

1. Exécutez l’interface CLI Hugo pour créer une application.

   ```bash
   hugo new site static-app
   ```

1. Accédez à l’application nouvellement créée.

   ```bash
   cd static-app
   ```

1. Initialisez un dépôt Git.

   ```bash
    git init
   ```

1. Ensuite, ajoutez un thème au site en installant un thème sous la forme d’un sous-module git, puis en le spécifiant dans le fichier de configuration Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Validez les modifications :

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Transmettre votre application à GitHub

Vous devez avoir un référentiel sur GitHub pour vous connecter à Azure Static Web Apps. Les étapes suivantes vous montrent comment créer un référentiel pour votre site.

1. Créez un référentiel GitHub (ne créez pas de fichier README) sur [https://github.com/new](https://github.com/new) appelé **hugo-static-app**.

1. Ajoutez le référentiel GitHub en tant que référentiel distant pour votre référentiel local. Veillez à remplacer l’espace réservé `<YOUR_USER_NAME>` par votre nom d’utilisateur GitHub dans la commande suivante.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Transmettez votre référentiel local à GitHub.

   ```bash
   git push --set-upstream origin main
   ```

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
    | _Groupe de ressources_ | **my-hugo-group**  |
    | _Nom_ | **hugo-static-app** |
    | _Type de plan_ | **Gratuit** |
    | _Région de l’API Azure Functions et des environnements intermédiaires_ | Sélectionnez la région la plus proche de vous. |
    | _Source_ | **GitHub** |

1. Sélectionnez **Se connecter avec GitHub** et authentifiez-vous auprès de GitHub.

1. Entrez les valeurs GitHub suivantes.

    | Propriété | Valeur |
    | --- | --- |
    | _Organisation_ | Sélectionnez l’organisation GitHub de votre choix. |
    | _Dépôt_ | Sélectionnez **hugo-static-app**. |
    | _Branche_ | Sélectionnez **principal**. |

1. Dans la section _Détails de build_, sélectionnez **Hugo** dans la liste déroulante _Présélections de build_ et conservez les valeurs par défaut.

### <a name="review-and-create"></a>Examiner et créer

1. Pour vérifier que les informations sont correctes, sélectionnez le bouton **Vérifier + créer**.

1. Pour démarrer la création de l’application web statique App Service et le provisionnement d’une action GitHub pour le déploiement, sélectionnez **Créer**.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

1. Sur l’écran de la ressource, cliquez sur le lien _URL_ pour ouvrir l’application que vous avez déployée. Vous devrez peut-être patienter une ou deux minutes, le temps que l’action GitHub soit terminée.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Application déployée":::

#### <a name="custom-hugo-version"></a>Version de Hugo personnalisée

Lorsque vous générez une application web statique, un [fichier de workflow](./github-actions-workflow.md) est généré. Celui-ci contient les paramètres de configuration de la publication de l’application. Vous pouvez désigner une version spécifique de Hugo dans le fichier de workflow, en fournissant une valeur pour `HUGO_VERSION` dans la section `env`. L’exemple de configuration suivant montre comment définir une version spécifique de Hugo.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](custom-domain.md)
