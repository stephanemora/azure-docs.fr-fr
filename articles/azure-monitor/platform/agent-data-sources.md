---
title: Configurer des sources de données d’agent dans Log Analytics | Microsoft Docs
description: Les sources de données définissent les données de journal que Log Analytics collecte auprès des agents et d’autres sources connectées.  Cet article décrit la façon dont Log Analytics utilise les sources de données, explique en détail comment les configurer, et fournit un résumé des différentes sources de données disponibles.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: d9bedeeb2e354dab8bc6a7be56826f28914326be
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101528"
---
# <a name="agent-data-sources-in-log-analytics"></a>Sources de données d’agent dans Log Analytics
Les données collectées par Log Analytics à partir des agents dépendent des sources de données que vous configurez.  Les données provenant des agents sont stockées en tant que [données de journal](data-collection.md) avec un ensemble d’enregistrements.  Chaque source de données crée des enregistrements d'un type particulier, chaque type ayant son propre ensemble de propriétés.

![Collecte de données de journal](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Résumé des sources de données
Le tableau suivant répertorie les sources de données des agents actuellement disponibles dans Log Analytics.  Chacune de ces sources comporte un lien vers un article distinct qui fournit des détails sur cette source de données.   Il fournit également des informations sur leur méthode et leur fréquence de collecte. 


| Source de données | Plateforme | Microsoft Monitoring Agent | Agent Operations Manager | Stockage Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Journaux personnalisés](data-sources-custom-logs.md) |  Windows |&#8226; |  | |  |  | à l'arrivée |
| [Journaux personnalisés](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | à l'arrivée |
| [Journaux IIS](data-sources-iis-logs.md) |  Windows |&#8226; |&#8226; |&#8226; |  |  |Dépend du paramètre Substitution de fichier journal |
| [Compteurs de performances](data-sources-performance-counters.md) |  Windows |&#8226; |&#8226; |  |  |  |comme prévu, minimum de 10 secondes |
| [Compteurs de performances](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |comme prévu, minimum de 10 secondes |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |à partir de Stockage Azure : 10 minutes ; à partir de l’agent : à l’arrivée |
| [Journaux d’événements Windows](data-sources-windows-events.md) | Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | à l'arrivée |


## <a name="configuring-data-sources"></a>Configuration des sources de données
Vous configurez des sources de données à partir du menu **Données** dans **Paramètres avancés** pour l’espace de travail.  Toutes les configurations sont remises à toutes les sources connectées dans votre espace de travail.  Nous ne pouvez actuellement exclure aucun agents de cette configuration.

![Configurer les événements Windows](./media/agent-data-sources/configure-events.png)

1. Dans le portail Azure, sélectionnez **Log Analytics** > votre espace de travail > **Paramètres avancés**.
2. Sélectionnez **Données**.
3. Cliquez sur la source de données à configurer.
4. Suivez le lien vers la documentation de chaque source de données dans le tableau ci-dessus pour plus d'informations sur leur configuration.


## <a name="data-collection"></a>Collecte des données
Les configurations des sources de données sont remises en quelques minutes aux agents directement connectés à Log Analytics.  Les données spécifiées sont collectées à partir de l'agent et remises directement à Log Analytics à des intervalles spécifiques à chaque source de données.  Consultez la documentation de chaque source de données pour obtenir ces informations spécifiques.

Pour les agents System Center Operations Manager d’un groupe d'administration connecté, les configurations de sources de données sont traduites en packs d'administration et remises au groupe d'administration toutes les 5 minutes par défaut.  L’agent télécharge le pack d’administration comme tout autre, et collecte les données spécifiées. Selon la source de données, soit les données sont envoyées à un serveur d’administration qui les transfère à Log Analytics, soit l’agent les envoie à Log Analytics sans passer par le serveur d’administration. Pour plus d’informations, consultez [Data collection details for monitoring solutions in Azure](../../azure-monitor/insights/solutions-inventory.md) (Détails sur la collecte de données pour les solutions de supervision dans Azure).  Vous pouvez consulter les détails de la connexion à Operations Manager et à Log Analytics et de la modification de la fréquence à laquelle la configuration est remise dans la rubrique [Configurer l'intégration avec System Center Operations Manager](../../log-analytics/log-analytics-om-agents.md).

Si l’agent ne peut pas se connecter à Log Analytics ou à Operations Manager, il continue à collecter des données qu’il fournira lorsqu’une connexion sera établie.  Les données peuvent être perdues si le volume de données atteint la taille maximale du cache du client ou si l’agent n’est pas en mesure d’établir une connexion dans un délai de 24 heures.

## <a name="log-records"></a>Enregistrement de journaux
Toutes les données collectées par Log Analytics sont stockées dans l’espace de travail en tant qu'enregistrements.  Les enregistrements collectés par différentes sources de données auront leur propre jeu de propriétés et seront identifiés par leur propriété **Type** .  Consultez la documentation de chaque source de données et solution pour plus d'informations sur chaque type d'enregistrement.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez les [solutions de supervision](../../azure-monitor/insights/solutions.md) qui ajoutent des fonctionnalités à Azure Monitor et collectent des données dans l’espace de travail.
* Découvrez les [requêtes de journal](../../log-analytics/log-analytics-queries.md) pour analyser les données collectées à partir de sources de données et de solutions de supervision.  
* Configurez des [alertes](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) qui vous informeront de façon proactive des données critiques collectées à partir de sources de données et de solutions de supervision.
