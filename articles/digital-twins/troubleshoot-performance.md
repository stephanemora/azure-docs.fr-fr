---
title: Résoudre les problèmes de performances
titleSuffix: Azure Digital Twins
description: Conseils pour la résolution des problèmes de performances d’une instance Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 5/11/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65b1873c1ac4f6ce9b06aad20dbb86e5f4613886
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116531"
---
# <a name="troubleshooting-azure-digital-twins-performance"></a>Résolution des problèmes rencontrés avec Azure Digital Twins : Performances

Si vous rencontrez un problème de délai ou d’autres problèmes de performances lors de l’utilisation d’Azure Digital Twins, essayez de résoudre le problème en vous aidant des conseils fournis dans cet article.

## <a name="isolate-the-source-of-the-delay"></a>Isoler la source du délai

Déterminez si le délai provient d’Azure Digital Twins ou d’un autre service de votre solution. Pour cela, vous pouvez utiliser la métrique **Latence de l’API** dans [Azure Monitor](../azure-monitor/essentials/quick-monitor-azure-resource.md) sur le portail Azure. Pour obtenir des instructions sur l’affichage des métriques Azure Monitor concernant une instance Azure Digital Twins, consultez [Résolution des problèmes : Afficher les métriques avec Azure Monitor](troubleshoot-metrics.md).

## <a name="check-regions"></a>Vérifier les régions

Si votre solution utilise Azure Digital Twins conjointement avec d’autres services Azure (comme Azure Functions), vérifiez la région de déploiement de chaque service. Si les services sont déployés dans des régions différentes, cela peut occasionner des délais dans votre solution. À moins que vous ne soyez intentionnellement en train de créer une solution distribuée, essayez de déployer toutes les instances de service dans la même région afin d’éviter d’introduire accidentellement des délais.

## <a name="leverage-logs"></a>Examiner les journaux

Azure Digital Twins peut collecter les journaux de votre instance de service pour vous aider à superviser ses performances et diverses autres données. Les journaux sont envoyés à [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) ou à votre système de stockage personnalisé. Pour activer la journalisation dans votre instance, suivez les instructions données dans [Résolution des problèmes : Configurer les diagnostics](troubleshoot-diagnostics.md). Vous pouvez analyser les horodatages des journaux pour mesurer les latences, évaluer leur cohérence et déterminer leur source.

## <a name="check-api-frequency"></a>Vérifier la fréquence de l’API

Un autre facteur susceptible d’impacter les performances est le temps nécessaire pour autoriser à nouveau les appels de l’API. Examinez la fréquence des appels de l’API. S’il y a un intervalle de plus de 15 minutes entre les appels, le système peut devoir réautoriser chaque appel, ce qui prend plus de temps. Vous pouvez éviter cela en ajoutant un minuteur ou un mécanisme similaire dans votre code pour vous assurer qu’Azure Digital Twins sera appelé au moins une fois toutes les 15 minutes.

## <a name="contact-support"></a>Contacter le support technique

Si vous rencontrez toujours des problèmes de performances après avoir suivi les étapes de dépannage ci-dessus, vous pouvez créer une demande de support dans Aide + Support Azure pour obtenir de l’aide supplémentaire. 

Procédez comme suit :

1. Collectez les [métriques](troubleshoot-metrics.md) et les [journaux](troubleshoot-diagnostics.md) de votre instance.
2. Accédez à [Aide + Support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) dans le portail Azure. À partir des invites, fournissez les détails de votre problème, consultez les solutions recommandées, partagez vos fichiers de métriques/journaux et envoyez toute autre information qui peut être utile à l’équipe de support technique pour vous aider à résoudre votre problème. Pour plus d’informations sur la création de demandes de support, consultez [Créer une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez d’autres façons de résoudre les problèmes rencontrés avec votre instance Azure Digital Twins dans les articles suivants :
* [Résolution des problèmes : Afficher les métriques avec Azure Monitor](troubleshoot-metrics.md)
* [Résolution des problèmes : Configurer les diagnostics](troubleshoot-diagnostics.md).
* [Résolution des problèmes : Configurer des alertes](troubleshoot-alerts.md).
* [Résolution des problèmes : Comprendre l’intégrité de vos ressources](troubleshoot-resource-health.md)
