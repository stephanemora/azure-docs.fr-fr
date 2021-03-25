---
title: Intégration de Microsoft 365 Defender avec Azure Sentinel | Microsoft Docs
description: Découvrez comment l’intégration de Microsoft 365 Defender (M365D) avec Azure Sentinel vous offre la possibilité d’utiliser Azure Sentinel comme votre file d’attente d’incidents universelle tout en préservant la puissance de M365D pour vous aider à étudier les incidents de sécurité liés à Microsoft 365 (M365), et également comment ingérer les données de repérage avancées des composants Defender dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743242"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Intégration de Microsoft 365 Defender avec Azure Sentinel

> [!IMPORTANT]
> Le connecteur Microsoft 365 Defender est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** était anciennement connu sous le nom de **Protection Microsoft contre les menaces** ou **MTP**.
>
> **Microsoft Defender for Endpoint** était précédemment appelé **Microsoft Defender Advanced Threat Protection**, ou **MDATP**.
>
> Vous pouvez voir les anciens noms encore en cours d’utilisation pendant un certain temps.

## <a name="incident-integration"></a>Intégration d’incidents

L’intégration d’incidents [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) d’Azure Sentinel vous permet de diffuser tous les incidents M365D dans Azure Sentinel et de les garder synchronisés entre les deux portails. Les incidents de M365D (anciennement Protection Microsoft contre les menaces) incluent l’ensemble des alertes, entités et informations pertinentes associées, ce qui vous offre suffisamment de contexte pour effectuer un triage et une investigation préliminaire dans Azure Sentinel. Une fois dans Sentinel, les incidents restent synchronisés de manière bidirectionnelle avec M365D, ce qui vous permet de tirer parti des avantages des deux portails dans votre investigation sur l’incident.

Cette intégration confère aux incidents de sécurité Microsoft 365 la visibilité nécessaire pour leur gestion à partir d’Azure Sentinel dans le cadre de la file d’attente des incidents principale de toute l’organisation, ce qui vous permet de voir les incidents M365, ainsi que de les corréler avec ceux de tous vos autres systèmes locaux et cloud. En même temps, elle vous permet de bénéficier de la puissance et des capacités uniques de M365D pour des investigations approfondies, ainsi que d’une expérience spécifique de M365 dans tout l’écosystème de M365. M365 Defender enrichit et regroupe les alertes de plusieurs produits M365, ce qui a pour effet de réduire la taille de la file d’attente des incidents de SOC et de raccourcir le temps de résolution. Les services de composants qui font partie de la pile M365 Defender sont les suivants :

- **Microsoft Defender pour Endpoint** (MDE, anciennement MDATP)
- **Microsoft Defender pour Identity** (MDI, anciennement AATP)
- **Microsoft Defender pour Office 365** (MDO, anciennement O365ATP)
- **Microsoft Cloud App Security** (MCAS)

En plus de collecter des alertes à partir de ces composants, M365 Defender génère ses propres alertes. Il crée des incidents à partir de toutes ces alertes et les envoie à Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Cas d’utilisation et scénarios courants

- Connexion en un clic d’incidents M365 Defender, y compris toutes les alertes et entités des composants M365 Defender, dans Azure Sentinel.

- Synchronisation bidirectionnelle entre les incidents Sentinel et M365D sur l’état, le propriétaire et le motif de clôture.

- Exploitation des fonctionnalités de regroupement et d’enrichissement des alertes M365 Defender dans Azure Sentinel, permettant de réduire le temps de résolution.

- Lien ciblé en contexte entre un incident Azure Sentinel et un incident M365 Defender parallèle pour faciliter les investigations sur les deux portails.

### <a name="connecting-to-microsoft-365-defender"></a>Connexion à Microsoft 365 Defender

Une fois que vous avez activé le connecteur de données Microsoft 365 Defender pour [collecter les incidents et les alertes](connect-microsoft-365-defender.md), les incidents M365D s’affichent dans la file d’attente des incidents d’Azure Sentinel, avec **Microsoft 365 Defender** dans le champ **Nom du produit**, peu après qu’ils ont été générés dans M365 Defender.
- Jusqu’à 10 minutes peuvent s’écouler entre le moment où un incident est généré dans M365 Defender et le moment où il apparaît dans Azure Sentinel.

- Les incidents sont ingérés et synchronisés sans frais supplémentaires.

Une fois l’intégration de M365 Defender connectée, les connecteurs d’alerte de tous les composants (MDE, MDI, MDO, MCAS) sont automatiquement connectés en arrière-plan s’ils ne l’étaient pas encore. Si des licences de composants ont été achetées après la connexion de M365 Defender, les alertes et les incidents du nouveau produit sont toujours transmis à Azure Sentinel sans que cela nécessite une configuration ou des frais supplémentaires.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>Incidents M365 Defender et règles de création d’incident Microsoft

- Les incidents générés par M365 Defender sur la base d’alertes provenant de produits de sécurité M365 sont créés à l’aide d’une logique M365 personnalisée.

- Les règles de création d’incident Microsoft dans Azure Sentinel créent également des incidents à partir des mêmes alertes, en utilisant une logique Azure Sentinel personnalisée (différente).

- L’utilisation conjointe des deux mécanismes est entièrement prise en charge, et cette configuration permet de faciliter la transition vers la nouvelle logique de création d’incident de M365 Defender. Toutefois, cela crée des **incidents en double** pour les mêmes alertes.

- Afin d’éviter de créer des incidents en double pour les mêmes alertes, nous recommandons aux clients de désactiver toutes les **règles de création d’incident Microsoft** pour les produits M365 (MDE, MDI et MDO -voir MCAS ci-dessous) lors de la connexion à M365 Defender. Pour ce faire, activez la case à cocher correspondante dans la page du connecteur. N’oubliez pas que, si vous procédez de la sorte, les filtres appliqués par les règles de création d’incident ne seront pas appliqués à l’intégration d’incidents M365 Defender.

- Pour les alertes Microsoft Cloud App Security (MCAS), certains types d’alerte ne sont pas intégrés actuellement à M365 Defender. Pour vous assurer de recevoir toujours des incidents pour toutes les alertes MCAS, vous devez conserver ou créer des **règles de création d’incident Microsoft** pour les types d’alerte *non intégrés* à M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Utilisation d’incidents M365 Defender dans Azure Sentinel et synchronisation bidirectionnelle

Les incidents M365 Defender s’affichent dans la file d’attente des incidents d’Azure Sentinel avec le nom de produit **Microsoft 365 Defender**, ainsi qu’avec des détails et fonctionnalités similaires à ceux d’autres incidents de Sentinel. Chaque incident contient un lien vers l’incident parallèle dans le portail M365 Defender.

À mesure que l’incident évolue dans M365 Defender et qu’un plus grand nombre d’alertes ou d’entités y sont ajoutés, l’incident Azure Sentinel est mis à jour en conséquence.

Les modifications apportées à l’état, au motif de clôture ou à l’affectation d’un incident M365, tant dans M365D que dans Azure Sentinel, sont également mises à jour en conséquence dans la file d’attente des incidents de l’autre. La synchronisation aura lieu dans les deux portails immédiatement après l’application de la modification de l’incident. Une actualisation peut être nécessaire pour voir les dernières modifications.

Dans M365 Defender, toutes les alertes d’un incident peuvent être transférées vers un autre, ce qui a pour effet de fusionner les incidents. Dans ce cas, les incidents Azure Sentinel reflètent les modifications. Un incident contiendra toutes les alertes des incidents d’origine, et l’autre sera automatiquement clos, avec ajout de l’étiquette « redirigé ».

> [!NOTE]
> Les incidents dans Azure Sentinel peuvent contenir jusqu’à 150 alertes. Les incidents M365D peuvent en contenir davantage. Si un incident M365D contenant plus de 150 alertes est synchronisé sur Azure Sentinel, l’incident Sentinel indique la présence de « 150 + » alertes et fournit un lien vers l’incident parallèle dans M365D, où il est possible de voir l’ensemble complet d’alertes.

## <a name="advanced-hunting-event-collection"></a>Collecte d’événements de repérage avancé

Le connecteur Microsoft 365 Defender vous permet de diffuser des événements de **repérage avancé** (un type de données d’événement brutes) de Microsoft 365 Defender vers Azure Sentinel. Vous pouvez actuellement collecter des événements de [repérage avancé](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) de [Microsoft Defender pour Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) et les diffuser directement vers des tables dédiées dans votre espace de travail Azure Sentinel. Ces tables sont générées sur le même schéma que celui utilisé dans le portail Microsoft 365 Defender, ce qui vous donne un accès complet à l’ensemble des événements de repérage avancé, et vous permet d’effectuer les opérations suivantes :

- Copier facilement vos requêtes de repérage avancé Microsoft Defender pour Endpoint existantes dans Azure Sentinel.

- Utiliser les journaux d’événements bruts afin de fournir des insights supplémentaires pour vos alertes, le repérage et l’investigation, et mettre ces événements avec en corrélation d’autres provenant de sources de données supplémentaires dans Azure Sentinel.

- Stockez les journaux avec une période de conservation accrue, au-delà de la durée par défaut de 30 jours de Microsoft Defender for Endpoint ou Microsoft 365 Defender. Pour ce faire, vous pouvez configurer la période de conservation de votre espace de travail ou la conservation par table dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment profiter de l’utilisation de Microsoft 365 Defender conjointement avec Azure Sentinel, à l’aide du connecteur Microsoft 365 Defender.

- Obtenir des instructions pour [activer le connecteur Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Créer des [alertes personnalisées](tutorial-detect-threats-custom.md) et [investiguer sur des incidents](tutorial-investigate-cases.md).
