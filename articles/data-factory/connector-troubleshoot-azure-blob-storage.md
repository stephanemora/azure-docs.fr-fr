---
title: Résoudre les problèmes liés au connecteur Stockage Blob Azure
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Stockage Blob Azure dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e9a1c085e3d1d88d0897d46b924c5a9a21c747a2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390571"
---
# <a name="troubleshoot-the-azure-blob-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Stockage Blob Azure dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Stockage Blob Azure dans Azure Data Factory et Azure Synapse.

## <a name="error-code-azurebloboperationfailed"></a>Code d’erreur : AzureBlobOperationFailed

- **Message** : « L’opération blob a échoué. ContainerName: %containerName;, path: %path;. »

- **Cause** : Problème lié au fonctionnement du stockage Blob.

- **Recommandation** :  Pour vérifier les détails de l’erreur, consultez [Codes d’erreur du stockage Blob](/rest/api/storageservices/blob-service-error-codes). Pour obtenir de l’aide, contactez l’équipe Stockage Blob.


## <a name="invalid-property-during-copy-activity"></a>Propriété non valide durant l’activité de copie

- **Message** : `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Cause** : Le type défini dans le jeu de données n’est pas cohérent avec le type de source/récepteur défini dans l’activité de copie.

- **Résolution** : Modifiez la définition JSON du jeu de données ou du pipeline pour rendre les types cohérents, puis réexécutez le déploiement.

## <a name="error-code-fipsmodeisnotsupport"></a>Code d’erreur : FIPSModeIsNotSupport

- **Message** : `Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **Cause** : La stratégie FIPS est activée sur la machine virtuelle sur laquelle le runtime d’intégration auto-hébergé a été installé.

- **Recommandation** : Désactivez le mode FIPS sur la machine virtuelle où le runtime d’intégration auto-hébergé a été installé. Windows ne recommande pas le mode FIPS.

## <a name="error-code-azureblobinvalidblocksize"></a>Code d’erreur : AzureBlobInvalidBlockSize

- **Message** : `Block size should between %minSize; MB and 100 MB.`

- **Cause** : La taille de bloc dépasse la limite de l’objet blob.

## <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>Code d’erreur : AzureStorageOperationFailedConcurrentWrite

- **Message** : `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **Cause** : Plusieurs exécutions d’activité de copie simultanées ou des applications écrivent dans le même fichier.

## <a name="error-code-azureappendblobconcurrentwriteconflict"></a>Code d’erreur : AzureAppendBlobConcurrentWriteConflict

- **Message** : `Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **Cause** : Plusieurs demandes d’écriture simultanées se produisent, ce qui provoque des conflits sur le contenu du fichier.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
