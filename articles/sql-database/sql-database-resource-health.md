---
title: Utiliser Azure Resource Health pour effectuer le monitorage de l’intégrité de la base de données
description: Utiliser Azure Resource Health pour surveiller l’intégrité de SQL Database vous aide à diagnostiquer les problèmes et à accéder au support quand un problème Azure a une incidence sur vos ressources SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208872"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Utiliser Resource Health pour résoudre des problèmes de connectivité avec Azure SQL Database

## <a name="overview"></a>Vue d’ensemble

[Resource Health](../service-health/resource-health-overview.md#get-started) vous aide à diagnostiquer les problèmes et à accéder au support quand un problème Azure a une incidence sur vos ressources SQL. Il vous informe de l’intégrité (actuelle et passée) de vos ressources et vous aide à atténuer les problèmes. Resource Health propose un support technique dès lors que vous êtes confronté à des problèmes de service Azure et que vous avez besoin d’aide.

![Vue d’ensemble](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Contrôles d'intégrité

Resource Health détermine l’intégrité de votre ressource SQL en examinant la réussite et échec des connexions à la ressource. Actuellement, Resource Health pour votre ressource SQL DB examine uniquement les échecs de connexion dus à une erreur système et non à une erreur utilisateur. L’état d’intégrité de la ressource est mis à jour toutes les 1 à 2 minutes.

## <a name="health-states"></a>États d'intégrité

### <a name="available"></a>Disponible

Un état **Disponible** signifie que Resource Health n’a pas détecté d’échecs de connexions dus à des erreurs système sur votre ressource SQL.

![Disponible](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Détérioré

Un état **Détérioré** signifie que Resource Health a détecté une majorité de connexions établies, mais également des échecs. Il s’agit probables erreurs de connexion temporaires. Pour réduire l’impact des problèmes de connexion dus à des erreurs de connexion temporaires, implémentez une [logique de nouvelle tentative](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) dans votre code.

![Détérioré](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Non disponible

État **Indisponible** signifie que Resource Health a détecté des échecs de connexion cohérente dans votre ressource SQL. Si votre ressource reste dans cet état pendant une période prolongée, contactez le support technique.

![Non disponible](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

L’état d’intégrité **Inconnu** indique que Resource Health n’a reçu aucune information sur cette ressource depuis plus de 10 minutes. Même si cet état n’est pas une indication définitive de l’état de la ressource, il s’agit d’un point de données important dans le processus de dépannage. Si la ressource fonctionne comme prévu, son état devient Disponible après quelques minutes. Si vous rencontrez des problèmes avec la ressource, l’état d’intégrité Inconnu peut suggérer qu’un événement de la plateforme influe sur la ressource.

![Unknown](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informations d’historique

Vous pouvez accéder à jusqu’à 14 jours d’historique d’intégrité dans la section Historique de l’intégrité de Resource Health. La section contient également la raison de l’arrêt (le cas échéant) signalé par Resource Health. Actuellement, Azure indique le temps d’arrêt de votre ressource de base de données SQL avec une granularité de deux minutes. Le temps d’arrêt réel est probablement inférieur à une minute – 8 s en moyenne.

### <a name="downtime-reasons"></a>Raisons des temps d’arrêt

Lorsque votre base de données SQL est arrêté, une analyse est effectuée pour en déterminer la cause. Lorsqu’elle est disponible, la raison du temps d’arrêt est signalée dans la section Historique de l’intégrité de Resource Health. Les raisons des temps d’arrêt sont généralement publiées 30 minutes après un événement.

#### <a name="planned-maintenance"></a>Maintenance planifiée

L’infrastructure Azure effectue périodiquement une maintenance planifiée (mise à niveau des composants matériels ou logiciels dans le centre de données). Pendant la maintenance de la base de données, SQL peut mettre fin à des connexions existantes et en refuser de nouvelles. Les échecs de connexion rencontrés pendant une maintenance planifiée sont généralement temporaires. Une [logique de nouvelle tentative](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) permet d’en réduire l’impact. Si vous êtes toujours confronté à des erreurs de connexion, contactez le support technique.

#### <a name="reconfiguration"></a>Reconfiguration

Les reconfigurations sont considérées comme des conditions transitoires et prévues de temps à autre. Ces événements peuvent être déclenchés par les échecs d’équilibrage de charge ou de logiciel/matériel. Toute application de production client qui se connecte à une base de données cloud doit implémenter une [logique de nouvelle tentative](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) de connexion robuste, qui aide à remédier à ces situations et rend généralement les erreurs transparentes pour l’utilisateur final.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [logique de nouvelle tentative pour les erreurs temporaires](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL](./sql-database-connectivity-issues.md)
- En savoir plus sur la [configuration des alertes Resource Health](../service-health/resource-health-alert-arm-template-guide.md)
- Obtenir une vue d’ensemble de [Resource Health](../service-health/resource-health-overview.md)
- [FAQ sur Resource Health](../service-health/resource-health-faq.md)
