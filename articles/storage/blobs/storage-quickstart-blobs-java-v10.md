---
title: 'Démarrage rapide : Utiliser le SDK de stockage Java pour créer un objet blob'
description: Dans ce guide de démarrage rapide, vous créez un conteneur dans le stockage d’objets (Azure Blob), chargez un fichier, répertoriez des objets et téléchargez à l’aide du Kit de développement logiciel (SDK) de stockage Java.
services: storage
author: mhopkins-msft
ms.custom: mvc, seo-java-july2019
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 12507ba87148bef6378542feb4ebde3b1c291a72
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565921"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Démarrage rapide : Charger, télécharger et lister des objets blob à l’aide du kit de développement logiciel (SDK) Stockage Java V10

Dans ce démarrage rapide, vous apprenez à utiliser le nouveau Kit de développement logiciel (SDK) de stockage Java pour charger, télécharger et répertorier des objets blob de blocs dans un conteneur de stockage Blob Azure. Le nouveau Kit de développement logiciel (SDK) Java utilise le modèle de programmation réactive avec RxJava, ce qui permet des opérations asynchrones. En savoir plus sur les [extensions réactives RxJava pour la machine virtuelle Java](https://github.com/ReactiveX/RxJava). 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Vérifiez que les prérequis suivants sont installés :

* [Maven](https://maven.apache.org/download.cgi), pour travailler à partir de la ligne de commande, ou tout autre environnement de développement intégré Java de votre choix.
* [JDK](https://aka.ms/azure-jdks)

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

[L’exemple d’application](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) utilisé dans ce guide de démarrage rapide est une application console de base. 

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Cette commande clone le dépôt dans votre dossier git local.

Une fois l’importation du projet terminée, ouvrez **Quickstart.java** (situé dans **src/main/java/quickstart**).

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
Cette solution nécessite que vous conserviez en toute sécurité le nom et la clé de votre compte de stockage. Conservez-les dans des variables d’environnement en local sur l’ordinateur qui exécute l’échantillon. Suivez l’exemple Linux ou Windows, en fonction de votre système d’exploitation, pour créer les variables d’environnement.

### <a name="linux-example"></a>Exemple Linux

```bash
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemple Windows

```CMD
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans votre répertoire par défaut, **AppData\Local\Temp**, pour les utilisateurs Windows. Il vous invite à procéder comme suit :

1. Saisissez les commandes permettant de charger le fichier de test vers le stockage d'objets Blob Azure.
2. Répertoriez les objets blob du conteneur.
3. Téléchargez le fichier chargé sous un nouveau nom pour pouvoir comparer l’ancien fichier et le nouveau. 

Si vous souhaitez exécuter l’exemple à l’aide de Maven au niveau de la ligne de commande, ouvrez un interpréteur de commandes et accédez à **storage-blobs-java-v10-quickstart** à l’intérieur de votre répertoire cloné. Puis saisissez `mvn compile exec:java`.

Cet exemple montre la sortie si vous exécutez l’application sous Windows.

```Output
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Comme vous contrôlez l’exemple, entrez les commandes nécessaires pour qu’il exécute le code. Les entrées respectent la casse.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Vérifiez les fichiers. Ensuite, sélectionnez **E** et **Entrée** pour terminer la démonstration et de supprimer les fichiers de test. Comme vous savez maintenant ce que fait l’exemple, ouvrez le fichier**Quickstart.java** pour examiner le code. 

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Les sections suivantes expliquent l’exemple de code pour vous aider à comprendre son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer les références aux objets utilisés pour accéder au stockage d’objets blob et le gérer. Ces objets reposent les uns sur les autres. Chacun est utilisé par le suivant dans la liste.

1. Créez une instance de l’objet **StorageURL** pointant vers le compte de stockage.

    * L’objet [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) est une représentation de votre compte de stockage. Vous l’utilisez pour générer un nouveau pipeline. 
    * Un pipeline est un ensemble de stratégies utilisées pour manipuler des requêtes et des réponses avec des mécanismes d’autorisation, de journalisation et de nouvelle tentative. Pour plus d’informations, consultez [Pipeline HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * À l’aide du pipeline, créez une instance de l’objet [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * À l’aide de l’objet **ServiceURL**, créez une instance de [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * L’élément **ContainerURL** est nécessaire d’exécuter des opérations sur les conteneurs d’objets blob.

2. Créez une instance de l’objet **ContainerURL** qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser les fichiers sur votre ordinateur.

    * **ContainerURL** fournit un point d’accès au service conteneur. 
    * Vous pouvez créer une instance de l’objet [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable) à l’aide de [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * L’objet **BlobURL** est nécessaire de créer des objets blob.

3. Créez une instance de l’objet **BlobURL** pointant vers l’objet blob spécifique qui vous intéresse. 

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Créez un conteneur. 

Dans cette section, vous créez une instance de **ContainerURL**. Vous créez également un conteneur en même temps. Dans l’exemple, le conteneur est appelé **quickstart**. 

Cet exemple utilise [ContainerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_), afin de créer un conteneur chaque fois que l’exemple est exécuté. Sinon, vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus couramment utilisés. Ils sont utilisés dans ce guide de démarrage rapide. 

1. Pour charger un fichier dans un objet blob, obtenez une référence à l’objet blob dans le conteneur cible. 
2. Une fois que vous obtenez la référence d’objet blob, vous pouvez charger un fichier vers ce dernier à l’aide des API suivantes :

   * API de bas niveau. Exemples : [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_), également appelée PutBlob, et [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable), également appelée PutBLock, dans l’instance de **BlockBlobURL**. 

   * API de haut niveau fournies dans la [classe TransferManager](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Par exemple, la méthode [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

     Cette opération crée l’objet blob s’il n’existe pas. Elle remplace l’objet blob s’il existe déjà.

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement. Il stocke le fichier à charger en tant que **sourceFile** et l’URL de l’objet blob dans **blob**. L’exemple suivant charge le fichier sur votre conteneur nommé **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {

    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());

    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Les objets blob de blocs peuvent être n’importe quel type de fichier texte ou binaire. Les objets blob de pages sont principalement utilisés pour les fichiers VHD utilisés pour stocker des machines virtuelles IaaS. Les objets blob d’ajout permettent d’ajouter des données à la fin et sont souvent utilisés pour la journalisation. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Vous pouvez obtenir la liste des objets d’un conteneur à l’aide de [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable). Cette méthode renvoie jusqu’à 5 000 objets à la fois avec un marqueur de continuation (suivant) si d’autres objets existent dans le conteneur. Créez une fonction d’assistance qui s’appelle elle-même à plusieurs reprises tant qu’un marqueur suivant se trouve dans la réponse **listBlobsFlatSegment** précédente.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs,
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }

    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */

        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */

        return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
            .flatMap(containersListBlobFlatSegmentResponse ->
                listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez les objets blob sur votre disque local à l’aide de [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

Le code suivant télécharge l’objet blob chargé dans une section précédente. Il ajoute le suffixe **_DOWNLOADED** au nom de l’objet blob, ce qui vous permet de voir les deux fichiers sur le disque local. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin des objets blob chargés dans ce démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Cette méthode permet de supprimer les fichiers du conteneur également.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et le stockage blob Azure avec Java. 

> [!div class="nextstepaction"]
> [Code source du Kit de développement logiciel (SDK) de stockage pour Java V10](https://github.com/Azure/azure-storage-java/)
> [Référence de l’API](https://docs.microsoft.com/java/api/overview/azure/storage/client?view=azure-java-stable)
> [En savoir plus sur RxJava](https://github.com/ReactiveX/RxJava)
