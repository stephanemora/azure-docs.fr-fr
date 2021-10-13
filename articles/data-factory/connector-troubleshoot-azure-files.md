---
title: Résoudre les problèmes du connecteur Azure Files
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes avec le connecteur Azure Files liés aux connecteurs dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 809bf30f1e11eaa7741ac0581881479d854ed4cb
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390582"
---
# <a name="troubleshoot-the-azure-files-connector-in-azure-data-factory-and-azure-synapse"></a>Dépanner le connecteur Azure Files dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Azure Files dans Azure Data Factory et Azure Synapse.

## <a name="error-code-azurefileoperationfailed"></a>Code d’erreur : AzureFileOperationFailed

- **Message** : `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Cause** : Problème lié au fonctionnement du stockage Azure Files.

- **Recommandation** :  Pour voir les détails de l’erreur, consultez [Aide Azure Files](/rest/api/storageservices/file-service-error-codes). Pour une aide supplémentaire, contactez l’équipe Azure Files.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
