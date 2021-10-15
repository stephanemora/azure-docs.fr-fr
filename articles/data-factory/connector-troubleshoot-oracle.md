---
title: Résoudre les problèmes liés au connecteur Oracle
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Oracle dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0450faa94af3c01fccebc618c5f91094447116f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390574"
---
# <a name="troubleshoot-the-oracle-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Oracle dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Oracle dans Azure Data Factory et Azure Synapse.

## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Code d’erreur : ArgumentOutOfRangeException

- **Message** : `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Cause** : Dans Azure Data Factory et les pipelines Synapse, les valeurs DateTime sont prises en charge dans la plage comprise entre 0001-01-01 00:00:00 et 9999-12-31 23:59:59. Cependant, Oracle prend en charge une plage plus étendue de valeurs DateTime (comme les siècles av. J-C ou min/s>59), ce qui entraîne une défaillance.

- **Recommandation** : 

    Pour voir si la valeur dans Oracle est comprise dans la plage prise en charge, exécutez `select dump(<column name>)`. 

    Pour connaître la séquence d’octets dans le résultat, consultez [Comment les dates sont-elles stockées dans Oracle ?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
