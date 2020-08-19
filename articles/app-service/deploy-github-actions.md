---
title: Configurer CI/CD avec GitHub Actions
description: Découvrez comment déployer votre code dans Azure App Service à partir d’un pipeline CI/CD avec GitHub Actions. Personnalisez les tâches de génération et exécutez des déploiements complexes.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 713f4228bc2ba968fc96668d4d5c568f33b7e786
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080281"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Déployer sur App Service à l’aide de GitHub Actions

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) vous donne la possibilité de créer un flux de travail de cycle de vie de développement logiciel automatisé. Avec les actions Azure App Service pour GitHub, vous pouvez automatiser votre flux de travail pour le déploiement sur [Azure App Service](overview.md) à l’aide de GitHub Actions.

> [!IMPORTANT]
> GitHub Actions est actuellement en version bêta. Vous devez d’abord [vous inscrire pour participer à la préversion](https://github.com/features/actions) avec votre compte GitHub.
> 

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres qui composent le workflow.

Pour un workflow Azure App Service, le fichier comporte trois sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | 1. Définissez un principal de service. <br /> 2. Créez un secret GitHub. |
|**Créer** | 1. Configurez l’environnement. <br /> 2. Créez l’application web. |
|**Déployer** | 1. Déployez l’application web. |

## <a name="generate-deployment-credentials"></a>Générer les informations d’identification du déploiement

# <a name="user-level-credentials"></a>[Informations d’identification au niveau de l’utilisateur](#tab/userlevel)

Vous pouvez créer un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) avec la commande [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dans [Azure CLI](https://docs.microsoft.com/cli/azure/). Vous pouvez exécuter cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

Dans l’exemple ci-dessus, remplacez les espaces réservés par votre ID d’abonnement, le nom de votre groupe de ressources et le nom de votre application. La sortie correspond à un objet JSON avec les informations d’identification de l’attribution de rôle qui fournit l’accès à votre application App Service, similaire à ce qui suit. Copiez cet objet JSON pour une version ultérieure.

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

# <a name="app-level-credentials"></a>[Informations d’identification au niveau de l’application](#tab/applevel)

Vous pouvez utiliser les informations d’identification au niveau de l’application à l’aide du profil de publication de votre application. Accédez à la page de gestion de votre application dans le portail. Dans la page **Vue d’ensemble**, cliquez sur l’option **Obtenir le profil de publication**.

Vous aurez besoin du contenu du fichier ultérieurement.

---

## <a name="configure-the-github-secret"></a>Configurer le secret GitHub

# <a name="user-level-credentials"></a>[Informations d’identification au niveau de l’utilisateur](#tab/userlevel)

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Pour utiliser les [informations d’identification au niveau de l’utilisateur](#generate-deployment-credentials), collez l’intégralité de la sortie JSON à partir de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret comme `AZURE_CREDENTIALS`.

Quand vous configurez le fichier de flux de travail ultérieurement, vous utilisez le secret pour l’entrée `creds` de l’action de connexion Azure. Par exemple :

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Informations d’identification au niveau de l’application](#tab/applevel)

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Pour utiliser les [informations d’identification au niveau de l’application](#generate-deployment-credentials), collez le contenu du fichier de profil de publication téléchargé dans le champ de valeur du secret. Nommez le secret comme `azureWebAppPublishProfile`.

Quand vous configurez le fichier de flux de travail ultérieurement, vous utilisez le secret pour l’entrée `publish-profile` de l’action Déployer Azure Web App. Par exemple :
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>Configurer l’environnement

La configuration de l’environnement peut être effectuée à l’aide de l’une des actions de configuration.

|**Langage**  |**Action de configuration**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Les exemples suivants illustrent la partie du workflow qui configure l’environnement pour les différents langages pris en charge :

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Créer l’application Web

Cela dépend du langage et pour les langages pris en charge par Azure App Service, cette section doit correspondre aux étapes de génération standard de chaque langage.

Les exemples suivants illustrent la partie du workflow qui génère l’application web, dans les différents langages pris en charge.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Déployer dans App Service

Pour déployer votre code dans une application App Service, utilisez l’action `azure/webapps-deploy@v2`. Cette action a quatre paramètres :

| **Paramètre**  | **Explication**  |
|---------|---------|
| **app-name** | (Requis) Nom de l’application App Service | 
| **publish-profile** | (Facultatif) Publier le contenu du fichier de profil avec les secrets Web Deploy |
| **package** | (Facultatif) Chemin d’accès au package ou dossier. *.zip, *.war, *.jar ou un dossier à déployer |
| **slot-name** | (Facultatif) Entrer un emplacement existant autre que l’emplacement de production |

# <a name="user-level-credentials"></a>[Informations d’identification au niveau de l’utilisateur](#tab/userlevel)

Voici un exemple de flux de travail pour créer et déployer une application Node.js dans Azure à l’aide du principal de service Azure. Notez la manière dont l’entrée `creds` fait référence au secret `AZURE_CREDENTIALS` que vous avez créé précédemment.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

# <a name="app-level-credentials"></a>[Informations d’identification au niveau de l’application](#tab/applevel)

Voici un exemple de flux de travail pour créer et déployer une application Node.js dans Azure à l’aide du profil de publication de l’application. Notez la manière dont l’entrée `publish-profile` fait référence au secret `azureWebAppPublishProfile` que vous avez créé précédemment.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

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
