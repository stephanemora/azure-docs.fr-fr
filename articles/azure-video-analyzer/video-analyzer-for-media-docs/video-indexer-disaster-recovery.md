---
title: Basculement et reprise d’activité avec Azure Video Analyzer for Media (anciennement Video Indexer)
titleSuffix: Azure Media Services
description: Découvrez comment basculer vers un compte Azure Video Analyzer for Media (anciennement Video Indexer) secondaire en cas de défaillance d’un centre de données régional ou de sinistre.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: e3f1f09f58d79ce3927b137cae20db5e1d55af14
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385608"
---
# <a name="video-analyzer-for-media-failover-and-disaster-recovery"></a>Basculement et reprise d’activité avec Azure Video Analyzer for Media

Azure Video Analyzer for Media (anciennement Video Indexer) ne fournit pas de basculement instantané du service en cas de panne ou de défaillance d’un centre de données régional. Cet article explique comment configurer votre environnement pour un basculement afin de garantir une disponibilité optimale pour les applications et réduire le temps de récupération en cas de sinistre.

Nous vous recommandons de configurer la continuité d’activité et reprise d’activité (BCDR) dans des paires régionales pour tirer parti des stratégies d’isolation et de disponibilité d’Azure. Pour plus d’informations, consultez [Régions jumelées Azure](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Prérequis

Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un essai gratuit Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Basculer vers un site secondaire

Pour implémenter BCDR, vous devez disposer de deux comptes Video Analyzer for Media afin de gérer la redondance.

1. Créez deux comptes Video Analyzer for Media connectés à Azure (voir [Créer un analyseur vidéo pour le compte multimédia](connect-to-azure.md)). Créez un compte pour votre région primaire et un autre pour la région Azure jumelée.
1. En cas de défaillance dans votre région primaire, basculez sur l’indexation à l’aide du compte secondaire.

> [!TIP]
> Vous pouvez automatiser BCDR en configurant des alertes de journal d’activité pour les notifications d’intégrité du service, comme mentionné dans [Créer des alertes de journal d'activité sur les notifications de service](../../service-health/alerts-activity-log-service-notifications-portal.md).

Pour plus d’informations sur l’utilisation de plusieurs locataires, consultez [Gérer plusieurs locataires](manage-multiple-tenants.md). Pour mettre en œuvre BCDR, choisissez une des deux options suivantes : [Compte Video Analyzer for Media par abonné](./manage-multiple-tenants.md#video-analyzer-for-media-account-per-tenant) ou [Abonnement Azure par abonné](./manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Étapes suivantes

[Gérer un compte Video Analyzer for Media connecté à Azure](manage-account-connected-to-azure.md).
