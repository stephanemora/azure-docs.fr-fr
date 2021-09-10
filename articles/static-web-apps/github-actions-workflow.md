---
title: Flux de travail GitHub Actions pour Azure Static Web Apps
description: Découvrez comment utiliser les référentiels GitHub pour configurer le déploiement continu sur Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9dc0c8a83279e3a70bceebb316536485e337c873
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531710"
---
# <a name="github-actions-workflows-for-azure-static-web-apps"></a>Flux de travail GitHub Actions pour Azure Static Web Apps

Lorsque vous utilisez le déploiement GitHub, un fichier YAML contrôle le flux de travail de génération de votre site. Cet article explique la structure et les options de ce fichier.

Un déploiement commence par un [déclencheur](#triggers), qui exécute des [travaux](#jobs) composés d’[étapes](#steps) individuelles.

> [!NOTE]
> Azure Static Web Apps prend également en charge les flux de travail Azure DevOps. Pour plus d’informations sur la configuration d’un pipeline, consultez la page [Publier avec Azure DevOps](publish-devops.md).

## <a name="file-name-and-location"></a>Nom et emplacement de fichier

Lorsque vous liez votre référentiel, Azure Static Web Apps génère un fichier qui contrôle le flux de travail.

Procédez comme suit pour afficher le fichier de flux de travail.

1. Ouvrez le référentiel de l’application sur GitHub.
1. Sélectionnez l’onglet **Code**.
1. Sélectionnez le dossier **.github/workflows**.
1. Sélectionnez le fichier dont le nom est similaire à **azure-static-web-apps-<NOM_ALÉATOIRE>.yml**.

## <a name="triggers"></a>Déclencheurs

Un [déclencheur](https://help.github.com/actions/reference/events-that-trigger-workflows) GitHub Actions notifie à un flux de travail GitHub Actions d’exécuter un travail en fonction de certains événements. Les déclencheurs sont répertoriés à l’aide de la propriété `on` dans le fichier de flux de travail.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Dans cet exemple, un flux de travail commence lorsqu’une demande de tirage (pull request) sur la branche *primaire* est :

- ouvert
- synchronisée
- rouverte
- Fermé

Vous pouvez personnaliser cette partie du flux de travail pour cibler différentes branches ou différents événements.

## <a name="jobs"></a>travaux

Chaque déclencheur définit une série de [travaux](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) à exécuter en réponse à l’événement.

| Name | Description |
| --- | --- |
| `build_and_deploy_job` | S’exécute quand vous envoyez des validations ou que vous ouvrez une demande de tirage (pull request) sur la branche indiquée dans la propriété `on`.          |
| `close_pull_request_job` | S’exécute UNIQUEMENT lorsque vous fermez une demande de tirage (pull request) qui supprime l’environnement intermédiaire créé à partir des demandes de tirage (pull requests). |

## <a name="steps"></a>Étapes

Les étapes sont les tâches séquentielles des travaux. Chaque étape effectue des actions telles que l’installation de dépendances, l’exécution de tests et le déploiement de votre application en production.

| Travail | Étapes |
| --- | --- |
| `build_and_deploy_job` | <li>Extrait le référentiel dans l’environnement de GitHub Actions.<li>Compile et déploie le référentiel sur Azure Static Web Apps. |
| `close_pull_request_job` | <li>Indique à Azure Static Web Apps qu’une demande de tirage (pull request) a été fermée. |

## <a name="build-and-deploy"></a>Générer et déployer

L’étape `build_and_deploy_job` compile et déploie votre site sur Azure Static Web Apps. Dans la section `with`, vous pouvez personnaliser les valeurs suivantes pour votre déploiement.

L’exemple suivant montre comment ces valeurs s’affichent dans un fichier de flux de travail.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

Ne modifiez pas les valeurs de `repo_token`, `action` et `azure_static_web_apps_api_token`, car elles sont définies pour vous par Azure Static Web Apps.

## <a name="custom-build-commands"></a>Commandes de compilation personnalisées

Vous pouvez contrôler avec précision les commandes exécutées pendant le processus de génération d’une application ou d’une API. Les commandes suivantes apparaissent dans la section `with` d’un travail.

| Commande | Description |
| --- |--- |
| `app_build_command` | Définit une commande personnalisée pour générer l’application de contenu statique.<br><br>Par exemple, pour configurer une build de production pour une application Angular, créez un script NPM nommé `build-prod` pour exécuter `ng build --prod` et entrez `npm run build-prod` comme commande personnalisée. Si elle n’est pas renseignée, le flux de travail tente d’exécuter les commandes `npm run build` ou `npm run build:azure`. |
| `api_build_command` | Définit une commande personnalisée pour générer l’application API Azure Functions. |

L’exemple suivant montre comment définir des commandes de génération personnalisées dans la section `with` d’un travail.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  ###### End of Repository/Build Configurations ######
```

> [!NOTE]
> Actuellement, vous pouvez uniquement définir des commandes de génération personnalisées pour les builds Node.js. Le processus de génération appelle toujours `npm install` avant toute commande personnalisée.

## <a name="skip-building-front-end-app"></a>Ignorer la génération de l’application frontale

Si vous avez besoin d’un contrôle total sur la build de votre application frontale, vous pouvez ajouter des étapes personnalisées au flux de travail. Par exemple, vous pouvez choisir d’ignorer la génération automatique et de déployer l’application générée à l’étape précédente.

Pour ignorer la génération de l’application frontale, définissez `skip_app_build` sur `true` et définissez `app_location` sur l’emplacement du dossier à déployer.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Propriété         | Description                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Affectez la valeur `true` pour ignorer la génération de l’application front-end. |

> [!NOTE]
> Vous ne pouvez ignorer la génération que pour l’application front-end. L’API est toujours générée si elle existe.

## <a name="environment-variables"></a>Variables d'environnement

Vous pouvez définir des variables d’environnement pour votre build via la section `env` de la configuration d’un travail.

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
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Prise en charge de référentiel unique

Un référentiel unique est un référentiel qui contient du code pour plusieurs applications. Par défaut, le flux de travail suit tous les fichiers d’un référentiel, mais vous pouvez ajuster la configuration pour cibler une seule application.

Lorsque vous configurez un référentiel unique, chaque application statique dispose de son propre fichier config qui ne cible que les fichiers d’une seule application. Les différents fichiers de flux de travail sont placés côte à côte dans le dossier _.github/workflows_ du référentiel.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Pour cibler un fichier de flux de travail sur une seule application, vous spécifiez les chemins d’accès dans les sections `push` et `pull_request`.

L’exemple suivant montre comment ajouter un nœud `paths` aux sections `push` et `pull_request` d’un fichier nommé _azure-static-web-apps-purple-pond.yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

Dans ce cas, seules les modifications apportées aux fichiers suivants déclenchent une nouvelle génération :

- Tous les fichiers contenus dans le dossier _app1_
- Tous les fichiers contenus dans le dossier _api1_
- Modifications apportées au fichier de flux de travail _azure-static-web-apps-purple-pond.yml_ de l’application

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en revue les demandes de tirage (pull request) dans les environnements de préproduction](review-publish-pull-requests.md)
