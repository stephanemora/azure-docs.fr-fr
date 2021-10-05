---
title: Configuration de build pour Azure Static Web Apps
description: Découvrez comment contrôler le processus de génération pour Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 54b6231c98ce244913062010782591493ea45a5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838522"
---
# <a name="build-configuration-for-azure-static-web-apps"></a>Configuration de build pour Azure Static Web Apps

La configuration de build Azure Static Web Apps est optimisée par Azure Pipelines ou GitHub Actions.  Chaque site dispose d’un fichier de configuration YAML qui contrôle le mode de génération et de déploiement d’un site. Cet article explique la structure et les options de ce fichier.

Le tableau suivant répertorie les paramètres de configuration disponibles.

| Propriété | Description | Obligatoire |
|---|---|---|
| `app_location` | Ce dossier contient le code source pour votre application frontale. La valeur est relative par rapport à la racine du dépôt dans GitHub et au dossier de travail actuel dans Azure DevOps. | Oui |
| `api_location` | Ce dossier contenant le code source pour votre application d’API. La valeur est relative par rapport à la racine du dépôt dans GitHub et au dossier de travail actuel dans Azure DevOps. | Non |
| `output_location` | Si votre application web exécute une étape de génération, l’emplacement de sortie est le dossier dans lequel les fichiers publics sont générés. Pour la plupart des projets, `output_location` est relatif par rapport à `app_location`. En revanche, pour les projets .NET, l’emplacement est relatif par rapport au dossier de sortie de publication. | Non |
| `app_build_command` | Pour les applications Node.js, vous pouvez définir une commande personnalisée pour générer l’application de contenu statique.<br><br>Par exemple, pour configurer une build de production pour une application Angular, créez un script NPM nommé `build-prod` pour exécuter `ng build --prod` et entrez `npm run build-prod` comme commande personnalisée. Si elle n’est pas renseignée, le flux de travail tente d’exécuter les commandes `npm run build` ou `npm run build:azure`. | Non |
| `api_build_command` | Pour les applications Node.js, vous pouvez définir une commande personnalisée pour générer l’application d’API Azure Functions. | Non |
| `skip_app_build` | Affectez la valeur `true` pour ignorer la génération de l’application front-end. | Non |

Avec ces paramètres, vous pouvez configurer des GitHub Actions ou [Azure Pipelines](publish-devops.md) pour exécuter l’intégration continue/la livraison continue (CI/CD) pour votre application web statique.

> [!NOTE]
> Actuellement, vous pouvez uniquement définir `app_build_command` et `api_build_command` pour les builds Node.js.

## <a name="file-name-and-location"></a>Nom et emplacement de fichier

# <a name="github-actions"></a>[Actions GitHub](#tab/github-actions)

Le fichier de configuration est généré par GitHub et stocké dans le dossier *github/workflows*, nommé en utilisant le format suivant : `azure-static-web-apps-<RANDOM_NAME>.yml`.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Par défaut, le fichier de configuration est stocké à la racine de votre dépôt, sous le nom `azure-pipelines.yml`.

---

## <a name="build-configuration"></a>Configuration de build

L’exemple de configuration suivant surveille les modifications du dépôt. Les validations étant envoyées à la branche `main`, l’application est générée à partir du dossier `app_location` et les fichiers dans `output_location` sont servis au web public. En outre, l’application dans le dossier *api* est disponible dans le chemin d’accès `api` du site.

# <a name="github-actions"></a>[Actions GitHub](#tab/github-actions)

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

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
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations ######
          app_location: "src" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

Dans cette configuration :

- La branche `main` est surveillée pour les validations.
- Un flux de travail GitHub Actions est [déclenché](https://help.github.com/actions/reference/events-that-trigger-workflows) quand une demande de tirage (pull request) sur la branche `main` est ouverte, synchronisée, rouverte ou fermée.
- La commande `build_and_deploy_job` s’exécute quand vous envoyez des validations ou ouvrez une demande de tirage (pull request) sur la branche indiquée dans la propriété `on`.
- `app_location` pointe vers le dossier `src` contenant les fichiers sources pour l’application web.
- `api_location` pointe vers le dossier `api`contenant l’application Azure Functions pour les points de terminaison d’API du site.
- `output_location` pointe vers le dossier `public` contenant la version finale des fichiers sources de l’application.

Ne modifiez pas les valeurs de `repo_token`, `action` et `azure_static_web_apps_api_token`, car elles sont définies pour vous par Azure Static Web Apps.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

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
      app_location: 'src' # App source code path
      api_location: 'api' # Api source code path - optional
      output_location: 'public' # Built app content directory - optional
      azure_static_web_apps_api_token: $(deployment_token)
```

Dans cette configuration :

- La branche `main` est surveillée pour les validations.
- `app_location` pointe vers le dossier `src` contenant les fichiers sources pour l’application web.
- `api_location` pointe vers le dossier `api`contenant l’application Azure Functions pour les points de terminaison d’API du site.
- `output_location` pointe vers le dossier `public` contenant la version finale des fichiers sources de l’application.
- La variable `$(deployment_token)` pointe vers le [jeton de déploiement Azure DevOps généré](./publish-devops.md).

---

## <a name="custom-build-commands"></a>Commandes de compilation personnalisées

Pour les applications Node.js, vous pouvez contrôler avec précision les commandes exécutées pendant le processus de génération d’une application ou d’une API. L’exemple suivant montre comment définir la génération avec des valeurs pour `app_build_command` et `api_build_command`.

# <a name="github-actions"></a>[Actions GitHub](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: '/'
  api_location: 'api'
  output_location: 'dist'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
```
# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
...

inputs:
  app_location: 'src'
  api_location: 'api'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  output_location: 'public'
  azure_static_web_apps_api_token: $(deployment_token)
```

---

## <a name="skip-building-front-end-app"></a>Ignorer la génération de l’application frontale

Si vous avez besoin d’un contrôle total de la génération pour votre application frontale, vous pouvez contourner la génération automatique et déployer l’application générée à l’étape précédente.

Pour ignorer la génération de l’application frontale :

- Définissez `app_location` sur l’emplacement des fichiers que vous souhaitez déployer.
- Définissez `skip_app_build` sur `true`.
- Définissez `output_location` sur une chaîne vide (`''`).

# <a name="github-actions"></a>[Actions GitHub](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src/dist'
  api_location: 'api'
  output_location: ''
  skip_app_build: true
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yml
...

inputs:
  app_location: 'src/dist'
  api_location: 'api'
  output_location: '' # Leave this empty
  skip_app_build: true
  azure_static_web_apps_api_token: $(deployment_token)
```

---

> [!NOTE]
> Vous ne pouvez ignorer la génération que pour l’application front-end. L’API est toujours générée si elle existe.

## <a name="environment-variables"></a>Variables d'environnement

Vous pouvez définir des variables d’environnement pour votre build via la section `env` de la configuration d’un travail.

# <a name="github-actions"></a>[Actions GitHub](#tab/github-actions)

```yaml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src'
  api_location: 'api'
  output_location: 'public'

env: # Add environment variables here
  HUGO_VERSION: 0.58.0
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Azure DevOps ne prend actuellement pas en charge les variables d’environnement.

---

## <a name="monorepo-support"></a>Prise en charge de référentiel unique

Un référentiel unique est un référentiel qui contient du code pour plusieurs applications. Par défaut, le flux de travail suit tous les fichiers d’un référentiel, mais vous pouvez ajuster la configuration pour cibler une seule application.

Pour cibler un fichier de flux de travail sur une seule application, vous spécifiez les chemins d’accès dans les sections `push` et `pull_request`.

# <a name="github-actions"></a>[Actions GitHub](#tab/github-actions)

Lorsque vous configurez un référentiel unique, chaque application statique ne cible que les fichiers d’une seule application. Les différents fichiers de flux de travail sont placés côte à côte dans le dossier _.github/workflows_ du référentiel.

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

Dans cet exemple, seules les modifications apportées aux fichiers suivants déclenchent une nouvelle génération :

- Tous les fichiers contenus dans le dossier _app1_
- Tous les fichiers contenus dans le dossier _api1_
- Modifications apportées au fichier de flux de travail _azure-static-web-apps-purple-pond.yml_ de l’application

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Pour prendre en charge plusieurs applications dans un seul dépôt, créez un fichier de flux de travail distinct et associez-le à des pipelines Azure différents.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en revue les demandes de tirage (pull request) dans les environnements de préproduction](review-publish-pull-requests.md)
