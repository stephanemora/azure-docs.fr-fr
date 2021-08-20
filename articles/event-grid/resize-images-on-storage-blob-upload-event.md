---
title: 'Tutoriel : Utiliser Azure Event Grid pour automatiser le redimensionnement des images chargées'
description: 'Tutoriel : Azure Event Grid peut être déclenché en cas de chargement d’objets blob dans le stockage Azure. Vous pouvez utiliser cette fonctionnalité pour envoyer des fichiers image chargés dans le stockage Azure vers d’autres services, tels qu’Azure Functions, en vue de les redimensionner ou de leur apporter d’autres améliorations.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: 21fea4c248cebe94bc237b8bc0256ed3e94c08ab
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492754"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Tutoriel : Automatiser le redimensionnement des images chargées à l’aide d’Event Grid

[Azure Event Grid](overview.md) est un service d’événement pour le cloud. Event Grid permet de créer des abonnements aux événements qui sont déclenchés par les services Azure ou des ressources tierces.  

Ce didacticiel constitue la deuxième partie d’une série de didacticiels sur le stockage. Il développe le [tutoriel précédent sur le stockage][previous-tutorial] en y ajoutant la génération automatique de miniatures serverless à l’aide d’Azure Event Grid et d’Azure Functions. Event Grid permet à [Azure Functions](../azure-functions/functions-overview.md) de répondre aux événements de [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md) et de générer les miniatures d’images chargées. Un abonnement est créé pour l’événement de création de Stockage Blob. Lorsqu’un objet blob est ajouté à un conteneur de stockage d’objets blob, un point de terminaison de fonction est appelé. Les données passées à la liaison de fonction à partir d’Event Grid sont utilisées pour accéder à l’objet blob et pour générer l’image miniature.

Pour ajouter la fonctionnalité de redimensionnement à une application existante de chargement d’images, utilisez Azure CLI et le portail Azure.

# <a name="net-v12-sdk"></a>[\.SDK .NET v12](#tab/dotnet)

![Capture d’écran montrant une application web publiée dans un navigateur pour le SDK \.NET v12.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[SDK Node.js v10](#tab/nodejsv10)

![Capture d’écran montrant une application web publiée dans un navigateur pour le SDK \.NET v10.](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Création d'un compte Azure Storage
> * Déployer du code sans serveur à l’aide d’Azure Functions
> * Créer un abonnement d’événement Stockage Blob dans Event Grid

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour suivre ce tutoriel :

- Vous avez besoin d’un [abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Ce tutoriel ne fonctionne pas avec l’**abonnement** gratuit. 
- Vous devez avoir terminé le tutoriel précédent sur le stockage d'objets blob : [Charger des données d’image dans le cloud avec Stockage Azure][previous-tutorial].  

## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage
Azure Functions nécessite un compte de stockage général. En plus du compte de stockage d’objets blob que vous avez créé dans le tutoriel précédent, créez un compte de stockage général dans le groupe de ressources. Les noms de compte de stockage doivent avoir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.

Définissez des variables pour stocker le nom du groupe de ressources que vous avez créé dans le tutoriel précédent, ainsi que l’emplacement des ressources à créer et le nom du nouveau compte de stockage requis par Azure Functions. Créez ensuite le compte de stockage pour la fonction Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez la commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

1. Spécifiez un nom pour le groupe de ressources. 

    ```azurepowershell-interactive
    $resourceGroupName="myResourceGroup"
    ```
2. Spécifiez l’emplacement pour le compte de stockage.

    ```azurepowershell-interactive
    $location="eastus"    
    ```
3. Spécifiez le nom du compte de stockage que la fonction doit utiliser.

    ```azurepowershell-interactive
    $functionstorage="<name of the storage account to be used by the function>"    
    ```
4. Créez un compte de stockage. 

    ```azurepowershell-interactive
    New-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $functionstorage -Location $location -SkuName Standard_LRS -Kind StorageV2        
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az storage account create](/cli/azure/storage/account).

> [!NOTE]
> Utilisez les commandes suivantes dans l’interpréteur de commandes Bash de Cloud Shell. Utilisez la liste déroulante en haut à gauche de Cloud Shell pour basculer vers l’interpréteur de commandes Bash le cas échéant. 

1. Spécifiez un nom pour le groupe de ressources. 

    ```azurecli-interactive
    resourceGroupName="myResourceGroup"    
    ```
2. Spécifiez l’emplacement pour le compte de stockage.

    ```azurecli-interactive
    location="eastus"
    ```
3. Spécifiez le nom du compte de stockage que la fonction doit utiliser.

    ```azurecli-interactive
    functionstorage="<name of the storage account to be used by the function>"
    ```
4. Créez un compte de stockage. 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location $location --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

---

## <a name="create-a-function-app"></a>Créer une application de fonction  

Vous devez disposer d’une application de fonction pour héberger l’exécution de votre fonction. La Function App fournit un environnement d’exécution sans serveur de votre code de fonction.

Dans la commande suivante, indiquez le nom de votre application de fonction unique. Le nom de l’application de fonction est utilisé en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure.

Spécifiez un nom pour l’application de fonction qui doit être créée, puis créez la fonction Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Créez une application de fonction à l’aide de la commande [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp).

1. Spécifiez un nom pour l’application de fonction. 

    ```azurepowershell-interactive
    $functionapp="<name of the function app>"    
    ```
2. Créer une application de fonction. 

    ```azurepowershell-interactive
    New-AzFunctionApp -Location $location -Name $functionapp -ResourceGroupName $resourceGroupName -Runtime PowerShell -StorageAccountName $functionstorage    
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Créez une Function App à l’aide de la commande [az functionapp create](/cli/azure/functionapp).

1. Spécifiez un nom pour l’application de fonction. 

    ```azurecli-interactive
    functionapp="<name of the function app>"
    ```
2. Créer une application de fonction. 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage --resource-group $resourceGroupName --consumption-plan-location $location --functions-version 2    
    ```

---

Maintenant, configurez l’application de fonction pour vous connecter au compte de stockage Blob que vous avez créé dans le [tutoriel précédent][previous-tutorial].

## <a name="configure-the-function-app"></a>Configurer l’application de fonction

La fonction nécessite des informations d’identification pour le compte de stockage blob, qui sont ajoutées aux paramètres d’application de l’application de fonction à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) ou [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting).

# <a name="net-v12-sdk"></a>[\.SDK .NET v12](#tab/dotnet)

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```azurepowershell-interactive
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

Update-AzFunctionAppSetting -Name $functionapp -ResourceGroupName $resourceGroupName -AppSetting AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[SDK Node.js v10](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Le paramètre `FUNCTIONS_EXTENSION_VERSION=~2` exécute l’application de fonction sur la version 2.x du runtime Azure Functions.

Vous pouvez désormais déployer un projet de code de fonction dans cette application de fonction.

## <a name="deploy-the-function-code"></a>Déployer le code de fonction 

# <a name="net-v12-sdk"></a>[\.SDK .NET v12](#tab/dotnet)

L’exemple de fonction de redimensionnement C# est disponible sur [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Déployez ce projet de code dans l’application de fonction à l’aide de la commande [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[SDK Node.js v10](#tab/nodejsv10)

La fonction d’exemple de redimensionnement Node.js est disponible sur [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10). Déployez ce projet de code de fonction dans l’application de fonction à l’aide de la commande [az functionapp deployment source config](/cli/azure/functionapp/deployment/source).

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

La fonction de redimensionnement d’image est déclenchée par les requêtes HTTP qui lui sont envoyées à partir du service Event Grid. Vous indiquez à Event Grid que vous souhaitez obtenir ces notifications à l’URL de votre fonction en créant un abonnement d’événement. Pour ce didacticiel, vous vous abonnez à des événements créés par des objets blob.

Les données passées à la fonction à partir de la notification de Event Grid incluent l’URL de l’objet blob. Cette URL est ensuite passée à la liaison d’entrée pour obtenir l’image chargée depuis le stockage Blob. La fonction génère une image miniature et écrit le flux résultant dans un conteneur distinct du stockage Blob.

Ce projet utilise `EventGridTrigger` pour le type de déclencheur. Il est préférable d’utiliser le déclencheur Event Grid plutôt que les déclencheurs HTTP génériques. Event Grid valide automatiquement les déclencheurs de fonction Event Grid. Dans le cas des déclencheurs HTTP génériques, vous devez implémenter la [réponse de validation](security-authentication.md).

# <a name="net-v12-sdk"></a>[\.SDK .NET v12](#tab/dotnet)

Pour en savoir plus sur cette fonction, consultez les [fichiers function.json et run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[SDK Node.js v10](#tab/nodejsv10)

Pour plus d’informations sur cette fonction, consultez les [fichiers function.json et index.js files](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Le code de projet de fonction est déployé directement à partir du dépôt d’exemples publics. Pour plus d’informations sur les options de déploiement Azure Functions, consultez [Déploiement continu pour Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Créer un abonnement d’événement

Un abonnement d’événement indique les événements générés par le fournisseur que vous souhaitez envoyer vers un point de terminaison spécifique. Dans ce cas, le point de terminaison est exposé par votre fonction. Pour créer un abonnement d’événement qui envoie des notifications à votre fonction dans le portail Azure, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com), en haut de la page, recherchez et sélectionnez `Function App`, puis choisissez l’application de fonction que vous venez de créer. Sélectionnez **Fonctions** et choisissez la fonction **Miniature**.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="Choisir la fonction Miniature dans le portail":::

1.  Sélectionnez **Intégration**, puis choisissez le **Déclencheur Event Grid** et sélectionnez **Créer un abonnement Event Grid**.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Accéder à Ajouter un abonnement Event Grid dans le portail Azure" :::

1. Utilisez les paramètres d’abonnement d’événement, comme spécifié dans le tableau.
    
    ![Créer un abonnement d’événement à partir de la fonction dans le portail Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Nom** | imageresizersub | Nom du nouvel abonnement d’événement. |
    | **Type de rubrique** | Comptes de stockage | Choisissez le fournisseur d’événements de compte de stockage. |
    | **Abonnement** | Votre abonnement Azure | Par défaut, votre abonnement Azure actuel est sélectionné. |
    | **Groupe de ressources** | myResourceGroup | Sélectionnez **Utiliser l’existant**, puis choisissez le groupe de ressources que vous avez utilisé dans ce didacticiel. |
    | **Ressource** | Votre compte de stockage Blob | Choisissez le compte de stockage Blob que vous avez créé. |
    | **Nom de la rubrique système** | imagestoragesystopic | Spécifiez un nom pour la rubrique système. Pour en savoir plus sur les rubriques système, consultez [Vue d’ensemble des rubriques système](system-topics.md). |    
    | **Types d’événements** | BlobCreated | Décochez tous les types autres que **BlobCreated**. Seuls les types d’événements de `Microsoft.Storage.BlobCreated` sont passés à la fonction. |
    | **Type de point de terminaison** | autogenerated | Prédéfini comme une **fonction Azure**. |
    | **Point de terminaison** | autogenerated | Nom de la fonction. Dans ce cas, il s’agit de **Thumbnail**. |

1. Basculez vers l’onglet **Filtres** pour effectuer les actions suivantes :
    1. Sélectionnez l’option **Activer le filtrage d’objet**.
    1. Dans **Le sujet commence par**, entrez la valeur suivante : **/blobServices/default/containers/images/** .

        ![Spécifier un filtre pour l’abonnement d’événement](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Pour ajouter l’abonnement d’événement, sélectionnez **Créer**. Cela crée un abonnement d’événement qui déclenche la fonction `Thumbnail` quand un objet blob est ajouté au conteneur `images`. La fonction redimensionne les images et les ajoute au conteneur `thumbnails`.

Maintenant que les services backend sont configurés, testez la fonctionnalité de redimensionnement d’images dans l’exemple d’application web.

## <a name="test-the-sample-app"></a>Tester l’exemple d’application

Pour tester le redimensionnement d’images dans l’application web, accédez à l’URL de l’application publiée. L’URL par défaut de l’application web est `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.SDK .NET v12](#tab/dotnet)

Cliquez sur la zone **Charger des photos** pour sélectionner et charger un fichier. Vous pouvez également faire glisser une photo dans cette zone.

Notez que, lorsque l’image chargée disparaît, une copie de celle-ci est affichée dans le carrousel **Miniatures générées**. Cette image a été redimensionnée par la fonction, ajoutée au conteneur *thumbnails* et téléchargée par le client web.

![Capture d’écran montrant une application web publiée intitulée « ImageResizer » dans un navigateur pour le SDK \.NET v12.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[SDK Node.js v10](#tab/nodejsv10)

Cliquez sur **Choisir un fichier** pour sélectionner un fichier, puis cliquez sur **Charger une image**. Lorsque le chargement est terminé, le navigateur accède à une page de confirmation. Cliquez sur le lien pour revenir à la page d’accueil. Une copie de l’image chargée s’affiche dans la zone **Miniatures générées**. (si l’image ne s’affiche pas, essayez de recharger la page). Cette image a été redimensionnée par la fonction, ajoutée au conteneur *thumbnails* et téléchargée par le client web.

![Application web publiée dans le navigateur](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un compte de stockage Azure général
> * Déployer du code sans serveur à l’aide d’Azure Functions
> * Créer un abonnement d’événement Stockage Blob dans Event Grid

Passez à la troisième partie de la série de didacticiels sur le stockage pour savoir comment sécuriser l’accès au compte de stockage.

> [!div class="nextstepaction"]
> [Sécuriser l’accès aux données d’une application dans le cloud](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Pour plus d’informations sur Event Grid, consultez [Présentation d’Event Grid](overview.md).
+ Pour essayer un autre didacticiel utilisant Azure Functions, consultez [Créer une fonction qui s’intègre à Azure Logic Apps](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
