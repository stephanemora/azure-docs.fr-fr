---
title: Résoudre les problèmes liés au connecteur DB2
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur DB2 dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f2a9e79fb3c01938eae52b6c34687943a9472024
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390566"
---
# <a name="troubleshoot-the-db2-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur DB2 dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Azure Database for PostgreSQL dans Azure Data Factory et Azure Synapse.

## <a name="error-code-db2driverrunfailed"></a>Code d’erreur : DB2DriverRunFailed

- **Message** : `Error thrown from driver. Sql code: '%code;'`

- **Cause** : Si le message d’erreur contient la chaîne « SQLSTATE=51002 SQLCODE=-805 », suivez le « conseil » sous [Copier des données à partir de DB2](./connector-db2.md#linked-service-properties).

- **Recommandation** :  Essayez de définir « NULLID » dans la propriété `packageCollection`.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
