---
title: Tirer parti d’Azure Advisor pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les offres Azure Advisor pour Azure Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 3c3b995cdf4b27056b0714ae6f9f8bc29fad6302
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713092"
---
# <a name="azure-advisor-for-azure-communication-services"></a>Azure Advisor pour Azure Communication Services

[Azure Advisor](../../advisor/advisor-overview.md) est un conseiller cloud personnalisé qui vous aide à suivre les bonnes pratiques pour optimiser vos déploiements Azure. Azure Communication Services est intégré à Azure Advisor et publie des recommandations sur la façon d’optimiser vos ressources de communication. Vous pouvez voir ces recommandations dans le [portail Azure](https://portal.azure.com), dans le [panneau Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview). Les recommandations sont stockées dans votre [journal d’activité Azure](../../azure-monitor/essentials/platform-logs-overview.md) et vous pouvez configurer des alertes pour ces recommandations en utilisant des [modèles ARM](../../advisor/advisor-alerts-arm.md) ou le [portail](../../advisor/advisor-alerts-portal.md). 

## <a name="install-the-latest-sdks"></a>Installer les derniers SDK

Pour garantir l’ensemble des correctifs et mises à jour récents, nous vous recommandons de toujours installer les derniers SDK disponibles. Si une version plus récente du ou des SDK que vous utilisez est disponible, vous voyez une recommandation dans la catégorie **Performances** qui vous indique d’installer le dernier SDK.

![Exemple d’Azure Advisor montrant une recommandation pour mettre à jour le SDK du chat.](./media/advisor-chat-sdk-update-example.png)

Les SDK suivants sont pris en charge pour cette fonctionnalité, ainsi que toutes leurs langues prises en charge. Vous pouvez en savoir plus sur les [options de SDK](./sdk-options.md) disponibles.

* Appelant (client)
* Conversation
* sms
* Identité
* Numéros de téléphone
* Gestion
* Network Traversal
* Automatisation des appels

## <a name="next-steps"></a>Étapes suivantes

Les documents suivants peuvent vous intéresser :

- [Journalisation et diagnostics](./logging-and-diagnostics.md)
- [Métriques](./metrics.md)
