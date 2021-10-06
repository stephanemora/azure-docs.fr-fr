---
title: 'Tutoriel : Publier Azure Static Web Apps avec Azure DevOps'
description: Apprenez à utiliser Azure DevOps pour publier Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: apedward
ms.openlocfilehash: bc1e6c5ad5423f48eed50c65237b0d42a4e61d7a
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236215"
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

1. Créez une application web statique avec les valeurs suivantes.

    :::image type="content" source="media/publish-devops/azure-portal-static-web-apps-devops.png" alt-text="Détails du déploiement - Autre":::

    | Paramètre | Valeur |
    |---|---|
    | Abonnement | Le nom de votre abonnement Azure. |
    | Groupe de ressources | Sélectionnez un nom de groupe existant ou créez-en un. |
    | Nom | Entrez **myDevOpsApp**. |
    | Type de plan d’hébergement | Sélectionnez **Gratuit**. |
    | Région | Sélectionnez la région la plus proche de vous. |
    | Source | Sélectionnez **Autre**. |

1. Sélectionner **Vérifier + créer**

1. Sélectionnez **Create** (Créer).

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. Sélectionnez **Gérer le jeton de déploiement**.

1. Copiez le **jeton de déploiement**, puis collez sa valeur dans un éditeur de texte afin de l’utiliser dans un autre écran.

    > [!NOTE]
    > Cette valeur est mise de côté pour l’instant, car vous allez copier et coller d’autres valeurs dans les étapes à venir.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Jeton de déploiement":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Créer la tâche de pipeline dans Azure DevOps

1. Accédez au référentiel créé précédemment dans Azure Repos.

2. Sélectionnez **Configurer la build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline de build":::

3. Dans l’écran *Configurer votre pipeline*, sélectionnez **Pipeline de démarrage**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configurer le pipeline":::

4. Copiez le YAML suivant et remplacez la configuration générée dans votre pipeline par ce code.

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
          app_location: '/src'
          api_location: 'api'
          output_location: '/src'
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Si vous n’utilisez pas l’exemple d’application, les valeurs de `app_location`, de `api_location` et de `output_location` doivent être modifiées pour correspondre aux valeurs utilisées dans votre application.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    La valeur `azure_static_web_apps_api_token` est autogérée et est configurée manuellement.

5. Sélectionnez **Variables**.

6. Sélectionnez **Nouvelle variable**.

7. Nommez la variable **deployment_token** (en utilisant le nom spécifié dans le workflow).

8. Copiez le jeton de déploiement que vous avez précédemment collé dans un éditeur de texte.

9. Collez le jeton de déploiement dans la zone _Valeur_.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Jeton de variable":::

10. Sélectionnez **Garder cette valeur secrète**.

11. Sélectionnez **OK**.

12. Sélectionnez **Enregistrer** pour revenir au YAML de votre pipeline.

13. Sélectionnez **Enregistrer et exécuter** pour ouvrir la boîte de dialogue _Enregistrer et exécuter_.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

14. Sélectionnez **Enregistrer et exécuter** pour exécuter le pipeline.

15. Une fois le déploiement réussi, accédez à la **Vue d’ensemble** d’Azure Static Web Apps, qui comprend des liens vers la configuration du déploiement. Notez que le lien _Source_ pointe maintenant vers la branche et l’emplacement du dépôt Azure DevOps.

16. Sélectionnez l’**URL** pour voir le site web nouvellement déployé.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Emplacement du déploiement":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources que vous avez utilisé dans ce tutoriel.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer Azure Static Web Apps](./configuration.md)
