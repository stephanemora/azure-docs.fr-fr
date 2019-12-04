---
title: 'Démarrage rapide : Bibliothèque de client Stockage Blob Azure v2 pour JavaScript'
description: Créez un compte de stockage et un conteneur dans un stockage d’objets (blob). Utilisez la bibliothèque de client Stockage Azure pour Node.js v2 afin de charger un objet blob, de télécharger un objet blob et de lister les objets blob dans un conteneur.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7300d4eccec5b1e4b3b5b7dc292cf6150a42e7b4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269687"
---
# <a name="quickstart-azure-blob-storage-client-library-v2-for-javascript"></a>Démarrage rapide : Bibliothèque de client Stockage Blob Azure v2 pour JavaScript

Dans ce guide pratique, vous allez découvrir comment utiliser la bibliothèque de client pour Node.js v2 afin de charger, télécharger et lister des objets blob avec le Stockage Blob Azure.

> [!TIP]
> La dernière version de la bibliothèque de client pour Node.js du service Stockage Azure est la version 10. Si possible, Microsoft vous recommande d’utiliser la dernière version de la bibliothèque de client. Pour vous familiariser avec la version 10, reportez-vous à [Démarrage rapide : Charger, télécharger, lister et supprimer des objets blob à l’aide de la bibliothèque de client Azure Storage pour JavaScript v10 (préversion)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Créez un compte de stockage Azure dans le [portail Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Si vous avez besoin d’aide pour créer un compte de stockage, consultez [Créez un compte de stockage](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

L’[exemple d’application](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) est une application console Node.js simple. Pour commencer, clonez le référentiel dans votre machine en exécutant la commande suivante :

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Pour ouvrir l’application, recherchez le dossier *storage-blobs-node-quickstart* et ouvrez-le dans l’environnement de modification de code de votre préférence.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Avant d’exécuter l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Le référentiel d’exemple contient un fichier nommé *.env.example*. Vous pouvez renommer ce fichier en supprimant l’extension *.example*, ce qui donne un fichier nommé *.env*. Dans le fichier *.env*, ajoutez la valeur de votre chaîne de connexion après la clé *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Installer les packages nécessaires

Dans le répertoire de l’application, exécutez la commande *npm install* pour installer les packages nécessaires à l’application.

```bash
npm install
```

## <a name="run-the-sample"></a>Exécution de l'exemple
Maintenant que les dépendances sont installées, vous pouvez exécuter l’exemple en émettant la commande suivante :

```bash
npm start
```

La sortie du script doit ressembler à ceci :

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Si vous utilisez un nouveau compte de stockage pour cet exmple, les noms des conteneurs peuvent ne pas être listés sous l’étiquette « *Containers* ».

## <a name="understanding-the-code"></a>Présentation du code
La première expression est utilisée pour charger des valeurs dans les variables d’environnement.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Le module *dotenv* charge les variables d’environnement au moment de l’exécution locale de l’application à des fins de débogage. Les valeurs sont définies dans un fichier nommé *.env* et chargées dans le contexte d’exécution actuel. Dans des contextes de production, la configuration du serveur fournit ces valeurs. C’est pourquoi ce code est uniquement exécuté quand le script n’est pas en cours d’exécution dans un contexte de « production ».

```javascript
const path = require('path');
const storage = require('azure-storage');
```

L’objectif de ce module est le suivant : 

fichier nommé *.env* dans le contexte d’exécution actuel
- *path* est requis pour déterminer le chemin d’accès absolu du fichier à charger dans le stockage d’objets blob
- *azure-storage* est le module de la [bibliothèque de client Stockage Azure](https://docs.microsoft.com/javascript/api/azure-storage) pour Node.js

Ensuite, la variable **blobService** est initialisée comme nouvelle instance du service Blob Azure.

```javascript
const blobService = storage.createBlobService();
```

Dans l’implémentation suivante, les fonctions *blobService* sont incluses dans une *Promesse*, ce qui permet d’accéder à la fonction Javascript *async* et d’*attendre* que l’opérateur simplifie la nature de rappel de l’[API Stockage Azure](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Lorsqu’une réponse réussie est retournée pour chaque fonction, la promesse est résolue avec des données cohérentes, ainsi qu’un message spécifique à l’action.

### <a name="list-containers"></a>Répertorier les conteneurs

La fonction *listContainers* appelle [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) qui retourne des collections de conteneurs dans des groupes.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

La taille des groupes est configurable par le biais de [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). L’appel de *listContainersSegmented* retourne des métadonnées d’objets blob sous la forme d’un tableau d’instances [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest). Les résultats y sont retournés par lots de 5 000 incréments (segments). S’il y a plus de 5 000 objets blob dans un conteneur, les résultats incluent une valeur pour *continuationToken*. Pour lister les segments suivants à partir du conteneur d’objets blob, vous pouvez passer le jeton de continuation dans *listContainersSegment* en tant que second argument.

### <a name="create-a-container"></a>Créez un conteneur.

La fonction *createContainer* appelle [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) et définit le niveau d’accès approprié pour l’objet blob.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Le second paramètre (*options*) de la fonction **createContainerIfNotExists** accepte une valeur pour [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). La valeur *blob* pour *publicAccessLevel* spécifie que les données d’objets blob spécifiques sont exposées publiquement. Ce paramètre est contraire au niveau d’accès de *container*, qui offre la capacité de répertorier le contenu du conteneur.

L’utilisation de **createContainerIfNotExists** permet à l’application d’exécuter la commande *createContainer* plusieurs fois sans retourner d’erreurs lorsque le conteneur existe déjà. Dans un environnement de production, vous appelez souvent la fonction **createContainerIfNotExists** une seule fois, étant donné que le même conteneur est utilisé pour toute l’application. Dans ces cas, vous pouvez créer le conteneur en avance via le portail ou Azure CLI.

### <a name="upload-text"></a>Charger du texte

La fonction *uploadString* appelle [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) pour écrire (ou remplacer) une chaîne arbitraire dans le conteneur d’objets blob.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Charger un fichier local

La fonction *uploadLocalFile* utilise [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) pour charger et écrire (ou remplacer) un fichier du système de fichiers dans le stockage d’objets blob. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
D’autres approches pour charger du contenu dans des objets blob sont disponibles, dans lesquelles on utilise du [texte](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) et des [flux](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Pour vérifier que le fichier est chargé dans votre stockage d’objets blob, vous pouvez utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour afficher les données dans votre compte.

### <a name="list-the-blobs"></a>Lister les objets blob

La fonction *listBlobs* appelle la méthode [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) pour retourner une liste de métadonnées d’objets blob dans un conteneur. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Appeler la méthode *listBlobsSegmented* permet de retourner des métadonnées d’objets blob sous forme de tableau d’instances [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Les résultats y sont retournés par lots de 5 000 incréments (segments). S’il y a plus de 5 000 objets blob dans un conteneur, les résultats incluent une valeur pour **continuationToken**. Pour répertorier les segments suivants à partir d’un conteneur d’objets blob, vous pouvez passer le jeton de continuation dans **listBlobsSegmented** en tant que second argument.

### <a name="download-a-blob"></a>Télécharger un objet blob

La fonction *downloadBlob* utilise [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) pour télécharger le contenu de l’objet blob dans le chemin de fichier absolu spécifié.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
L’implémentation indiquée ici change la source pour retourner le contenu de l’objet blob en tant que chaîne. Vous pouvez également télécharger l’objet blob en tant que [flux](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) ou directement dans un [fichier local](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Supprimer un objet blob

La fonction *deleteBlob* appelle la fonction [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Comme son nom l’indique, cette fonction ne retourne pas d’erreur si l’objet blob a déjà été supprimé.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Supprimer un conteneur

Pour supprimer un conteneur, appelez la méthode *deleteContainer* méthode du service blob et passez le nom du conteneur.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Appel de code

Pour prendre en charge la syntaxe *async/await* de JavaScript, tout le code appelant est wrappé dans une fonction nommée *execute*. Puis, execute est appelé et géré comme une promesse.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Tout le code suivant s’exécute dans la fonction execute où le commentaire `// commands` est placé.

Tout d’abord, les variables appropriées sont déclarées pour attribuer des noms, échantillonner du contenu et pointer vers le fichier local à charger sur Stockage Blob.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Pour lister les conteneurs dans le compte de stockage, la fonction listContainers est appelée et la liste des conteneurs retournée est consignée dans la fenêtre Sortie.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Une fois la liste des conteneurs disponible, vous pouvez utiliser la méthode Array *findIndex* pour voir si le conteneur que vous souhaitez créer existe déjà. Si le conteneur n’existe pas, il est créé.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Ensuite, une chaîne et un fichier local sont chargés dans Stockage Blob.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Le processus permettant de lister des objets blob est le même que celui permettant de lister des conteneurs. L’appel à *listBlobs* retourne un tableau d’objets blob dans le conteneur. Ces objets blob sont consignés dans la fenêtre Sortie.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Pour télécharger un objet blob, la réponse est capturée et utilisée pour accéder à la valeur de l’objet blob. À partir de la réponse, readableStreamBody est converti en chaîne et consigné dans la fenêtre Sortie.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Enfin, l’objet blob et le conteneur sont supprimés du compte de stockage.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Toutes les données écrites dans le compte de stockage sont automatiquement supprimées à la fin de l’exemple de code. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Ressources sur le développement d’applications Node.js avec des objets blob

Consultez ces ressources supplémentaires sur le développement Node.js avec le stockage Blob :

### <a name="binaries-and-source-code"></a>Fichiers binaires et code source

- Affichez et installez le [code source de la bibliothèque de client Node.js](https://github.com/Azure/azure-storage-node) pour Stockage Azure sur GitHub.

### <a name="client-library-reference-and-samples"></a>Référence et exemples de la bibliothèque de client

- Consultez la [référence API Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage) pour plus d’informations sur la bibliothèque de client Node.js.
- Explorez les [exemples de Stockage Blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) écrits avec la bibliothèque de client Node.js.

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment charger un fichier entre un disque local et le Stockage Blob Azure avec Node.js. Pour en savoir plus sur l’utilisation de Stockage Blob, accédez au dépôt GitHub.

> [!div class="nextstepaction"]
> [Kit de développement logiciel (SDK) Stockage Azure pour Node.js et JavaScript dans les navigateurs](https://github.com/Azure/azure-storage-node)
