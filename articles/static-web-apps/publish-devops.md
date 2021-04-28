---
title: 'Tutoriel : Publier Azure Static Web Apps avec Azure DevOps'
description: Apprenez à utiliser Azure DevOps pour publier Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 4283bccf543dc496342d64cc0e7b898a4f279866
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108141628"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Tutoriel : Publier Azure Static Web Apps avec Azure DevOps

Cet article explique comment effectuer un déploiement sur [Azure Static Web Apps](./overview.md) à l’aide d’[Azure DevOps](https://dev.azure.com/).

Ce didacticiel vous apprend à effectuer les opérations suivantes :

- Configurer un site Azure Static Web Apps
- Créer un pipeline Azure DevOps pour générer et publier une application web statique

## <a name="prerequisites"></a>Prérequis

- **Compte Azure actif :** si vous n’en avez pas, vous pouvez [créer un compte gratuitement](https://azure.microsoft.com/free/).
- **Projet Azure DevOps :** si vous n’en avez pas, vous pouvez [créer un projet gratuitement](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Pipeline Azure DevOps :** si vous avez besoin d’aide pour commencer, consultez [Créer votre premier pipeline](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Créer une application web statique dans un dépôt Azure DevOps

  > [!NOTE]
  > Si vous disposez d’une application existante dans votre dépôt, vous pouvez passer à la section suivante.

1. Accédez à votre dépôt Azure DevOps.

1. Sélectionnez **Importer** pour commencer l’importation d’un exemple d’application.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Dépôt DevOps":::

1. Dans **URL de clone**, entrez `https://github.com/staticwebdev/vanilla-api.git`.

1. Sélectionnez **Importer**.

## <a name="create-a-static-web-app"></a>Créer une application web statique

1. Accédez au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Créer une ressource**.

1. Recherchez **Static Web Apps**.

1. Sélectionnez **Static Web Apps (Préversion)** .

1. Sélectionnez **Create** (Créer).

1. Sous _Détails du déploiement_, veillez à sélectionner **Autre**. Cela vous permet d’utiliser le code de votre dépôt Azure DevOps.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Détails du déploiement - Autre":::

1. Une fois le déploiement réussi, accédez à la nouvelle ressource Static Web Apps.

1. Sélectionnez **Gérer le jeton de déploiement**.

1. Copiez le **jeton de déploiement**, puis collez-le dans un éditeur de texte afin de l’utiliser dans un autre écran.

    > [!NOTE]
    > Cette valeur est mise de côté pour l’instant, car vous allez copier et coller d’autres valeurs dans les étapes à venir.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Jeton de déploiement":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Créer la tâche de pipeline dans Azure DevOps

1. Accédez au dépôt Azure DevOps créé précédemment.

1. Sélectionnez **Configurer la build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline de build":::

1. Dans l’écran *Configurer votre pipeline*, sélectionnez **Pipeline de démarrage**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configurer le pipeline":::

1. Copiez et collez le YAML suivant dans votre pipeline.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Si vous n’utilisez pas l’exemple d’application, les valeurs de `app_location`, de `api_location` et de `output_location` doivent être modifiées pour correspondre aux valeurs utilisées dans votre application.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    La valeur `azure_static_web_apps_api_token` est autogérée et est configurée manuellement.

1. Sélectionnez **Variables**.

1. Créez une nouvelle variable.

1. Nommez la variable **deployment_token** (en utilisant le nom spécifié dans le workflow).

1. Copiez le jeton de déploiement que vous avez précédemment collé dans un éditeur de texte.

1. Collez le jeton de déploiement dans la zone _Valeur_.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Jeton de variable":::

1. Sélectionnez **Garder cette valeur secrète**.

1. Sélectionnez **OK**.

1. Sélectionnez **Enregistrer** pour revenir au YAML de votre pipeline.

1. Sélectionnez **Enregistrer et exécuter** pour ouvrir la boîte de dialogue _Enregistrer et exécuter_.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. Sélectionnez **Enregistrer et exécuter** pour exécuter le pipeline.

1. Une fois le déploiement réussi, accédez à la **Vue d’ensemble** d’Azure Static Web Apps, qui comprend des liens vers la configuration du déploiement. Notez que le lien _Source_ pointe maintenant vers la branche et l’emplacement du dépôt Azure DevOps.

1. Sélectionnez l’**URL** pour voir le site web nouvellement déployé.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Emplacement du déploiement":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer Azure Static Web Apps](./configuration.md)