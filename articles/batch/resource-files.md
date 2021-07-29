---
title: Création et utilisation de fichiers de ressources
description: Découvrez comment créer des fichiers de ressources Batch à partir de diverses sources d’entrée. Cet article décrit quelques méthodes courantes pour les créer et les placer sur une machine virtuelle.
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: 1ef8cde8c345cebeb166cddd67a1951d71eea810
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110467690"
---
# <a name="creating-and-using-resource-files"></a>Création et utilisation de fichiers de ressources

Une tâche Azure Batch requiert généralement une certaine forme de données à traiter. Les fichiers de ressources permettent de fournir ces données à votre machine virtuelle Batch via une tâche. Tous les types de tâches prennent en charge les fichiers de ressources : tâches, tâches de démarrage, tâches de préparation de travail, tâches de mise en production de travail, etc. Cet article décrit quelques méthodes courantes pour créer des fichiers de ressources et les placer sur une machine virtuelle.  

Les fichiers de ressources permettent de placer des données sur une machine virtuelle dans Batch, mais le type de données et leur utilisation sont flexibles. Il existe toutefois des cas d’utilisation courants :

- Approvisionner des fichiers communs sur chaque machine virtuelle à l’aide de fichiers de ressources sur une tâche de démarrage.
- Approvisionner des données d’entrée devant être traitées par des tâches.

Les fichiers communs peuvent être, par exemple, des fichiers sur une tâche de démarrage utilisée pour installer des applications exécutées par vos tâches. Les données d’entrée peuvent être des données brutes d’image ou de vidéo, ou toute information devant être traitée par Batch.

## <a name="types-of-resource-files"></a>Types de fichiers de ressources

Différentes options sont disponibles pour générer des fichiers de ressources, chacune avec ses propres [méthodes](/dotnet/api/microsoft.azure.batch.resourcefile#methods). Le processus de création des fichiers de ressources varie en fonction de l’emplacement de stockage des données d’origine et de la nécessité de créer plusieurs fichiers.

- [URL de conteneur de stockage](#storage-container-url) : génère des fichiers de ressources à partir d’un conteneur de stockage dans Azure.
- [Nom de conteneur de stockage](#storage-container-name-autostorage) : génère des fichiers de ressources à partir du nom d’un conteneur dans un compte de Stockage Azure lié à votre compte Batch (le compte d’autostorage).
- [Fichier de ressources unique à partir du point de terminaison Web](#single-resource-file-from-web-endpoint) : génère un fichier de ressources unique à partir de n’importe quelle URL HTTP valide.

### <a name="storage-container-url"></a>URL de conteneur de stockage

L’utilisation d’une URL de conteneur de stockage, avec les autorisations appropriées, signifie que vous pouvez accéder aux fichiers d’un quelconque conteneur de stockage dans Azure.

Dans cet exemple C#, les fichiers ont déjà été chargés dans un conteneur de stockage Azure en tant que stockage d’objets blob. Pour accéder aux données nécessaires à la création d’un fichier de ressources, nous devons tout d’abord accéder au conteneur de stockage.

Créez un URI de signature d’accès partagé (SAS) avec les autorisations appropriées pour accéder au conteneur de stockage. Définissez le délai d’expiration et les autorisations pour la SAS. Dans ce cas, aucune heure de début n’est spécifiée afin que la SAS soit valide immédiatement et expire deux heures après sa génération.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Pour l’accès au conteneur, vous devez disposer des autorisations `Read` et `List`, tandis que pour l’accès aux objets blob, vous n’avez besoin que de l’autorisation `Read`.

Une fois que les autorisations sont configurées, créez le jeton SAS et mettez en forme l’URL SAS pour l’accès au conteneur de stockage. À l’aide de l’URL SAS mise en forme pour le conteneur de stockage, générez un fichier de ressources avec [FromStorageContainerUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Si vous le souhaitez, vous pouvez utiliser la propriété [blobprefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) pour limiter les téléchargements aux objets blob dont le nom commence par un préfixe spécifié :

```csharp
ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl, blobPrefix = yourPrefix);
```

Une alternative à la génération d’une URL SAS consiste à activer l’accès en lecture anonyme public pour un conteneur et ses objets blob dans Stockage Blob Azure. En procédant ainsi, vous pouvez accorder un accès en lecture seule à ces ressources sans partager votre clé de compte et sans exiger de signature d’accès partagé. L’accès en lecture public est généralement utilisé dans les situations où vous voulez conférer à certains objets blob un accès en lecture anonyme permanent. Si ce scénario convient à votre solution, consultez l’article [Accès anonyme aux objets Blob](../storage/blobs/anonymous-read-access-configure.md) article pour en savoir plus sur la gestion de l’accès à vos données d’objet blob.

### <a name="storage-container-name-autostorage"></a>Nom du conteneur de stockage (autostorage)

Plutôt que de configurer et de créer une URL SAS, vous pouvez utiliser le nom de votre conteneur de stockage Azure pour accéder à vos données d’objet blob. Le conteneur de stockage utilisé doit se trouver dans le compte de stockage Azure lié à votre compte Batch, également appelé *compte autostorage*. L’utilisation du conteneur « autostorage » vous permet de contourner la configuration et la création d’une URL SAS pour accéder à un conteneur de stockage. Indiquez plutôt le nom du conteneur de stockage dans votre compte de stockage lié.

Si vous n’avez pas de compte « autostorage », consultez les étapes décrites dans [Créer un compte Batch](batch-account-create-portal.md) pour plus d’informations sur la création et la liaison d’un compte de stockage.

L’exemple suivant utilise [AutoStorageContainer](/dotnet/api/microsoft.azure.batch.resourcefile.fromautostoragecontainer) pour générer le fichier à partir des données du compte autostorage.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

Comme avec une URL de conteneur de stockage, vous pouvez utiliser la propriété [blobprefix](/dotnet/api/microsoft.azure.batch.resourcefile.blobprefix) pour spécifier quels objets blob seront téléchargés :

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName, blobPrefix = yourPrefix);
```

### <a name="single-resource-file-from-web-endpoint"></a>Fichier de ressources unique à partir d’un point de terminaison web

Pour créer un fichier de ressources unique, vous pouvez spécifier une URL HTTP valide contenant vos données d’entrée. L’URL est fournie à l’API Batch, et les données sont ensuite utilisées pour créer un fichier de ressources. Cette méthode peut être utilisée si les données pour créer votre fichier de ressources se trouvent dans le Stockage Azure ou dans tout autre emplacement web, tel qu’un point de terminaison GitHub.

L’exemple suivant utilise [FromUrl](/dotnet/api/microsoft.azure.batch.resourcefile.fromurl) pour récupérer le fichier à partir d’une chaîne qui contient une URL valide, puis génère un fichier de ressources qui sera utilisé par votre tâche. Aucune information d’identification n’est nécessaire pour ce scénario. (Les informations d’identification sont requises si vous utilisez le stockage d’objets blob, sauf si l’accès en lecture public est activé sur le conteneur d’objets blob.)

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourURL, filePath);
```

Vous pouvez également utiliser une chaîne que vous définissez en tant qu’URL (ou une combinaison de chaînes qui, ensemble, crée l’URL complète de votre fichier).

```csharp
ResourceFile inputFile = ResourceFile.FromUrl(yourDomain + yourFile, filePath);
```

## <a name="tips-and-suggestions"></a>Conseils et suggestions

Les tâches Azure Batch peuvent utiliser des fichiers de nombreuses façons, ce qui explique pourquoi Batch fournit diverses options pour la gestion des fichiers sur les tâches. Les scénarios suivants ne sont pas censés être exhaustifs, mais couvrent quelques situations courantes et fournissent des suggestions.

### <a name="many-resource-files"></a>Plusieurs fichiers de ressources

Si les fichiers de tâches communs sont partagés entre de nombreuses tâches dans votre tâche Batch, vous pouvez utiliser un [package d’application](batch-application-packages.md) pour contenir ces fichiers. Les packages d’application permettent l’optimisation de la vitesse de téléchargement et les données des packages d’application sont mises en cache entre les tâches. Grâce aux packages d’application, vous n’avez pas besoin de gérer manuellement plusieurs fichiers de ressources ni de générer des URL SAS pour accéder aux fichiers dans le Stockage Azure. Batch fonctionne en arrière-plan avec le Stockage Azure pour stocker et déployer des packages d’application sur des nœuds de calcul. Si vos fichiers de tâches ne changent pas souvent, les packages d’application peuvent être adaptés à votre solution.

Si chacune de vos tâches comporte de nombreux fichiers uniques à celle-ci, les fichiers de ressources sont probablement la meilleure option. Les tâches qui utilisent des fichiers uniques doivent généralement être mises à jour ou remplacées, ce qui n’est pas si simple avec le contenu de package d’application. Les fichiers de ressources offrent une flexibilité supplémentaire pour la mise à jour, l’ajout ou la modification de fichiers individuels.

### <a name="number-of-resource-files-per-task"></a>Nombre de fichiers de ressources par tâche

Quand une tâche spécifie plusieurs centaines de fichiers de ressources, Batch peut rejeter la tâche car trop importante. Des petites tâches sont préférables, en réduisant le nombre de fichiers de ressources sur la tâche elle-même.

S’il est impossible de réduire le nombre de fichiers nécessaires à votre tâche, vous pouvez optimiser la tâche en créant un fichier de ressources unique qui fait référence à un conteneur de stockage de fichiers de ressources. Pour ce faire, placez vos fichiers de ressources dans un conteneur de stockage Azure et utilisez l’une des méthodes décrites ci-dessus pour générer des fichiers de ressources en fonction des besoins.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [packages d’application](batch-application-packages.md) comme alternative aux fichiers de ressources.
- En savoir plus sur [l’utilisation de conteneurs](batch-docker-container-workloads.md) pour les fichiers de ressources.
- Découvrez comment [rassembler et enregistrer les données de sortie de vos tâches](batch-task-output.md).
- Découvrez les [outils et API Batch](batch-apis-tools.md) disponibles pour créer des solutions Batch.
