---
title: 'Démarrage rapide : Bibliothèque de stockage Blob Azure v12 – JavaScript'
description: Ce démarrage rapide explique comment utiliser la bibliothèque de client du stockage blob Azure version 12 pour JavaScript afin de créer un conteneur et un blob dans un stockage (d’objets) blob. Vous apprenez ensuite à télécharger l’objet blob sur votre ordinateur local et à lister tous les objets blob dans un conteneur.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 2e5a5f2a4de4e01d2e4fa66f819e55839959afd0
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130690"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Démarrage rapide : Bibliothèque de client du stockage blob Azure v12 pour JavaScript

Prise en main de la bibliothèque de client du stockage blob Azure v12 pour JavaScript. Le stockage Blob Azure est la solution de stockage d’objet de Microsoft pour le cloud. Suivez les étapes pour installer le package et essayer l’exemple de code pour les tâches de base. Le stockage Blob est optimisé pour stocker de grandes quantités de données non structurées.

> [!NOTE]
> Pour une bonne prise en main de la version précédente du kit de développement logiciel (SDK), consultez [Démarrage rapide : bibliothèque de client du stockage blob Azure pour JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Utilisez la bibliothèque de client du stockage blob Azure v12 pour JavaScript pour :

* Créez un conteneur.
* Charger un blob dans le stockage Azure
* Lister tous les objets blob d’un conteneur
* Télécharger l’objet blob sur votre ordinateur local
* Supprimer un conteneur

[Documentation de référence de l’API](/javascript/api/@azure/storage-blob) | [Code source bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Package (Gestionnaire de package de nœud)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
* Compte de stockage Azure : [créez un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Node.js](https://nodejs.org/en/download/) actuel pour votre système d’exploitation.

## <a name="setting-up"></a>Configuration

Cette section vous guide tout au long de la préparation d’un projet à utiliser avec la bibliothèque de client de stockage blob Azure v12 pour JavaScript.

### <a name="create-the-project"></a>Créer le projet

Créez une application JavaScript nommée *blob-quickstart-v12*.

1. Dans une fenêtre de console (telle que cmd, PowerShell ou Bash), créez un nouveau répertoire pour le projet.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Basculez vers le répertoire *blob-quickstart-v12* nouvellement créé.

    ```console
    cd blob-quickstart-v12
    ```

1. Créez un nouveau fichier texte appelé *package.json*. Ce fichier définit le projet Node.js. Enregistrez ce fichier dans le répertoire *blob-quickstart-v12*. Voici le contenu du fichier :

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Vous pouvez placer votre propre nom dans pour le champ `author`, si vous le souhaitez.
   
### <a name="install-the-package"></a>Installer le package

Alors que vous êtes toujours dans le répertoire *blob-quickstart-v12*, installez la bibliothèque de client du stockage blob Azure pour le package JavaScript à l’aide de la commande `npm install`. Cette commande lit le fichier *package.json* et installe la bibliothèque de client de stockage blob Azure v12 pour le package JavaScript et toutes les bibliothèques dont elle dépend.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez un nouveau fichier texte dans votre éditeur de code
1. Ajouter des appels `require` pour charger des modules Azure et Node.js
1. Créez la structure du programme, y compris la gestion des exceptions de base

    Voici le code :

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Enregistrez le nouveau fichier sous *blob-quickstart-v12.js* dans le répertoire *blob-quickstart-v12*.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copier vos informations d’identification depuis le portail Azure

Lorsque l’exemple d’application effectue une requête auprès du stockage Azure, il doit être autorisé. Pour autoriser une demande, ajoutez les informations d’identification de votre compte de stockage à l’application sous la forme d’une chaîne de connexion. Affichez les informations d'identification de votre compte de stockage en suivant ces étapes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Dans la section **Paramètres** de la présentation du compte de stockage, sélectionnez **Clés d’accès**. Vos clés d’accès au compte s’affichent, ainsi que la chaîne de connexion complète de chaque clé.
4. Recherchez la valeur de **Chaîne de connexion** sous **clé1**, puis sélectionnez le bouton **Copier** pour copier la chaîne de connexion. Vous allez ajouter la valeur de chaîne de connexion dans une variable d’environnement à l’étape suivante.

    ![Capture d’écran montrant comment copier une chaîne de connexion à partir du portail Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Après avoir copié votre chaîne de connexion, écrivez-la dans une variable d’environnement sur l’ordinateur local exécutant l’application. Pour définir la variable d’environnement, ouvrez une fenêtre de console et suivez les instructions pour votre système d’exploitation. Remplacez `<yourconnectionstring>` par votre chaîne de connexion.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Après avoir ajouté la variable d’environnement dans Windows, vous devez démarrer une nouvelle instance de la fenêtre de commande.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Redémarrer des programmes

Après avoir ajouté la variable d’environnement, redémarrez tous les programmes en cours d’exécution qui devront la lire. Par exemple, redémarrez votre environnement de développement ou éditeur avant de continuer.

## <a name="object-model"></a>Modèle objet

Le Stockage Blob Azure est optimisé pour stocker de grandes quantités de données non structurées. Les données non structurées sont des données qui n’obéissent pas à un modèle ou une définition de données en particulier, comme des données texte ou binaires. Le stockage Blob offre trois types de ressources :

* Le compte de stockage
* Un conteneur dans le compte de stockage.
* Un blob dans le conteneur

Le diagramme suivant montre la relation entre ces ressources.

![Diagramme de l’architecture du stockage Blob](./media/storage-blob-introduction/blob1.png)

Utilisez les classes JavaScript suivantes pour interagir avec ces ressources :

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): La classe `BlobServiceClient` vous permet de manipuler les ressources de stockage Azure et les conteneurs blob.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) : La classe `ContainerClient` vous permet de manipuler des conteneurs de stockage Azure et leurs blobs.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient) : La classe `BlobClient` vous permet de manipuler des blobs de stockage Azure.

## <a name="code-examples"></a>Exemples de code

Ces exemples d’extraits montrent comment effectuer les opérations suivantes avec la bibliothèque de client de stockage blob Azure pour JavaScript :

* [Obtenir la chaîne de connexion](#get-the-connection-string)
* [Créer un conteneur](#create-a-container)
* [Charger des objets blob sur un conteneur](#upload-blobs-to-a-container)
* [Lister les objets blob d’un conteneur](#list-the-blobs-in-a-container)
* [Télécharger des objets blob](#download-blobs)
* [Supprimer un conteneur](#delete-a-container)

### <a name="get-the-connection-string"></a>Obtenir la chaîne de connexion

Le code ci-dessous récupère la chaîne de connexion pour le compte de stockage à partir de la variable d’environnement créée dans la section [Configurer votre chaîne de connexion de stockage](#configure-your-storage-connection-string).

Ajoutez ce code dans la fonction `main` :

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Créez un conteneur.

Choisissez un nom pour le nouveau conteneur. Le code ci-dessous ajoute une valeur UUID au nom du conteneur pour s’assurer qu’il est unique.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur l’affectation de noms aux conteneurs et objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Créez une instance de la classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) en appelant la méthode [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-). Ensuite, appelez la méthode [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) pour obtenir une référence à un conteneur. Ensuite, appelez la méthode [créer](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) pour effectivement créer le conteneur dans votre compte de stockage.

Ajoutez ce code à la fin de la fonction `main` :

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>Charger des objets blob sur un conteneur

L’extrait de code suivant :

1. Crée une chaîne de texte à charger dans un blob.
1. Obtient une référence à un objet [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) en appelant la méthode [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) sur le [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) dans la section [Créer un conteneur](#create-a-container).
1. Charge le fichier texte local dans l’objet Blob en appelant la méthode [upload_blob](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-).

Ajoutez ce code à la fin de la fonction `main` :

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Répertoriez les blobs dans le conteneur en appelant la méthode [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-). Dans ce cas, un seul objet blob a été ajouté au conteneur. Il n’y a donc qu’un objet blob répertorié.

Ajoutez ce code à la fin de la fonction `main` :

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez le blob créé précédemment en appelant la méthode [télécharger](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-). L’exemple de code comprend une fonction d’assistance appelée `streamToString` utilisée pour lire un flux lisible Node.js dans une chaîne.

Ajoutez ce code à la fin de la fonction `main` :

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Ajoutez cette fonction d’assistance *après* la fonction `main` :

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Supprimer un conteneur

Le code suivant nettoie les ressources créées par l’application en supprimant le conteneur tout entier à l’aide de la méthode [supprimer](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-). Si vous voulez, vous pouvez aussi supprimer les fichiers locaux.

Ajoutez ce code à la fin de la fonction `main` :

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>Exécuter le code

Cette application crée une chaîne de texte et la charge dans le stockage blob. L’exemple répertorie ensuite le ou les blobs dans le conteneur, télécharge le blob et affiche les données téléchargées.

À partir de l’invite de console, accédez au répertoire contenant le fichier *blob-quickstart-v12.py*, puis exécutez la commande `node` suivante pour exécuter l’application.

```console
node blob-quickstart-v12.js
```

La sortie de l’application ressemble à l’exemple suivant :

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Parcourez le code dans le débogueur et vérifiez votre Portail Azure tout au long du processus. Vérifiez que le conteneur est en cours d’exécution. Vous pouvez ouvrir le blob à l’intérieur du conteneur et afficher le contenu.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à charger, télécharger et répertorier des blobs avec JavaScript.

Pour afficher des exemples d’applications de stockage blob, passez à :

> [!div class="nextstepaction"]
> [Exemples de Kit de développement logiciel (SDK) de stockage blob Azure v12 JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Pour plus d’informations, consultez le [Kit de développement logiciel (SDK) Azure pour JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Pour obtenir des didacticiels, des exemples, des démarrages rapides et d’autres documents, visitez [documentation du Kit de développement logiciel (SDK) Azure pour JavaScript](/azure/javascript/).
