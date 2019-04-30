---
title: Création et utilisation des fichiers de ressources - Azure Batch | Microsoft Docs
description: Découvrez comment créer des fichiers de ressources Azure Batch à partir de diverses sources d’entrée.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 679a1c60e44694bde86cafba21d7f1d2c6fb94d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616549"
---
# <a name="creating-and-using-resource-files"></a>Création et utilisation des fichiers de ressources

Une tâche Azure Batch nécessite souvent une forme de données à traiter. Fichiers de ressources sont les moyens de fournir les données à votre machine de virtuelle (VM) Batch via une tâche. Tous les types de tâches prennent en charge les fichiers de ressources : tâches, démarrer des tâches, les tâches de préparation de travail, tâches de lancement, etc. Cet article décrit quelques méthodes courantes sur la façon de créer des fichiers de ressources et les placer sur une machine virtuelle.  

Fichiers de ressources sont un mécanisme permettant de placer des données sur une machine virtuelle dans un lot, mais le type de données et son utilisation est flexible. Toutefois, il existe certains cas d’utilisation courants :

1. Approvisionner des fichiers communs sur chaque machine virtuelle à l’aide de fichiers de ressources sur une tâche de démarrage
1. Configurer les données d’entrée doivent être traitées par des tâches

Fichiers communs peut être, par exemple, les fichiers sur une tâche de démarrage utilisé pour installer des applications qui s’exécutent vos tâches. Données d’entrée peut être raw image ou des données vidéo ou toutes les informations doivent être traités par lot.

## <a name="types-of-resource-files"></a>Types de fichiers de ressources

Il existe plusieurs options disponibles pour générer des fichiers de ressources. Le processus de création de fichiers de ressources varie selon où sont stockées les données d’origine.

Options pour la création d’un fichier de ressources :

- [URL du conteneur de stockage](#storage-container-url): Génère un fichier de ressources à partir de n’importe quel conteneur de stockage dans Azure
- [Nom de conteneur de stockage](#storage-container-name): Génère un fichier de ressources à partir du nom d’un conteneur dans un compte de stockage Azure lié au lot
- [Point de terminaison Web](#web-endpoint): Génère un fichier de ressources à partir de n’importe quelle URL HTTP valide

### <a name="storage-container-url"></a>URL du conteneur de stockage

À l’aide d’une URL de conteneur de stockage signifie que vous pouvez accéder aux fichiers dans n’importe quel conteneur de stockage dans Azure. Avec les autorisations appropriées

Dans ce C# exemple, les fichiers ont déjà été chargés dans un conteneur de stockage Azure en tant que stockage d’objets blob. Pour accéder aux données nécessaires pour créer un fichier de ressources, nous devons d’abord accéder au conteneur de stockage.

Créer une signature d’accès partagé (SAP) URI avec les autorisations appropriées pour accéder au conteneur de stockage. Définir le délai d’expiration et les autorisations pour les associations de sécurité. Dans ce cas, aucune heure de début n’est spécifié, afin que la SAP devient valide immédiatement et expire deux heures après sa création.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Pour l’accès au conteneur, vous devez avoir les deux `Read` et `List` autorisations, tandis qu’avec l’accès aux objets blob, vous devez uniquement `Read` autorisation.

Une fois que les autorisations sont configurées, créez le jeton SAP et mettre en forme l’URL SAS pour l’accès au conteneur de stockage. À l’aide de l’URL SAS mis en forme pour le conteneur de stockage, de générer un fichier de ressources avec [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Une alternative à la génération d’une URL SAS consiste à activer l’accès en lecture anonyme public sur un conteneur et ses objets BLOB dans stockage Blob Azure. En procédant ainsi, vous pouvez accorder un accès en lecture seule à ces ressources sans partager votre clé de compte et sans nécessiter une SAP. Accès en lecture public est généralement utilisé pour les scénarios où vous souhaitez certains objets BLOB sont toujours disponibles pour l’accès en lecture anonyme. Si ce scénario est adapté à votre solution, consultez le [l’accès anonyme aux objets BLOB](../storage/blobs/storage-manage-access-to-resources.md) article pour en savoir plus sur la gestion de l’accès à vos données d’objet blob.

### <a name="storage-container-name"></a>Nom de conteneur de stockage

Au lieu de la configuration et la création d’une URL SAS, vous pouvez utiliser le nom de votre conteneur de stockage Azure à accéder à vos données d’objet blob. Le conteneur de stockage utilisé doit dans le compte de stockage Azure lié à votre compte Batch, appelé compte autostorage. En utilisant le nom de conteneur de stockage d’un compte autostorage vous permet d’ignorer la configuration et la création d’une URL SAS pour accéder à un conteneur de stockage.

Dans cet exemple, nous partons du principe que les données à utiliser pour la création de fichier de ressources sont déjà un compte de stockage Azure lié à votre compte Batch. Si vous n’avez pas un compte autostorage, consultez les étapes décrites dans [créer un compte Batch](batch-account-create-portal.md) pour plus d’informations sur la façon de créer et lier un compte.

En utilisant un compte de stockage lié, vous n’avez pas besoin créer et configurer une URL SAS à un conteneur de stockage. Au lieu de cela, indiquez le nom du conteneur de stockage dans votre compte de stockage lié.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Point de terminaison web

Données qui n’est pas téléchargées vers le stockage Azure peuvent toujours être utilisées pour créer des fichiers de ressources. Vous pouvez spécifier n’importe quelle URL HTTP valide contenant vos données d’entrée. L’URL est fournie à l’API Batch, et ensuite les données sont utilisées pour créer un fichier de ressources.

Dans l’exemple suivant C# exemple, les données d’entrée est hébergé sur le point de terminaison GitHub fictive. L’API récupère le fichier de point de terminaison web valide et génère un fichier de ressources à être consommé par votre tâche. Aucune information d’identification n’est nécessaires pour ce scénario.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Conseils et suggestions

Chaque tâche Azure Batch utilise des fichiers différemment, c’est pourquoi le lot comporte des options disponibles pour la gestion des fichiers sur des tâches. Les scénarios suivants ne sont pas censés être exhaustif, mais au lieu de cela aborder quelques situations courantes et fournit des recommandations.

### <a name="many-resource-files"></a>Nombre de fichiers de ressources

Votre traitement par lot peut contenir plusieurs tâches qui utilisent tous les fichiers commun. Si les fichiers de tâches courantes sont partagés entre de nombreuses tâches, à l’aide d’un package d’application pour contenir les fichiers au lieu d’utiliser des fichiers de ressources peut-être une meilleure option. Optimise les packages d’application pour la vitesse de téléchargement. En outre, les données dans les packages d’application sont mise en cache entre les tâches, si vos fichiers de la tâche ne changent pas souvent, les packages d’application peut donc être adapté à votre solution. Packages d’application, vous n’avez pas besoin gérer plusieurs fichiers de ressources manuellement ou de générer des URL SAS pour accéder aux fichiers dans le stockage Azure. Batch fonctionne en arrière-plan avec le stockage Azure pour stocker et déployer des packages d’application aux nœuds de calcul.

Si chaque tâche comporte de nombreux fichiers uniques à cette tâche, les fichiers de ressources sont principalement probablement la meilleure option. Tâches qui utilisent souvent des fichiers uniques doivent être mis à jour ou remplacés, qui n’est pas aussi facile à faire avec le contenu de packages d’application. Fichiers de ressources fournissent une flexibilité supplémentaire pour la mise à jour, ajouter ou modifier des fichiers individuels.

### <a name="number-of-resource-files-per-task"></a>Nombre de fichiers de ressources par tâche

S’il existe plusieurs fichiers de centaines de ressources spécifiés sur une tâche, Batch peut rejeter la tâche pour qu’il soit trop grande. Il est préférable de conserver vos tâches petites en réduisant le nombre de fichiers de ressources sur la tâche elle-même.

S’il n’existe aucun moyen pour réduire le nombre de fichiers de votre tâche aux besoins, vous pouvez optimiser la tâche en créant un fichier de ressources unique qui fait référence à un conteneur de stockage de fichiers de ressources. Pour ce faire, placez vos fichiers de ressources dans un conteneur de stockage Azure et utiliser les différents modes de « Conteneur » des fichiers de ressources. Utilisez les options de préfixe d’objet blob pour spécifier des ensembles de fichiers à télécharger pour vos tâches.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [packages d’application](batch-application-packages.md) comme alternative aux fichiers de ressources.
- Pour plus d’informations sur l’utilisation de conteneurs pour les fichiers de ressources, consultez [les charges de travail conteneur](batch-docker-container-workloads.md).
- Pour apprendre à rassembler et enregistrer les données de sortie à partir de vos tâches, consultez [conserver la sortie des travaux et des tâches](batch-task-output.md).
- Découvrez les [outils et API Batch](batch-apis-tools.md) disponibles pour créer des solutions Batch.