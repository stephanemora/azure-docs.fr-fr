---
title: Intégration de Microsoft 365 Defender avec Azure Sentinel | Microsoft Docs
description: Découvrez comment l’utilisation de Microsoft 365 Defender conjointement avec Azure Sentinel vous permet d’utiliser Azure Sentinel comme file d’attente d’incidents universelle tout en appliquant de manière transparente les forces de Microsoft 365 Defender pour vous aider à examiner les incidents de sécurité Microsoft 365. Découvrez également comment ingérer les données de chasse avancées d’un composant Defender dans Azure Sentinel.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 86a998e304755085a9ae1e15e011df3b242c4df8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531498"
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

L’intégration d’incidents [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) d’Azure Sentinel vous permet de diffuser tous les incidents Microsoft 365 Defender dans Azure Sentinel et de les garder synchronisés entre les deux portails. Les incidents de Microsoft 365 Defender (anciennement Protection Microsoft contre les menaces) incluent l’ensemble des alertes, entités et informations pertinentes associées, ce qui vous offre suffisamment de contexte pour effectuer un triage et une investigation préliminaire dans Azure Sentinel. Une fois dans Sentinel, les incidents restent synchronisés de manière bidirectionnelle avec Microsoft 365 Defender, ce qui vous permet de tirer parti des avantages des deux portails dans votre investigation sur l’incident.

Cette intégration confère aux incidents de sécurité Microsoft 365 la visibilité nécessaire pour leur gestion à partir d’Azure Sentinel dans le cadre de la file d’attente des incidents principale de toute l’organisation, ce qui vous permet de voir les incidents Microsoft 365, ainsi que de les corréler avec ceux de tous vos autres systèmes locaux et cloud. En même temps, elle vous permet de bénéficier de la puissance et des capacités uniques de Microsoft 365 Defender pour des investigations approfondies, ainsi que d’une expérience spécifique de Microsoft 365 dans tout l’écosystème de Microsoft 365. Microsoft 365 Defender enrichit et regroupe les alertes de plusieurs produits Microsoft 365, ce qui a pour effet de réduire la taille de la file d’attente des incidents de SOC et de raccourcir le temps de résolution. Les services de composants qui font partie de la pile Microsoft 365 Defender sont les suivants :

- **Microsoft Defender pour point de terminaison** (anciennement Microsoft Defender ATP)
- **Microsoft Defender for Identity** (anciennement Azure ATP)
- **Microsoft Defender pour Office 365** (anciennement Office 365 ATP)
- **Microsoft Cloud App Security**

En plus de collecter des alertes à partir de ces composants, Microsoft 365 Defender génère ses propres alertes. Il crée des incidents à partir de toutes ces alertes et les envoie à Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Cas d’utilisation et scénarios courants

- Connexion en un clic d’incidents Microsoft 365 Defender, y compris toutes les alertes et entités des composants Microsoft 365 Defender, dans Azure Sentinel.

- Synchronisation bidirectionnelle entre les incidents Sentinel et Microsoft 365 Defender sur l’état, le propriétaire et le motif de clôture.

- Application des fonctionnalités de regroupement et d’enrichissement des alertes Microsoft 365 Defender dans Azure Sentinel, permettant de réduire le temps de résolution.

- Lien ciblé en contexte entre un incident Azure Sentinel et un incident Microsoft 365 Defender parallèle pour faciliter les investigations sur les deux portails.

### <a name="connecting-to-microsoft-365-defender"></a>Connexion à Microsoft 365 Defender

Une fois que vous avez activé le connecteur de données Microsoft 365 Defender pour [collecter les incidents et les alertes](connect-microsoft-365-defender.md), les incidents Microsoft 365 Defender s’affichent dans la file d’attente des incidents d’Azure Sentinel, avec **Microsoft 365 Defender** dans le champ **Nom du produit**, peu après qu’ils ont été générés dans Microsoft 365 Defender.
- Jusqu’à 10 minutes peuvent s’écouler entre le moment où un incident est généré dans Microsoft 365 Defender et le moment où il apparaît dans Azure Sentinel.

- Les incidents sont ingérés et synchronisés sans frais supplémentaires.

Une fois que l’intégration de Microsoft 365 Defender est connectée, tous les connecteurs d’alerte de composant (Defender pour point de terminaison, Defender pour Identity, Defender pour Office 365, Cloud App Security) seront automatiquement connectés en arrière-plan s’ils ne l’étaient pas déjà. Si des licences de composants ont été achetées après la connexion de Microsoft 365 Defender, les alertes et les incidents du nouveau produit sont toujours transmis à Azure Sentinel sans que cela nécessite une configuration ou des frais supplémentaires.

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Incidents Microsoft 365 Defender et règles de création d’incident Microsoft

- Les incidents générés par Microsoft 365 Defender, en fonction des alertes provenant des produits de sécurité Microsoft 365, sont créés à l’aide d’une logique Microsoft 365 Defender personnalisée.

- Les règles de création d’incident Microsoft dans Azure Sentinel créent également des incidents à partir des mêmes alertes, en utilisant une logique Azure Sentinel personnalisée (différente).

- L’utilisation conjointe des deux mécanismes est entièrement prise en charge, et cette configuration permet de faciliter la transition vers la nouvelle logique de création d’incident de Microsoft 365 Defender. Toutefois, cela crée des **incidents en double** pour les mêmes alertes.

- Pour éviter de créer des incidents en double pour les mêmes alertes, nous recommandons aux clients de désactiver toutes les **règles de création d’incidents Microsoft** pour les produits Microsoft 365 (Defender pour Endpoint, Defender pour Identity, Defender pour Office 365 et Cloud App Security) lors de la connexion à Microsoft 365 Defender. Pour ce faire, vous pouvez désactiver la création d’un incident dans la page du connecteur. N’oubliez pas que, si vous procédez de la sorte, les filtres appliqués par les règles de création d’incident ne seront pas appliqués à l’intégration d’incidents Microsoft 365 Defender.

    > [!NOTE]
    > Tous les types d’alertes Microsoft Cloud App Security sont désormais intégrés à Microsoft 365 Defender.

### <a name="working-with-microsoft-365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Utilisation d’incidents Microsoft 365 Defender dans Azure Sentinel et synchronisation bidirectionnelle

Les incidents Microsoft 365 Defender s’affichent dans la file d’attente des incidents d’Azure Sentinel avec le nom de produit **Microsoft 365 Defender**, ainsi qu’avec des détails et fonctionnalités similaires à ceux d’autres incidents de Sentinel. Chaque incident contient un lien vers l’incident parallèle dans le portail Microsoft 365 Defender.

À mesure que l’incident évolue dans Microsoft 365 Defender et qu’un plus grand nombre d’alertes ou d’entités y sont ajoutés, l’incident Azure Sentinel est mis à jour en conséquence.

Les modifications apportées à l’état, au motif de clôture ou à l’affectation d’un incident Microsoft 365, tant dans Microsoft 365 Defender que dans Azure Sentinel, sont également mises à jour en conséquence dans la file d’attente des incidents de l’autre. La synchronisation aura lieu dans les deux portails immédiatement après l’application de la modification de l’incident. Une actualisation peut être nécessaire pour voir les dernières modifications.

Dans Microsoft 365 Defender, toutes les alertes d’un incident peuvent être transférées vers un autre, ce qui a pour effet de fusionner les incidents. Quand cette fusion se produit, les incidents Azure Sentinel reflètent les modifications. Un incident contiendra toutes les alertes des incidents d’origine, et l’autre sera automatiquement clos, avec ajout de l’étiquette « redirigé ».

> [!NOTE]
> Les incidents dans Azure Sentinel peuvent contenir jusqu’à 150 alertes. Les incidents Microsoft 365 Defender peuvent avoir plus que cela. Si un incident Microsoft 365 Defender contenant plus de 150 alertes est synchronisé sur Azure Sentinel, l’incident Sentinel indique la présence de « 150 + » alertes et fournit un lien vers l’incident parallèle dans Microsoft 365 Defender, où il est possible de voir l’ensemble complet d’alertes.

## <a name="advanced-hunting-event-collection"></a>Collecte d’événements de repérage avancé

Le connecteur Microsoft 365 Defender vous permet de diffuser des événements de **repérage avancé** (un type de données d’événement brutes) de Microsoft 365 Defender vers Azure Sentinel. Vous pouvez actuellement collecter des événements de [repérage avancé](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) de [Microsoft Defender pour Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) et les diffuser directement vers des tables dédiées dans votre espace de travail Azure Sentinel. Ces tables sont générées sur le même schéma que celui utilisé dans le portail Microsoft 365 Defender, ce qui vous donne un accès complet à l’ensemble des événements de repérage avancé, et vous permet d’effectuer les opérations suivantes :

- Copier facilement vos requêtes de repérage avancé Microsoft Defender pour Endpoint existantes dans Azure Sentinel.

- Utilisez les journaux des événements bruts afin de fournir des insights supplémentaires pour vos alertes, la recherche de menaces et l’investigation, et mettez en corrélation les événements avec ceux provenant d’autres sources de données dans Azure Sentinel.

- Stockez les journaux avec une période de conservation accrue, au-delà de la durée par défaut de 30 jours de Microsoft Defender for Endpoint ou Microsoft 365 Defender. Pour ce faire, vous pouvez configurer la période de conservation de votre espace de travail ou la conservation par table dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment profiter de l’utilisation de Microsoft 365 Defender conjointement avec Azure Sentinel, à l’aide du connecteur Microsoft 365 Defender.

- Obtenir des instructions pour [activer le connecteur Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Créer des [alertes personnalisées](detect-threats-custom.md) et [investiguer sur des incidents](investigate-cases.md).
