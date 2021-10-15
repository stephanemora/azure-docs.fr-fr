---
title: Résoudre les problèmes liés au connecteur Format ORC
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Format ORC dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 973322c2ec7747fc5233eb897b302bfc910fee1b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390617"
---
# <a name="troubleshoot-the-orc-format-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Format ORC dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Format ORC dans Azure Data Factory et Azure Synapse.

## <a name="error-code-orcjavainvocationexception"></a>Code d’erreur : OrcJavaInvocationException

- **Message** : `An error occurred when invoking Java, message: %javaException;.`
- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

    | Analyse de la cause                                               | Recommandation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Lorsque le message d’erreur contient les chaînes « java.lang.OutOfMemory », « Java heap space » et « doubleCapacity », il s’agit généralement d’un problème de gestion de la mémoire dans une ancienne version du runtime d’intégration. | Si vous utilisez des runtime d’intégration auto-hébergés, nous vous recommandons une mise à niveau vers la version la plus récente. |
    | Lorsque le message d’erreur contient la chaîne « java.lang.OutOfMemory », le runtime d’intégration ne dispose pas de suffisamment de ressources pour traiter les fichiers. | Limitez les exécutions simultanées sur le runtime d’intégration. Pour les runtimes d’intégration auto-hébergés, effectuez un scale-up vers un ordinateur puissant doté d’une mémoire égale ou supérieure à 8 Go. |
    |Lorsque le message d’erreur contient la chaîne « NullPointerReference », cela peut être dû à une erreur temporaire. | Retentez l’opération. Si le problème persiste, contactez le support technique. |
    | Lorsque le message d’erreur contient la chaîne « BufferOverflowException », cela peut être dû à une erreur temporaire. | Retentez l’opération. Si le problème persiste, contactez le support technique. |
    | Lorsque le message d’erreur contient la chaîne « java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text », il peut s’agir d’un problème de conversion de type au sein du runtime Java. En règle générale, cela signifie que les données sources ne peuvent pas être gérées correctement dans le runtime Java. | Il s’agit d’un problème de données. Essayez d’utiliser une chaîne au lieu de char ou de varchar dans des données au format ORC. |

## <a name="error-code-orcdatetimeexceedlimit"></a>Code d’erreur : OrcDateTimeExceedLimit

- **Message** : `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Cause** : Si la valeur datetime est « 0001-01-01 00:00:00 », cela peut être dû à la différence entre le [calendrier julien et le calendrier grégorien](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Recommandation** :  Vérifiez la valeur des graduations et évitez d’utiliser la valeur DateTime « 0001-01-01 00:00:00 ».

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
