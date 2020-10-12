---
title: Utiliser GitHub Actions pour déployer un site statique sur Stockage Azure
description: Hébergement de site web statique dans Stockage Azure avec GitHub Actions
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure
ms.openlocfilehash: 919fa0d7b6dff0361e4439b442bcfe9648ed8677
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776389"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Configurer un workflow GitHub Actions pour déployer votre site web statique dans Stockage Azure

Commencez avec [GitHub Actions](https://docs.github.com/en/actions) en utilisant un workflow pour déployer un site statique sur un blob de stockage Azure. Une fois que vous avez configuré un workflow GitHub Actions, vous pouvez automatiquement déployer votre site vers Azure à partir de GitHub lorsque vous apportez des modifications au code de votre site. 

> [!NOTE]
> Si vous utilisez [Azure Static Web Apps](https://docs.microsoft.com/azure/static-web-apps/), vous n’avez pas besoin de configurer manuellement un workflow GitHub Actions.
> Azure Static Web Apps crée automatiquement un workflow GitHub pour vous. 

## <a name="prerequisites"></a>Prérequis

Un abonnement Azure et un compte GitHub. 

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un compte GitHub avec votre code de site web statique. Si vous n’avez pas de compte GitHub, [inscrivez-vous gratuitement](https://github.com/join).  
- Un site web statique opérationnel hébergé dans Stockage Azure. Découvrez comment [héberger un site web statique dans Stockage Azure](storage-blob-static-website-how-to.md). Votre site web statique doit inclure un [réseau de distribution de contenu](static-website-content-delivery-network.md).

## <a name="generate-deployment-credentials"></a>Générer les informations d’identification du déploiement

Vous pouvez créer un [principal de service](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dans [Azure CLI](/cli/azure/). Exécutez cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

Remplacez l’espace réservé `myStaticSite` par le nom de votre site hébergé dans Stockage Azure. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
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
> Il est toujours conseillé d’accorder un accès minimal. L’étendue dans l’exemple précédent est limitée à l’application App Service spécifique, et non à l’ensemble du groupe de ressources.

## <a name="configure-the-github-secret"></a>Configurer le secret GitHub

1. Dans [GitHub](https://github.com/), accédez à votre référentiel.

1. Sélectionnez **Paramètres > Secrets > Nouveau secret**.

1. Collez l’intégralité de la sortie JSON de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret comme `AZURE_CREDENTIALS`.

    Quand vous configurez le fichier de flux de travail ultérieurement, vous utilisez le secret pour l’entrée `creds` de l’action de connexion Azure. Par exemple :

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Ajouter votre workflow

1. Accédez à **Actions** pour votre référentiel GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Élément de menu GitHub Actions&quot;:::

1. Sélectionnez **Configurer vous-même un workflow**. 

1. Supprimez tous les éléments après la section `on:` de votre fichier de workflow. Par exemple, votre workflow restant peut ressembler à ce qui suit. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Renommez votre workflow `Blob storage website CI` et ajoutez les actions d’extraction et de connexion. Ces actions extraient votre code de site et vous authentifient auprès d’Azure à l’aide du secret GitHub `AZURE_CREDENTIALS` que vous avez créé précédemment. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Utilisez l’action Azure CLI pour charger votre code dans le stockage de blob et vider votre point de terminaison CDN. Pour `az storage blob upload-batch`, remplacez l’espace réservé par le nom de votre compte de stockage. Le script est chargé dans le conteneur `$web`. Pour `az cdn endpoint purge`, remplacez les espaces réservés par le nom de votre profil CDN, le nom de votre point de terminaison CDN et votre groupe de ressources.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Terminez votre workflow en ajoutant une action permettant de vous déconnecter d’Azure. Voici le workflow terminé. Le fichier apparaît dans le dossier `.github/workflows` de votre référentiel.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Vérifier votre déploiement

1. Accédez à **Actions** pour votre référentiel GitHub. 

1. Ouvrez le premier résultat pour afficher les journaux détaillés de l’exécution de votre workflow. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Élément de menu GitHub Actions&quot;:::

1. Sélectionnez **Configurer vous-même un workflow**. 

1. Supprimez tous les éléments après la section `on:` de votre fichier de workflow. Par exemple, votre workflow restant peut ressembler à ce qui suit. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Renommez votre workflow `Blob storage website CI` et ajoutez les actions d’extraction et de connexion. Ces actions extraient votre code de site et vous authentifient auprès d’Azure à l’aide du secret GitHub `AZURE_CREDENTIALS` que vous avez créé précédemment. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Utilisez l’action Azure CLI pour charger votre code dans le stockage de blob et vider votre point de terminaison CDN. Pour `az storage blob upload-batch`, remplacez l’espace réservé par le nom de votre compte de stockage. Le script est chargé dans le conteneur `$web`. Pour `az cdn endpoint purge`, remplacez les espaces réservés par le nom de votre profil CDN, le nom de votre point de terminaison CDN et votre groupe de ressources.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Terminez votre workflow en ajoutant une action permettant de vous déconnecter d’Azure. Voici le workflow terminé. Le fichier apparaît dans le dossier `.github/workflows` de votre référentiel.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque votre référentiel et votre site statique Azure ne sont plus nécessaires, nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources et votre référentiel GitHub. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur Azure Static Web Apps](https://docs.microsoft.com/azure/static-web-apps/)