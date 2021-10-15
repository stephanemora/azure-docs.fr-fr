---
title: Résoudre les problèmes liés au connecteur Stockage Table Azure
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Stockage Table Azure dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e99a31f4ca0380d8bbc12f941248d745e0c7f74c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390567"
---
# <a name="troubleshoot-the-azure-table-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Stockage Table Azure dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Stockage Table Azure dans Azure Data Factory et Azure Synapse.

## <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Code d’erreur : AzureTableDuplicateColumnsFromSource

- **Message** : `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Cause** : Des colonnes sources dupliquées peuvent se produire pour l’une des raisons suivantes :
   * Vous utilisez la base de données en tant que source et vous avez appliqué des jointures de table.
   * Vous avez des fichiers CSV non structurés avec des noms de colonnes dupliqués dans la ligne d’en-tête.

- **Recommandation** :  Vérifiez et corrigez les colonnes sources, si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
