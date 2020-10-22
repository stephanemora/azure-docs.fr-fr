---
title: Intégration continue/Déploiement continu d’un conteneur personnalisé à partir de GitHub Actions
description: Découvrez comment utiliser GitHub Actions pour déployer votre conteneur Linux personnalisé sur App Service à partir d’un pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 3a5e319115c124551c05f2ac5aa393ba19596d0d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893354"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Déployer un conteneur personnalisé sur App Service à l’aide de GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) vous donne la possibilité de créer un workflow de développement logiciel automatisé. Grâce à l’[action Azure Web Deploy](https://github.com/Azure/webapps-deploy), vous pouvez automatiser votre workflow pour déployer des conteneurs personnalisés sur [App Service](overview.md) à l’aide de GitHub Actions.

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres définissant le workflow.

Pour un workflow de conteneur Azure App Service, le fichier comporte trois sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | 1. Récupérez un principal de service ou un profil de publication. <br /> 2. Créez un secret GitHub. |
|**Créer** | 1. Créez l’environnement. <br /> 2. Générez l’image de conteneur. |
|**Déployer** | 1. Déployez l’image conteneur. |

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un compte GitHub. Si vous n’en avez pas, inscrivez-vous [gratuitement](https://github.com/join).  
- Un registre de conteneurs fonctionnel et l’application Azure App Service pour les conteneurs. Cet exemple utilise Azure Container Registry. 
    - [Découvrez comment créer une application Node.js conteneurisée avec Docker, envoyer (push) l’image conteneur à un registre, puis déployer l’image sur Azure App Service.](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Générer les informations d’identification du déploiement

La méthode recommandée pour l’authentification auprès d’Azure App Service pour GitHub Actions consiste à utiliser un profil de publication. Vous pouvez également vous authentifier avec un principal de service, mais le processus requiert des étapes supplémentaires. 

Enregistrez les informations d’identification de votre profil de publication ou le principal du service en tant que [secret GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) pour vous authentifier auprès d’Azure. Vous allez accéder au secret dans votre workflow. 

# <a name="publish-profile"></a>[Profil de publication](#tab/publish-profile)

Un profil de publication est une information d’identification au niveau de l’application. Configurez votre profil de publication en tant que secret GitHub. 

1. Accédez à votre service d’application dans le portail Azure. 

1. Dans la page **Vue d’ensemble**, sélectionnez **Obtenir le profil de publication**.

1. Enregistrez le fichier téléchargé. Vous utiliserez le contenu du fichier pour créer un secret GitHub.

# <a name="service-principal"></a>[Principal du service](#tab/service-principal)

Vous pouvez créer un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dans [Azure CLI](/cli/azure/). Exécutez cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Dans l’exemple, remplacez les espaces réservés par votre ID d’abonnement, le nom de votre groupe de ressources et le nom de votre application. La sortie correspond à un objet JSON avec les informations d’identification de l’attribution de rôle qui fournit l’accès à votre application App Service. Copiez cet objet JSON pour une version ultérieure.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Il est toujours conseillé d’accorder un accès minimal. L’étendue dans l’exemple précédent est limitée à l’application App Service spécifique, et non à l’ensemble du groupe de ressources.

---

## <a name="configure-the-github-secret"></a>Configurer le secret GitHub

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Collez le contenu de la sortie JSON comme valeur de la variable secrète. Nommez le secret comme `AZURE_CREDENTIALS`.

Quand vous configurez le fichier de flux de travail ultérieurement, vous utilisez le secret pour l’entrée `creds` de l’action de connexion Azure. Par exemple :

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Configurer le secret GitHub pour l’authentification

# <a name="publish-profile"></a>[Profil de publication](#tab/publish-profile)

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Pour utiliser les [informations d’identification au niveau de l’application](#generate-deployment-credentials), collez le contenu du fichier de profil de publication téléchargé dans le champ de valeur du secret. Nommez le secret `AZURE_WEBAPP_PUBLISH_PROFILE`.

Quand vous configurez votre workflow GitHub, vous utilisez `AZURE_WEBAPP_PUBLISH_PROFILE` dans l’action Déployer l’application web Azure. Exemple :
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Principal du service](#tab/service-principal)

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Pour utiliser les [informations d’identification au niveau de l’utilisateur](#generate-deployment-credentials), collez l’intégralité de la sortie JSON à partir de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret comme `AZURE_CREDENTIALS`.

Quand vous configurez le fichier de flux de travail ultérieurement, vous utilisez le secret pour l’entrée `creds` de l’action de connexion Azure. Par exemple :

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Configurer des secrets GitHub pour votre registre

Définissez les secrets à utiliser grâce à l’action Docker Login. 

1. Accédez à votre conteneur dans le portail Azure ou dans Docker et copiez le nom d’utilisateur et le mot de passe. 

2. Définissez un nouveau secret pour le nom d’utilisateur du registre nommé `REGISTRY_USERNAME`. 

3. Définissez un nouveau secret pour le mot de passe du registre nommé `REGISTRY_PASSWORD`. 

## <a name="build-the-container-image"></a>Générer l’image de conteneur

L’exemple suivant montre une partie du workflow qui génère une image Docker Node.js. Utilisez [Docker Login](https://github.com/azure/docker-login) pour vous connecter à un registre de conteneurs privé. Cet exemple utilise Azure Container Registry, mais la même action fonctionne pour les autres registres. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Vous pouvez également utiliser [Docker Login](https://github.com/azure/docker-login) pour vous connecter à plusieurs registres de conteneurs en même temps. Cet exemple comprend deux nouveaux secrets GitHub pour l’authentification avec docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Déployer sur un conteneur App Service

Pour déployer votre image sur un conteneur personnalisé dans App Service, utilisez l’action `azure/webapps-deploy@v2`. Cette action a cinq paramètres :

| **Paramètre**  | **Explication**  |
|---------|---------|
| **app-name** | (Requis) Nom de l’application App Service | 
| **publish-profile** | (Facultatif) Publier le contenu du fichier de profil avec les secrets Web Deploy |
| **images** | Nom complet des images conteneur. Par exemple, « myregistry.azurecr.io/nginx:latest » or « python:3.7.2-alpine/ ». Pour un scénario à plusieurs conteneurs, plusieurs noms d’images conteneur peuvent être fournis (séparés en plusieurs lignes) |
| **slot-name** | (Facultatif) Entrer un emplacement existant autre que l’emplacement de production |
| **configuration-file** | (Facultatif) Chemin d’accès au fichier Docker-Compose |

# <a name="publish-profile"></a>[Profil de publication](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Principal du service](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez trouver notre ensemble d’Actions regroupées dans différents référentiels GitHub, chacun contenant de la documentation et des exemples pour vous aider à utiliser GitHub pour les opérations de CI/CD et à déployer vos applications sur Azure.

- [Flux de travail d’actions à déployer sur Azure](https://github.com/Azure/actions-workflow-samples)

- [Connexion à Azure](https://github.com/Azure/login)

- [Azure Web App](https://github.com/Azure/webapps-deploy)

- [Connexion/déconnexion de Docker](https://github.com/Azure/docker-login)

- [Événements qui déclenchent des flux de travail](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Déployer K8](https://github.com/Azure/k8s-deploy)

- [Flux de travail de démarrage](https://github.com/actions/starter-workflows)