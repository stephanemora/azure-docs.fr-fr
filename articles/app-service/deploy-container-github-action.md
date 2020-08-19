---
title: Intégration continue/Déploiement continu d’un conteneur personnalisé à partir de GitHub Actions
description: Découvrez comment utiliser GitHub Actions pour déployer votre conteneur Linux personnalisé sur App Service à partir d’un pipeline CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 21019917f37ad95dc15056daa51b3d9e53ec06fa
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082803"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Déployer un conteneur personnalisé sur App Service à l’aide de GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) vous donne la possibilité de créer un flux de travail de cycle de vie de développement logiciel automatisé. Avec les [actions Azure App Service pour les conteneurs](https://github.com/Azure/webapps-container-deploy), vous pouvez automatiser votre flux de travail pour déployer des conteneurs personnalisés sur [App Service](overview.md) à l’aide de GitHub Actions.

> [!IMPORTANT]
> GitHub Actions est actuellement en version bêta. Vous devez d’abord [vous inscrire pour participer à la préversion](https://github.com/features/actions) avec votre compte GitHub.
> 

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres qui composent le workflow.

Pour un workflow de conteneur Azure App Service, le fichier comporte trois sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | 1. Définissez un principal de service. <br /> 2. Créez un secret GitHub. |
|**Créer** | 1. Configurez l’environnement. <br /> 2. Générez l’image de conteneur. |
|**Déployer** | 1. Déployez l’image conteneur. |

## <a name="create-a-service-principal"></a>Créer un principal du service

Vous pouvez créer un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) avec la commande [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dans [Azure CLI](https://docs.microsoft.com/cli/azure/). Vous pouvez exécuter cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

Dans l’exemple ci-dessus, remplacez les espaces réservés par votre ID d’abonnement et le nom de votre groupe de ressources. La sortie correspond à un objet JSON avec les informations d’identification de l’attribution de rôle qui fournit l’accès à votre application App Service, similaire à ce qui suit. Copiez cet objet JSON pour une version ultérieure.

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
> Il est toujours conseillé d’accorder un accès minimal. Vous pouvez limiter l’étendue dans la commande CLI Az ci-dessus à l’application App Service spécifique et au registre Azure Container Registry vers lequel les images de conteneur sont envoyées.

## <a name="configure-the-github-secret"></a>Configurer le secret GitHub

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Collez les contenus de la sortie JSON depuis [Créer un principal de service](#create-a-service-principal) comme valeur de la variable secrète. Nommez le secret comme `AZURE_CREDENTIALS`.

Quand vous configurez le fichier de flux de travail ultérieurement, vous utilisez le secret pour l’entrée `creds` de l’action de connexion Azure. Par exemple :

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

De même, définissez les secrets supplémentaires suivants associés aux informations d’identification du registre de conteneurs et configurez-les dans l’action de connexion Docker.

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>Générer l’image de conteneur

L’exemple suivant montre une partie du flux de travail qui génère l’image Docker.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Déployer sur un conteneur App Service

Pour déployer votre image sur un conteneur personnalisé dans App Service, utilisez l’action `azure/webapps-container-deploy@v1`. Cette action a cinq paramètres :

| **Paramètre**  | **Explication**  |
|---------|---------|
| **app-name** | (Requis) Nom de l’application App Service | 
| **slot-name** | (Facultatif) Entrer un emplacement existant autre que l’emplacement de production |
| **images** | (Requis) Spécifiez le nom complet de l’image ou des images conteneur. Par exemple, « myregistry.azurecr.io/nginx:latest » or « python:3.7.2-alpine/ ». Pour une application à plusieurs conteneurs, plusieurs noms d’images conteneur peuvent être fournis (séparés par plusieurs lignes) |
| **configuration-file** | (Facultatif) Chemin d’accès au fichier Docker-Compose. Doit être un chemin d’accès complet ou relatif au répertoire de travail par défaut. Requis pour les applications à plusieurs conteneurs. |
| **container-command** | (Facultatif) Entrez la commande de démarrage. Par exemple, dotnet run ou dotnet filename.dll |

Voici un exemple de flux de travail pour créer et déployer une application Node.js sur un conteneur personnalisé dans App Service. Notez la manière dont l’entrée `creds` fait référence au secret `AZURE_CREDENTIALS` que vous avez créé précédemment.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez trouver notre ensemble d’Actions regroupées dans différents référentiels GitHub, chacun contenant de la documentation et des exemples pour vous aider à utiliser GitHub pour les opérations de CI/CD et à déployer vos applications sur Azure.

- [Flux de travail d’actions à déployer sur Azure](https://github.com/Azure/actions-workflow-samples)

- [Connexion à Azure](https://github.com/Azure/login)

- [Azure Web App](https://github.com/Azure/webapps-deploy)

- [Azure Web App pour conteneurs](https://github.com/Azure/webapps-container-deploy)

- [Connexion/déconnexion de Docker](https://github.com/Azure/docker-login)

- [Événements qui déclenchent des flux de travail](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Déployer K8](https://github.com/Azure/k8s-deploy)

- [Flux de travail de démarrage](https://github.com/actions/starter-workflows)
