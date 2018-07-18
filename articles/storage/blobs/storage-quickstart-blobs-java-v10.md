---
title: Démarrage rapide Azure - Créer un objet blob dans un stockage d’objets à l’aide du Kit de développement logiciel (SDK) de stockage Java V10 | Microsoft Docs
description: Dans ce démarrage rapide, vous créez un conteneur dans le stockage d’objets (blob), chargez un fichier, répertoriez une liste d’objets et téléchargez à l’aide du Kit de développement logiciel (SDK) de stockage Java.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704468"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide du Kit de développement logiciel (SDK) Java V10 (préversion)

Dans ce démarrage rapide, vous apprenez à utiliser le nouveau Kit de développement logiciel (SDK) de stockage Java pour charger, télécharger et répertorier des objets blob de blocs dans un conteneur de stockage Blob Azure. Le nouveau Kit de développement logiciel (SDK) Java utilise le modèle de programmation réactive avec RxJava, ce qui vous permet d’avoir des opérations asynchrones. Apprenez-en plus sur RxJava [ici](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide :

* Installez et configurez [Maven](http://maven.apache.org/download.cgi) pour qu’il fonctionne à partir de la ligne de commande ou d’un IDE Java de votre choix
* Installer et configurer [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

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
Cette solution demande que le nom et la clé de votre compte de stockage soient stockés en toute sécurité dans les variables d’environnement locales de la machine exécutant l’exemple. Suivez l’un des exemples ci-dessous, en fonction de votre système d’exploitation, pour créer la variable d’environnement.

### <a name="for-linux"></a>Pour Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>Pour Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans votre répertoire par défaut (AppData\Local\Temp, pour les utilisateurs de Windows), puis vous invite à saisir des commandes pour charger le fichier de test dans le stockage d’objets blob, pour répertorier les objets blob dans le conteneur et pour télécharger le fichier chargé avec un nouveau nom pour que vous puissiez comparer les anciens fichiers avec les nouveaux. 

Si vous souhaitez exécuter l’exemple à l’aide de Maven au niveau de la ligne de commande, ouvrez un interpréteur de commandes et accédez à **storage-blobs-java-v10-quickstart** à l’intérieur de votre répertoire cloné. Puis saisissez `mvn compile exec:java`.

Voici un exemple de résultat si vous deviez exécuter l’application sur Windows.

```
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

Comme vous contrôlez l’exemple, entrez les commandes nécessaires pour qu’il exécute le code. N’oubliez pas que les entrées respectent la casse.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur la touche Entrée pour terminer la démonstration et supprimer les fichiers de test. Comme vous savez maintenant ce que fait l’exemple, ouvrez le fichier**Quickstart.java** pour examiner le code. 

## <a name="understand-the-sample-code"></a>Comprendre l’exemple de code

Ensuite, parcourez l’exemple de code pour comprendre son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent l’un sur l’autre : chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet StorageURL pointant vers le compte de stockage.

    L’objet [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) est une représentation de votre compte de stockage et vous permet de générer un nouveau pipeline. Un pipeline ([Pipeline HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)) est un ensemble de stratégies qui sont utilisées pour manipuler des requêtes et des réponses avec des mécanismes d’autorisation, de journalisation et de nouvelle tentative. Avec le pipeline, vous pouvez créer une instance de l’objet [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview), qui vous permet de créer une instance de [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), ce qui est nécessaire pour exécuter des opérations sur les conteneurs d’objets blob.

* Créez une instance de l’objet ContainerURL qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

    **ContainerURL** vous fournit un point d’accès au service conteneur. Vous pouvez utiliser **ContainerURL** pour créer une instance de l’objet [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), nécessaire pour créer des objets blob.

* Créez une instance de l’objet [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview), qui pointe vers l’objet blob spécifique qui vous intéresse.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Créez un conteneur. 

Dans cette section, vous créez une instance de ContainerURL, ainsi qu’un conteneur. Dans l’exemple, le conteneur est appelé **quickstart**. 

Cet exemple utilise [CreateIfNotExists](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview), car nous voulons créer un conteneur chaque fois que l’exemple est exécuté. Ou vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

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

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus couramment utilisés et nous les utilisons donc dans ce guide de démarrage rapide. 

Pour charger un fichier dans un objet blob, obtenez une référence à l’objet blob dans le conteneur cible. Une fois que vous avez la référence à l’objet blob, vous pouvez charger un fichier sur celle-ci à l’aide d’API de niveau inférieur comme [Upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview) ou PutBlob, [StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_) ou PutBLock dans l’instance de BlockBlobURL, ou d’API de niveau supérieur fournies dans la classe [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview), comme la méthode [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview). Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe.

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement. stockage du fichier à charger en tant que **sourceFile** et de l’url de l’objet blob dans **blob**. L’exemple suivant charge le fichier sur votre conteneur nommé **quickstart**.

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

Vous pouvez obtenir une liste des objets dans un conteneur à l’aide de [containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview). Cette méthode renvoie 5 000 objets maximum à la fois avec un marqueur de continuation (marqueur suivant) si d’autres objets existent dans le conteneur. Pour ce faire, nous créons une fonction d’assistance qui s’appelle elle-même à plusieurs reprises tant qu’un marqueur suivant se trouve dans la réponse listBlobsFlatSegment précédente.

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

Téléchargez les objets blob sur votre disque local à l’aide de [blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview).

Le code suivant télécharge l’objet blob chargé dans la section précédente et ajoute le suffixe « _DOWNLOADED » au nom de l’objet blob pour pouvoir voir les deux fichiers sur le disque local. 

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

### <a name="clean-up-resources"></a>Supprimer les ressources

Si vous n’avez plus besoin des objets blob chargés dans ce démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview). Cette méthode permet de supprimer les fichiers du conteneur également.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et le stockage blob Azure avec Java. 

> [!div class="nextstepaction"]
> [Code source du Kit de développement logiciel (SDK) de stockage pour Java V10](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [Référence de l’API](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [En savoir plus sur RxJava](https://github.com/ReactiveX/RxJava)