---
title: Migrer d’Azure Media Services v2 vers v3 | Microsoft Docs
description: Cet article décrit les changements qui ont été introduits dans Azure Media Services v3 et montre les différences entre les deux versions.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, flux, diffusion, en direct, hors connexion
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/12/2018
ms.author: juliako
ms.openlocfilehash: a382af644d30f9f0ebb586273c982ef1766f50b0
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292044"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrer de Media Services v2 vers v3

> [!NOTE]
> La version la plus récente d’Azure Media Services est disponible en préversion et peut être appelée v3.

Cet article décrit les changements qui ont été introduits dans Azure Media Services v3 et montre les différences entre les deux versions.

## <a name="why-should-you-move-to-v3"></a>Pourquoi passer à v3 ?

### <a name="api-is-more-approachable"></a>L’API est plus accessible

*  v3 est basée sur une surface d’API unifiée qui expose des fonctionnalités de gestion et de fonctionnement qui s’appuient sur Azure Resource Manager. Vous pouvez utiliser les modèles Azure Resource Manager pour créer et déployer des transformations, des points de terminaison de streaming, des événements LiveEvent, etc.
* Document de spécification OpenAPI (Swagger).
* SDK disponibles pour .NET, .NET Core, Node.js, Python, Java, Ruby.
* Intégration d’Azure CLI.

### <a name="new-features"></a>Nouvelles fonctionnalités

* L’encodage prend désormais en charge l’ingestion HTTPS (entrée basée sur une URL).
* Les transformations sont nouvelles dans v3. Une transformation permet de partager des configurations, de créer des modèles Azure Resource Manager, et d’isoler les paramètres d’encodage pour un client ou un locataire spécifique. 
* Une ressource peut avoir plusieurs StreamingLocators, chacun avec des paramètres différents d’empaquetage dynamique et de chiffrement dynamique.
* La protection du contenu prend en charge les fonctionnalités à plusieurs clés. 
* L’aperçu LiveEvent prend en charge l’empaquetage dynamique et le chiffrement dynamique. Ceci permet la protection du contenu sur l’aperçu, ainsi que l’empaquetage DASH et HLS.
* LiveOuput est plus simple à utiliser que l’ancienne entité Program. 
* La prise en charge de RBAC sur les entités a été ajoutée.

## <a name="changes-from-v2"></a>Changements par rapport à la v2

* Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

    Pour les ressources créées avec la version v3, Media Services prend en charge le chiffrement de stockage côté serveur [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
    
* Les kits SDK Media Services sont découplés du kit SDK Stockage, ce qui donne davantage de contrôle sur le SDK Stockage utilisé et évite les problèmes de gestion des versions. 
* Dans Azure Media Services v3, toutes les vitesses d’encodage sont données en bits par seconde. Ceci diffère des préréglages de Media Encoder Standard de REST v2. Par exemple, un débit en bits dans v2 serait spécifié sous la forme « 128 », mais dans v3, il serait de « 128000 ». 
* AssetFiles, AccessPolicies, IngestManifests n’existent pas dans v3.
* ContentKey n’est plus une entité, propriété du StreamingLocator.
* La prise en charge de la grille d’événements remplace NotificationEndpoints.
* Certaines entités ont été renommées.

  * JobOutput remplace Task, qui fait maintenant simplement partie de Job.
  * LiveEvent remplace Channel.
  * LiveOutput remplace Program.
  * StreamingLocator remplace Locator.

## <a name="code-changes"></a>Modifications du code

### <a name="create-an-asset-and-upload-a-file"></a>Créer une ressource et charger un fichier 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Soumettre un travail

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Publier une ressource avec chiffrement AES 

#### <a name="v2"></a>v2

1. Créer une ContentKeyAuthorizationPolicyOption
2. Créer une ContentKeyAuthorizationPolicy
3. Créer une AssetDeliveryPolicy
4. Créer une ressource et charger du contenu OU soumettre un travail et utiliser une ressource en sortie
5. Associer une AssetDeliveryPolicy à un Asset
6. Créer une ContentKey
7. Attacher un ContentKey à un Asset
8. Créer une AccessPolicy
9. Créer un Locator

#### <a name="v3"></a>v3

1. Créer une stratégie de clé de contenu
2. Créer un Asset
3. Charger du contenu ou utiliser un Asset comme JobOutput
4. Créer un localisateur de diffusion en continu

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir combien il est facile de démarrer le codage et le streaming de fichiers vidéo, consultez [Diffuser des fichiers](stream-files-dotnet-quickstart.md). 

