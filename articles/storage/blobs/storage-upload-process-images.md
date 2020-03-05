---
title: Charger des données d’image dans le cloud avec le Stockage Azure | Microsoft Docs
description: Utiliser le stockage Blob Azure avec une application web pour stocker les données d’application
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 11/16/2019
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: e4a2b1ee1b2b2726b7e0a807a965dbf4bc6b9500
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196992"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutoriel : Charger des données d’image dans le cloud avec le Stockage Azure

Ce tutoriel est la première partie d’une série d’étapes. Dans ce tutoriel, vous allez apprendre à déployer une application web qui utilise la bibliothèque de client Stockage Azure dans le but de charger des images dans un compte de stockage. À la fin, vous disposerez d’une application web qui stocke et affiche des images à partir du Stockage Azure.

# <a name="net"></a>[\.NET](#tab/dotnet)
![Application de redimensionnement d’image dans .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdk"></a>[SDK Node.js V2](#tab/nodejs)
![Application de redimensionnement d’image dans Node.js V2](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdk"></a>[SDK Node.js V10](#tab/nodejsv10)
![Application de redimensionnement d’image dans Node.js V10](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Créez un compte de stockage.
> * Créer un conteneur et définir des autorisations
> * Récupérer une clé d’accès
> * Déployer une application web dans Azure
> * Configuration des paramètres d’application
> * Interagir avec l’application web

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer d’un abonnement Azure. Avant de commencer, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pour installer et utiliser l’interface CLI localement, ce tutoriel vous demande d’exécuter Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources 

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.  

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia

```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

L’exemple charge des images sur un conteneur Blob dans un compte de stockage Azure. Le compte de stockage Azure fournit un espace de noms unique pour stocker les objets de données de Stockage Azure et y accéder. Créez un compte de stockage dans le groupe de ressources que vous avez créé à l’aide de la commande [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> Dans la deuxième partie de ce tutoriel, vous utiliserez Azure Event Grid avec le stockage Blob. Veillez à créer votre compte de stockage dans une région Azure qui prend en charge Event Grid. Pour obtenir la liste des régions prises en charge, consultez [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Dans la commande suivante, indiquez le nom global unique de votre compte de stockage Blob dans l’espace réservé `<blob_storage_account>`.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia --resource-group myResourceGroup --sku Standard_LRS --kind blobstorage --access-tier hot

```

## <a name="create-blob-storage-containers"></a>Créer des conteneurs de stockage Blob

L’application utilise deux conteneurs dans le compte de stockage Blob. Les conteneurs s’apparentent à des dossiers où sont stockés les objets blob. C’est dans le conteneur *images* que l’application charge les images pleine résolution. Dans l’une des parties suivantes de la série, une application de fonction Azure charge les miniatures d’images redimensionnées dans le conteneur *thumbnails*.

Récupérez la clé du compte de stockage avec la commande [az storage account keys list](/cli/azure/storage/account/keys). Ensuite, utilisez cette clé pour créer deux conteneurs avec la commande [az storage container create](/cli/azure/storage/container).  

L’accès public du conteneur *Images* est défini sur `off`. L’accès public du conteneur *Miniatures* est défini sur `container`. Le paramètre d’accès public `container` permet aux utilisateurs qui visitent la page web de voir les miniatures.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create -n images --account-name $blobStorageAccount --account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount --account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey

```

Notez le nom et la clé de votre compte de stockage Blob. L’exemple d’application utilise ces paramètres pour se connecter au compte de stockage afin de charger les images. 

## <a name="create-an-app-service-plan"></a>Créer un plan App Service

Un [plan App Service](../../app-service/overview-hosting-plans.md) spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application.

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan).

L’exemple suivant crée un plan App Service nommé `myAppServicePlan` dans le niveau tarifaire **Gratuit** :

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free

```

## <a name="create-a-web-app"></a>Créer une application web

L’application web offre un espace d’hébergement au code de l’exemple d’application qui est déployé à partir du dépôt d’exemples GitHub. Créez une [application web](../../app-service/overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp).  

Dans la commande suivante, remplacez `<web_app>` par un nom unique. Les caractères valides sont `a-z`, `0-9` et `-`. Si `<web_app>` n’est pas unique, le message d’erreur suivant s'affiche : _Un site web avec ce nom `<web_app>` existe déjà._ L’URL par défaut de l’application web est `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan

```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Déployer l’exemple d’application à partir du référentiel GitHub

# <a name="net"></a>[\.NET](#tab/dotnet)

App Service prend en charge plusieurs façons de déployer du contenu vers une application web. Dans ce didacticiel, vous déployez l’application web depuis un [exemple de référentiel GitHub public](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configurez le déploiement GitHub sur l’application web avec la commande [az webapp deployment source config](/cli/azure/webapp/deployment/source).

L’exemple de projet contient une application [ASP.NET MVC](https://www.asp.net/mvc). Cette application accepte une image, l’enregistre dans un compte de stockage et affiche des images à partir d’un conteneur de miniatures. L’application web utilise les espaces de noms [Microsoft.Azure.Storage](/dotnet/api/overview/azure/storage), [Microsoft.Azure.Storage.Blob](/dotnet/api/microsoft.azure.storage.blob)et Microsoft.Azure.Storage.Auth de la bibliothèque de client du Stockage Azure pour interagir avec le stockage Azure.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup --branch master --manual-integration --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp

```

# <a name="nodejs-v2-sdk"></a>[SDK Node.js V2](#tab/nodejs)
App Service prend en charge plusieurs façons de déployer du contenu vers une application web. Dans ce didacticiel, vous déployez l’application web depuis un [exemple de référentiel GitHub public](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Configurez le déploiement GitHub sur l’application web avec la commande [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup --branch master --manual-integration --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node

```

# <a name="nodejs-v10-sdk"></a>[SDK Node.js V10](#tab/nodejsv10)
App Service prend en charge plusieurs façons de déployer du contenu vers une application web. Dans ce didacticiel, vous déployez l’application web depuis un [exemple de référentiel GitHub public](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Configurez le déploiement GitHub sur l’application web avec la commande [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup --branch master --manual-integration --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10

```

---

## <a name="configure-web-app-settings"></a>Configurer les paramètres de l’application web

# <a name="net"></a>[\.NET](#tab/dotnet)

L’exemple d’application web utilise la [bibliothèque de client du Stockage Azure](/dotnet/api/overview/azure/storage) pour demander des jetons d’accès de requête, qui servent à charger des images. Les informations d’identification du compte de stockage utilisées par le SDK Stockage sont définies dans les paramètres de l’application web. Ajoutez des paramètres d’application à l’application déployée avec la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup --settings AzureStorageConfig__AccountName=$blobStorageAccount AzureStorageConfig__ImageContainer=images AzureStorageConfig__ThumbnailContainer=thumbnails AzureStorageConfig__AccountKey=$blobStorageAccountKey

```

# <a name="nodejs-v2-sdk"></a>[SDK Node.js V2](#tab/nodejs)

L’exemple d’application web utilise la [bibliothèque de client du Stockage Azure](https://docs.microsoft.com/javascript/api/azure-storage) pour demander des jetons d’accès de requête, qui servent à charger des images. Les informations d’identification du compte de stockage utilisées par le SDK Stockage sont définies dans les paramètres de l’application web. Ajoutez des paramètres d’application à l’application déployée avec la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group resourceGroupName --name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup --settings AzureStorageConfig__ImageContainer=images AzureStorageConfig__ThumbnailContainer=thumbnails AzureStorageConfig__AccountName=$blobStorageAccount AzureStorageConfig__AccountKey=$blobStorageAccountKey AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString

```

# <a name="nodejs-v10-sdk"></a>[SDK Node.js V10](#tab/nodejsv10)

L’exemple d’application web utilise la [bibliothèque de client du Stockage Azure](https://github.com/Azure/azure-storage-js) pour demander des jetons d’accès de requête, qui servent à charger des images. Les informations d’identification du compte de stockage utilisées par le SDK Stockage sont définies dans les paramètres de l’application web. Ajoutez des paramètres d’application à l’application déployée avec la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey

```

---

Une fois l’application web déployée et configurée, vous pouvez tester la fonctionnalité de chargement d’images dans l’application.

## <a name="upload-an-image"></a>Charger une image

Pour tester l’application web, accédez à l’URL de l’application publiée. L’URL par défaut de l’application web est `https://<web_app>.azurewebsites.net`.

# <a name="net"></a>[\.NET](#tab/dotnet)

Sélectionnez la zone **Charger des photos** pour sélectionner et charger un fichier, ou faites glisser un fichier dans la zone. L’image disparaît si le chargement a réussi. La section **Miniatures générées** reste vide jusqu’aux tests effectués plus loin dans cette rubrique.

![Télécharger des photos dans .NET](media/storage-upload-process-images/figure1.png)

Dans l’exemple de code, la tâche `UploadFiletoStorage` du fichier *Storagehelper.cs* sert à charger les images dans le conteneur *Images* au sein du compte de stockage suivant la méthode [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync). L’exemple de code suivant contient la tâche `UploadFiletoStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Les classes et méthodes suivantes sont utilisées dans la tâche précédente :

|Classe  |Méthode  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)       |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)    | [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference)        |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)     | [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync)        |

# <a name="nodejs-v2-sdk"></a>[SDK Node.js V2](#tab/nodejs)

Sélectionnez **Choisir un fichier** pour sélectionner un fichier, puis cliquez sur **Charger une image**. La section **Miniatures générées** reste vide jusqu’aux tests effectués plus loin dans cette rubrique. 

![Télécharger des photos dans Node.js V2](media/storage-upload-process-images/upload-app-nodejs.png)

Dans l’exemple de code, l’itinéraire `post` est responsable du chargement de l’image dans un conteneur d’objets blob. L’itinéraire utilise les modules pour faciliter le traitement du chargement :

- [multer](https://github.com/expressjs/multer) implémente la stratégie de chargement pour le gestionnaire de routage.
- [into-stream](https://github.com/sindresorhus/into-stream) convertit la mémoire tampon en un flux, comme le nécessite [createBlockBlobFromStream].(https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

Étant donné que le fichier est envoyé à la route, le contenu du fichier reste en mémoire jusqu’à son chargement dans le conteneur d’objets blob.

> [!IMPORTANT]
> Le chargement en mémoire des fichiers volumineux peut nuire aux performances de votre application web. Si vous pensez que des utilisateurs pourraient publier des fichiers volumineux, vous devrez peut-être utiliser la mise en lots de fichiers sur le système de fichiers, puis planifier les chargements dans le stockage Blob. Une fois que les fichiers sont dans le stockage Blob, vous pouvez les supprimer du système de fichiers du serveur.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```

# <a name="nodejs-v10-sdk"></a>[SDK Node.js V10](#tab/nodejsv10)

Sélectionnez **Choisir un fichier** pour sélectionner un fichier, puis cliquez sur **Charger une image**. La section **Miniatures générées** reste vide jusqu’aux tests effectués plus loin dans cette rubrique. 

![Télécharger des photos dans Node.js V10](media/storage-upload-process-images/upload-app-nodejs.png)

Dans l’exemple de code, l’itinéraire `post` est responsable du chargement de l’image dans un conteneur d’objets blob. L’itinéraire utilise les modules pour faciliter le traitement du chargement :

- [multer](https://github.com/expressjs/multer) implémente la stratégie de chargement pour le gestionnaire de routage.
- [into-stream](https://github.com/sindresorhus/into-stream) convertit la mémoire tampon en flux, comme le nécessite [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

Étant donné que le fichier est envoyé à la route, le contenu du fichier reste en mémoire jusqu’à son chargement dans le conteneur d’objets blob.

> [!IMPORTANT]
> Le chargement en mémoire des fichiers volumineux peut nuire aux performances de votre application web. Si vous pensez que des utilisateurs pourraient publier des fichiers volumineux, vous devrez peut-être utiliser la mise en lots de fichiers sur le système de fichiers, puis planifier les chargements dans le stockage Blob. Une fois que les fichiers sont dans le stockage Blob, vous pouvez les supprimer du système de fichiers du serveur.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Vérifier que l’image s’affiche dans le compte de stockage

Connectez-vous au [portail Azure](https://portal.azure.com). Dans le menu de gauche, sélectionnez **Comptes de stockage**, puis sélectionnez le nom de votre compte de stockage. Sélectionnez **Conteneurs**, puis sélectionnez le conteneur **images**.

Vérifiez que l’image s’affiche dans le conteneur.

![Conteneur de liste d’images du portail Azure](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Tester l’affichage des miniatures

Pour tester l’affichage des miniatures, chargez une image dans le conteneur **Miniatures** afin de vérifier que l’application peut lire les **miniatures** du conteneur.

Connectez-vous au [portail Azure](https://portal.azure.com). Dans le menu de gauche, sélectionnez **Comptes de stockage**, puis sélectionnez le nom de votre compte de stockage. Sélectionnez **Conteneurs**, puis sélectionnez le conteneur **thumbnails** (miniatures). Cliquez sur **Charger** pour ouvrir le volet **Charger des objets Blob**.

Choisissez un fichier à l’aide du sélecteur de fichiers, puis sélectionnez **Charger**.

Revenez à votre application pour vérifier que l’image chargée dans le conteneur **thumbnails** est visible.

# <a name="net"></a>[\.NET](#tab/dotnet)
![Application de redimensionnement d’image .NET avec la nouvelle image affichée](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdk"></a>[SDK Node.js V2](#tab/nodejs)
![Application de redimensionnement d’image Node.js V2 avec la nouvelle image affichée](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdk"></a>[SDK Node.js V10](#tab/nodejsv10)
![Application de redimensionnement d’image Node.js V10 avec la nouvelle image affichée](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Dans la deuxième partie de la série, vous allez automatiser la création d’images miniatures. Vous n’avez donc pas besoin de cette image. Dans le conteneur **thumbnails**, sur le Portail Azure, sélectionnez l’image que vous avez chargée et sélectionnez **Supprimer** pour supprimer l’image. 

Vous pouvez activer CDN pour mettre en cache le contenu de votre compte de stockage Azure. Pour plus d’informations sur l’activation de CDN avec votre compte de stockage Azure, consultez [Intégrer un compte de stockage Azure à Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de la série, vous avez vu comment configurer une application web pour interagir avec le stockage.

Passez à la deuxième partie de la série pour savoir comment utiliser Event Grid dans le but de déclencher une fonction Azure afin de redimensionner une image.

> [!div class="nextstepaction"]
> [Utiliser Event Grid pour déclencher une fonction Azure afin de redimensionner une image chargée](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
