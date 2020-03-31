---
title: Utiliser GitHub Actions pour effectuer des mises à jour de code dans Azure Functions
description: Découvrez comment utiliser GitHub Actions pour définir un workflow afin de générer et déployer des projets Azure Functions dans GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: dd74fd5c38e5a8800d2092afc1db1b412b126861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649906"
---
# <a name="continuous-delivery-by-using-github-action"></a>Livraison continue à l’aide de GitHub Actions

[GitHub Actions](https://github.com/features/actions) vous permet de définir un workflow pour générer et déployer automatiquement votre code sur votre application de fonction dans Azure. 

Dans GitHub Actions, un [workflow](https://help.github.com/articles/about-github-actions#workflow) est un processus automatisé que vous définissez dans votre dépôt GitHub. Ce processus indique à GitHub comment générer et déployer votre projet d’application de fonction sur GitHub. 

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres qui composent le workflow. 

Pour un workflow Azure Functions, le fichier comporte trois sections : 

| Section | Tâches |
| ------- | ----- |
| **Authentification** | <ol><li>Définissez un principal de service.</li><li>Téléchargez le profil de publication.</li><li>Créez un secret GitHub.</li></ol>|
| **Créer** | <ol><li>Configurez l’environnement.</li><li>Créez l’application de fonction.</li></ol> |
| **Déployer** | <ol><li>Déployez l’application de fonction.</li></ol>|

> [!NOTE]
> Vous n’avez pas besoin de créer un principal de service si vous décidez d’utiliser le profil de publication à des fins d’authentification.

## <a name="create-a-service-principal"></a>Créer un principal du service

Vous pouvez créer un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dans [Azure CLI](/cli/azure/). Vous pouvez exécuter cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Dans cet exemple, remplacez les espaces réservés dans la ressource par votre ID d’abonnement, votre groupe de ressources et le nom de votre application de fonction. La sortie correspond aux informations d’identification de l’attribution de rôle qui fournit l’accès à votre application de fonction. Copiez cet objet JSON, que vous pouvez utiliser pour vous authentifier à partir de GitHub.

> [!IMPORTANT]
> Il est toujours conseillé d’accorder un accès minimal. C’est pourquoi l’étendue dans l’exemple précédent est limitée à l’application de fonction spécifique, et non à l’ensemble du groupe de ressources.

## <a name="download-the-publishing-profile"></a>Télécharger le profil de publication

Vous pouvez télécharger le profil de publication de votre application de fonction en accédant à la page **Vue d’ensemble** de votre application, puis en cliquant sur **Obtenir le profil de publication**.

   ![Télécharger le profil de publication](media/functions-how-to-github-actions/get-publish-profile.png)

Copiez le contenu du fichier.

## <a name="configure-the-github-secret"></a>Configurer le secret GitHub

1. Dans [GitHub](https://github.com), accédez à votre dépôt, sélectionnez **Paramètres** > **Secrets** > **Ajouter un nouveau secret**.

   ![Ajouter un secret](media/functions-how-to-github-actions/add-secret.png)

1. Ajoutez un nouveau secret.

   * Si vous utilisez le principal de service que vous avez créé à l’aide de l’interface de ligne de commande Azure, utilisez `AZURE_CREDENTIALS` pour **Nom**. Collez ensuite la sortie de l’objet JSON copié pour **Valeur**, puis sélectionnez **Ajouter un secret**.
   * Si vous utilisez un profil de publication, utilisez `SCM_CREDENTIALS` pour **Nom**. Utilisez ensuite le contenu du fichier du profil de publication pour **Valeur**, puis sélectionnez **Ajouter un secret**.

GitHub peut à présent s’authentifier auprès de votre application de fonction dans Azure.

## <a name="set-up-the-environment"></a>Configurer l’environnement 

La configuration de l’environnement s’effectue à l’aide d’une action de configuration de publication spécifique à une langue.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant illustre la partie du flux de travail qui utilise l’action `actions/setup-node` pour configurer l’environnement :

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant illustre la partie du flux de travail qui utilise l’action `actions/setup-python` pour configurer l’environnement :

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

L’exemple suivant illustre la partie du flux de travail qui utilise l’action `actions/setup-dotnet` pour configurer l’environnement :

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

L’exemple suivant illustre la partie du flux de travail qui utilise l’action `actions/setup-java` pour configurer l’environnement :

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Générer l’application de fonction

Cela dépend du langage et pour les langages pris en charge par Azure Functions, cette section doit correspondre aux étapes de génération standard de chaque langage.

L’exemple suivant illustre la partie du flux de travail qui génère l’application de fonction, qui est propre à la langue :

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Déployer l’application de fonction

Pour déployer votre code sur une application de fonction, vous avez besoin d’utiliser l’action `Azure/functions-action`. Cette action a deux paramètres :

|Paramètre |Explication  |
|---------|---------|
|**_app-name_** | (Obligatoire) Nom de votre application de fonction. |
|_**slot-name**_ | (Facultatif) Nom de l’[emplacement de déploiement](functions-deployment-slots.md) sur lequel vous voulez effectuer le déploiement. L’emplacement doit déjà être défini dans votre application de fonction. |


L’exemple suivant utilise la version 1 de `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Étapes suivantes

Pour voir un fichier .yaml de workflow complet, consultez l’un des fichiers situés dans le [dépôt des exemples de workflow GitHub Actions](https://aka.ms/functions-actions-samples) qui contiennent `functionapp` dans leur nom. Vous pouvez utiliser ces exemples comme point de départ pour votre workflow.

> [!div class="nextstepaction"]
> [En savoir plus sur GitHub Actions](https://help.github.com/en/articles/about-github-actions)
