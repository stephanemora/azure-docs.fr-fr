---
title: Activer l’accès en lecture public pour les conteneurs et les objets blob dans Stockage Blob Azure | Microsoft Docs
description: Découvrez comment autoriser l’accès anonyme aux conteneurs et aux objets Blob et comment utiliser un programme pour y accéder.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 6293fc84969c4e246c05da4482f76142263db230
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985561"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob

Vous pouvez activer l’accès en lecture anonyme public pour un conteneur et ses objets blob dans Stockage Blob Azure. En procédant ainsi, vous pouvez accorder un accès en lecture seule à ces ressources sans partager votre clé de compte et sans exiger de signature d’accès partagé (SAP).

L’accès en lecture public est idéal dans les situations où vous voulez conférer à certains objets blob un accès en lecture anonyme permanent. Pour un contrôle plus précis, vous pouvez créer une signature d’accès partagé. Les signatures d’accès partagé vous permettent d’accorder un accès restreint avec différentes autorisations sur une période donnée. Pour plus d’informations sur la création de signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP) dans le Stockage Azure](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob

Par défaut, un conteneur et les objets blob qu’il contient sont accessibles uniquement par un utilisateur qui dispose des autorisations appropriées. Pour octroyer aux utilisateurs anonymes un accès en lecture à un conteneur et ses objets blob, vous pouvez configurer le niveau d’accès public du conteneur. Quand vous octroyez un accès public à un conteneur, les utilisateurs anonymes peuvent lire les objets blob d’un conteneur accessible publiquement sans avoir à autoriser la requête.

Vous pouvez configurer un conteneur avec les autorisations suivantes :

* **Aucun accès en lecture public :** seul le propriétaire du compte de stockage peut accéder au conteneur et à ses objets blob. Il s’agit de la configuration par défaut de tous les nouveaux conteneurs.
* **Accès en lecture public pour les objets blob uniquement :** les objets blob à l’intérieur du conteneur peuvent être lus via une requête anonyme, mais les données du conteneur ne sont pas disponibles. Les clients anonymes ne peuvent pas énumérer les objets blob présents dans le conteneur.
* **Public read access for container and its blobs (Accès en lecture public pour le conteneur et ses objets blob) :** toutes les données de conteneur et d’objets blob peuvent être lus via une requête anonyme. Les clients peuvent énumérer les objets blob présents dans le conteneur par une demande anonyme, mais ne peuvent pas énumérer les conteneurs présents dans le compte de stockage.

Vous pouvez définir les autorisations de conteneur par les moyens suivants :

* [Portail Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Interface de ligne de commande Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Par programmation, en utilisant l’une des bibliothèques clientes de stockage ou l’API REST

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Définir le niveau d’accès public du conteneur dans le portail Azure

Dans le [portail Azure](https://portal.azure.com), vous pouvez mettre à jour le niveau d’accès public pour un ou plusieurs conteneurs :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous l’élément **Service Blob** du panneau de menu, sélectionnez **Objets blob**.
1. Sélectionnez les conteneurs pour lesquels vous souhaitez définir le niveau d’accès public.
1. Utilisez le bouton **Modifier le niveau d’accès** pour afficher les paramètres d’accès public.
1. Sélectionnez le niveau d’accès public souhaité à partir du menu déroulant **Niveau d’accès public** et cliquez sur le bouton OK pour appliquer la modification aux conteneurs sélectionnés.

La capture d’écran suivante montre comment modifier le niveau d’accès public pour les conteneurs sélectionnés.

![Capture d’écran montrant comment définir le niveau d’accès public dans le portail](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Vous ne pouvez pas modifier le niveau d’accès public pour un objet blob individuel. Le niveau d’accès public est défini uniquement au niveau du conteneur.

### <a name="set-container-public-access-level-with-net"></a>Définir un niveau d’accès public de conteneur avec .NET

Pour définir les autorisations d’un conteneur en utilisant C# et la bibliothèque cliente de stockage pour .NET, vous devez d’abord récupérer les autorisations existantes du conteneur en appelant la méthode **GetPermissions**. Définissez ensuite la propriété **PublicAccess** de l’objet **BlobContainerPermissions** qui est retourné par la méthode **GetPermissions**. Pour finir, appelez la méthode **SetPermissions** avec les autorisations mises à jour.

Dans l’exemple suivant, nous définissons les autorisations du conteneur pour permettre un accès en lecture public complet. Pour autoriser un accès en lecture public pour les objets Blob uniquement, définissez la propriété **PublicAccess** sur **BlobContainerPublicAccessType.Blob**. Pour supprimer toutes les autorisations pour les utilisateurs anonymes, définissez la propriété sur **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Accéder anonymement aux conteneurs et aux objets Blob

Un client ayant un accès anonyme aux conteneurs et aux objets Blob peut utiliser des constructeurs qui ne nécessitent pas d’informations d’identification. Les exemples suivants illustrent différentes manières de référencer des conteneurs et objets blob de façon anonyme.

### <a name="create-an-anonymous-client-object"></a>Créer un objet de client anonyme

Vous pouvez créer un objet de client de service pour permettre un accès anonyme en spécifiant le point de terminaison du stockage d’objets blob associé au compte. Toutefois, vous devez également connaître le nom d’un conteneur attaché à ce compte qui est disponible pour un accès anonyme.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Référencer un conteneur de manière anonyme

Si vous disposez de l’URL permettant d’accéder à un conteneur accessible de manière anonyme, vous pouvez l’utiliser pour référencer directement le conteneur.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Référencer un objet Blob de façon anonyme

Si vous disposez de l’URL permettant d’accéder à un objet Blob accessible de manière anonyme, vous pouvez l’utiliser pour référencer directement l’objet Blob :

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Fonctionnalités accessibles aux utilisateurs anonymes

Le tableau suivant indique les opérations pouvant être appelées de façon anonyme quand un conteneur est configuré pour l’accès public.

| Opération REST | Accès en lecture public au conteneur | Accès en lecture public aux objets blob uniquement |
| --- | --- | --- |
| List Containers | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Create Container | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Get Container Properties | Requêtes anonymes autorisées | Requêtes autorisées uniquement |
| Get Container Metadata | Requêtes anonymes autorisées | Requêtes autorisées uniquement |
| Set Container Metadata | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Get Container ACL | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Set Container ACL | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Delete Container | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| List Blobs | Requêtes anonymes autorisées | Requêtes autorisées uniquement |
| Put Blob | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Get Blob | Requêtes anonymes autorisées | Requêtes anonymes autorisées |
| Get Blob Properties | Requêtes anonymes autorisées | Requêtes anonymes autorisées |
| Set Blob Properties | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Get Blob Metadata | Requêtes anonymes autorisées | Requêtes anonymes autorisées |
| Set Blob Metadata | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Put Block | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Get Block List (blocs validés uniquement) | Requêtes anonymes autorisées | Requêtes anonymes autorisées |
| Get Block List (blocs non validés uniquement ou tous les blocs) | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Put Block List | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Delete Blob | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Copie d'un objet blob | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Snapshot Blob | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Lease Blob | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Put Page | Requêtes autorisées uniquement | Requêtes autorisées uniquement |
| Get Page Ranges | Requêtes anonymes autorisées | Requêtes anonymes autorisées |
| Append Blob | Requêtes autorisées uniquement | Requêtes autorisées uniquement |

## <a name="next-steps"></a>Étapes suivantes

* [Authorization for the Azure Storage Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services) (Autorisation pour les services de stockage Azure)
* [Utilisation des signatures d’accès partagé (SAP)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)