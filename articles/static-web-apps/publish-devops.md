---
title: 'Tutoriel : Publier Azure Static Web Apps avec Azure DevOps'
description: Apprenez à utiliser Azure DevOps pour publier Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 17a41bd64f1bba4a5ae4d6d9d497c03afae037e7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444223"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Tutoriel : Publier Azure Static Web Apps avec Azure DevOps

Cet article explique comment effectuer un déploiement sur [Azure Static Web Apps](./overview.md) à l’aide d’[Azure DevOps](https://dev.azure.com/).

Ce didacticiel vous apprend à effectuer les opérations suivantes :

- Configurer un site Azure Static Web Apps
- Créer un pipeline Azure pour générer et publier une application web statique

## <a name="prerequisites"></a>Prérequis

- **Compte Azure actif :** si vous n’en avez pas, vous pouvez [créer un compte gratuitement](https://azure.microsoft.com/free/).
- **Projet Azure DevOps :** si vous n’en avez pas, vous pouvez [créer un projet gratuitement](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
  - Azure DevOps inclut **Azure Pipelines**. Si vous avez besoin d’aide pour prendre en main Azure Pipelines, consultez [Créer votre premier pipeline](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops).
  - Actuellement, la tâche de pipeline pour application web statique fonctionne uniquement sur les ordinateurs **Linux**. Quand vous exécutez le pipeline indiqué ci-dessous, assurez-vous qu’il s’exécute sur une machine virtuelle Linux.

## <a name="create-a-static-web-app-in-an-azure-devops"></a>Créer une application web statique dans une instance Azure DevOps

  > [!NOTE]
  > Si vous disposez d’une application existante dans votre dépôt, vous pouvez passer à la section suivante.

1. Accédez à votre référentiel dans Azure Repos.

1. Sélectionnez **Importer** pour commencer l’importation d’un exemple d’application.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Dépôt DevOps":::

1. Dans **URL de clone**, entrez `https://github.com/staticwebdev/vanilla-api.git`.

1. Sélectionnez **Importer**.

## <a name="create-a-static-web-app"></a>Créer une application web statique

1. Accédez au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Créer une ressource**.

1. Recherchez **Static Web Apps**.

1. Sélectionnez **Static Web Apps**.

1. Sélectionnez **Create** (Créer).

1. Sous _Détails du déploiement_, veillez à sélectionner **Autre**. Cela vous permet d’utiliser le code dans Azure Repos.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Détails du déploiement - Autre":::

1. Une fois le déploiement réussi, accédez à la nouvelle ressource Static Web Apps.

1. Sélectionnez **Gérer le jeton de déploiement**.

1. Copiez le **jeton de déploiement**, puis collez-le dans un éditeur de texte afin de l’utiliser dans un autre écran.

    > [!NOTE]
    > Cette valeur est mise de côté pour l’instant, car vous allez copier et coller d’autres valeurs dans les étapes à venir.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Jeton de déploiement":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Créer la tâche de pipeline dans Azure DevOps

1. Accédez au référentiel créé précédemment dans Azure Repos.

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
          app_location: '/'
          api_location: 'api'
          output_location: ''
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Si vous n’utilisez pas l’exemple d’application, les valeurs de `app_location`, de `api_location` et de `output_location` doivent être modifiées pour correspondre aux valeurs utilisées dans votre application.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    La valeur `azure_static_web_apps_api_token` est autogérée et est configurée manuellement.

2. Sélectionnez **Variables**.

3. Créez une nouvelle variable.

4. Nommez la variable **deployment_token** (en utilisant le nom spécifié dans le workflow).

5. Copiez le jeton de déploiement que vous avez précédemment collé dans un éditeur de texte.

6. Collez le jeton de déploiement dans la zone _Valeur_.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Jeton de variable":::

7. Sélectionnez **Garder cette valeur secrète**.

8. Sélectionnez **OK**.

9. Sélectionnez **Enregistrer** pour revenir au YAML de votre pipeline.

10. Sélectionnez **Enregistrer et exécuter** pour ouvrir la boîte de dialogue _Enregistrer et exécuter_.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

11. Sélectionnez **Enregistrer et exécuter** pour exécuter le pipeline.

12. Une fois le déploiement réussi, accédez à la **Vue d’ensemble** d’Azure Static Web Apps, qui comprend des liens vers la configuration du déploiement. Notez que le lien _Source_ pointe maintenant vers la branche et l’emplacement du dépôt Azure DevOps.

13. Sélectionnez l’**URL** pour voir le site web nouvellement déployé.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Emplacement du déploiement":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer Azure Static Web Apps](./configuration.md)
