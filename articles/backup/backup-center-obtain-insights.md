---
title: Obtenir des insights avec le Centre de sauvegarde
description: Découvrez comment analyser des tendances historiques et obtenir des insights plus approfondis au sujet de vos sauvegardes avec le Centre de sauvegarde.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c48173749a9b47be7eeb906e9f8eec716e0cb200
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506007"
---
# <a name="obtain-insights-using-backup-center"></a>Obtenir des insights avec le Centre de sauvegarde

Pour analyser les tendances historiques et obtenir des insights plus approfondies sur vos sauvegardes, le Centre de sauvegarde fournit une interface pour les [Rapports de sauvegarde](configure-reports.md), qui utilise des [journaux Azure Monitor](../azure-monitor/logs/data-platform-logs.md) et des [classeurs Azure](../azure-monitor/visualize/workbooks-overview.md). La solution Rapports de sauvegarde offre les fonctionnalités suivantes :

- allocation et prévision du stockage cloud utilisé ;

- audit des sauvegardes et restaurations ;

- identification des tendances clés à différents niveaux de granularité.

- Obtention de visibilité et d’insights sur les possibilités d’optimisation des coûts pour vos sauvegardes.

## <a name="supported-scenarios"></a>Scénarios pris en charge

- La solution Rapports de sauvegarde n’est actuellement pas disponible pour la sauvegarde du serveur Azure Database pour PostgreSQL.

- Pour obtenir une liste détaillée des scénarios pris en charge et non pris en charge, consultez la [Matrice de prise en charge](backup-center-support-matrix.md).

## <a name="get-started"></a>Bien démarrer

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Configurer vos coffres pour envoyer des données à un espace de travail Log Analytics

[Découvrez comment configurer des paramètres de diagnostic à grande échelle pour vos coffres](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Afficher les rapports de sauvegarde dans le portail du Centre de sauvegarde

Pour ouvrir les rapports, sélectionnez l’option de menu **Rapports de sauvegarde** dans le Centre de sauvegarde. Choisissez un ou plusieurs espaces de travail Log Analytics pour afficher et analyser les informations essentielles sur vos sauvegardes.

![Rapports de sauvegarde dans le Centre de sauvegarde](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Voici les affichages disponibles :

1. **Résumé** : cet onglet donne une vue d’ensemble globale de votre espace de sauvegarde. [En savoir plus](./configure-reports.md#summary)

2. **Éléments de sauvegarde** : cet onglet fournit des informations et des tendances sur le stockage cloud consommé au niveau d’un élément de sauvegarde. [En savoir plus](./configure-reports.md#backup-items)

3. **Utilisation** : cet onglet indique les principaux paramètres de facturation pour vos sauvegardes. [En savoir plus](./configure-reports.md#usage)

4. **Travaux** : cet onglet indique les tendances durables sur les travaux, par exemple le nombre de travaux ayant échoué par jour et les principales causes d’échec. [En savoir plus](./configure-reports.md#jobs)

5. **Stratégies** : cet onglet fournit des informations sur toutes les stratégies actives, par exemple le nombre d’éléments associés et le stockage cloud total consommé par les éléments sauvegardés dans le cadre d’une stratégie donnée. [En savoir plus](./configure-reports.md#policies)

6. **Optimiser** : cet onglet vous permet d’obtenir une visibilité sur les opportunités potentielles d’optimisation des coûts pour vos sauvegardes. [En savoir plus](./configure-reports.md#optimize)

7. **Adhésion à la stratégie** : utilisez cet onglet pour obtenir une visibilité sur le fait que chaque instance de sauvegarde a eu au moins une sauvegarde réussie par jour. [En savoir plus](./configure-reports.md#policy-adherence)

Vous pouvez également configurer des e-mails pour tous ces rapports à l’aide de la fonctionnalité [Envoyer le rapport par e-mail](backup-reports-email.md).

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et exécuter des sauvegardes](backup-center-monitor-operate.md)
- [Gérer votre espace de sauvegarde](backup-center-govern-environment.md)
- [Effectuer des actions à l’aide du Centre de sauvegarde](backup-center-actions.md)