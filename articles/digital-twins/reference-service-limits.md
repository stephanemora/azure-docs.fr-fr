---
title: Limites du service
titleSuffix: Azure Digital Twins
description: Graphique présentant les limites du service Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728017"
---
# <a name="azure-digital-twins-service-limits"></a>Limites du service Azure Digital Twins

Il s’agit des limites de service d’Azure Digital Twins.

> [!NOTE]
> Certaines zones de ce service ont des limites ajustables. Cela est représenté dans les tableaux ci-dessous avec la colonne *Ajustable ?* . Quand la limite peut être réglée, la valeur *Ajustable ?* est *Oui*.
>
> Si votre entreprise nécessite de déclencher une limite ou un quota ajustable au-delà de la limite par défaut, vous pouvez demander des ressources supplémentaires en [ouvrant un ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-by-type"></a>Limites par type

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Gestion des limites

Lorsqu’une limite est atteinte, le service limite les requêtes supplémentaires. Cela engendre une réponse d’erreur 429 pour ces requêtes.

Voici quelques recommandations relatives aux limites.
* **Utilisez la logique de nouvelle tentative.** Les [kits de développement logiciel (SDK) Azure Digital Twins](how-to-use-apis-sdks.md) implémentent la logique de nouvelle tentative pour les requêtes ayant échoué. Par conséquent, si vous travaillez avec un kit de développement logiciel (SDK) fourni, elle est déjà intégrée. Sinon, envisagez d’implémenter une logique de nouvelle tentative dans votre propre application. Le service renvoie un en-tête `Retry-After` dans la réponse d’échec, que vous pouvez utiliser pour déterminer la durée d’attente avant une nouvelle tentative.
* **Utilisez des seuils et des notifications pour signaler les limites.** Certaines des limites de service pour Azure Digital Twins ont des [métriques](troubleshoot-metrics.md) correspondantes qui peuvent être utilisées pour suivre l’utilisation dans ces domaines. Pour configurer des seuils et une alerte sur n’importe quelle métrique lorsqu’un seuil est proche, consultez les instructions dans [*Résolution des problèmes : Configurer des alertes*](troubleshoot-alerts.md). Pour configurer des notifications pour d’autres limites où les métriques ne sont pas fournies, envisagez d’implémenter cette logique dans votre propre code d’application.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la version actuelle d’Azure Digital Twins dans la vue d’ensemble du service :
* [*Vue d’ensemble : Qu’est-ce qu’Azure Digital Twins ?*](overview.md)
