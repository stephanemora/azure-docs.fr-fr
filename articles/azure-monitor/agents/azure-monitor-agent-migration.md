---
title: Migrer depuis des agents hérités vers le nouvel agent Azure Monitor
description: Conseils pour la migration des agents hérités existants vers le nouvel agent Azure Monitor (AMA) et les règles de collecte de données (DCR).
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/12/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d3f2fa2457ef52fc02e2fd5b7d183918e2e9e09c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177984"
---
# <a name="migrate-from-log-analytics-agents"></a>Migrer depuis des agents Log Analytics
Cet article fournit des conseils de haut niveau sur le moment et la façon de migrer vers le nouvel agent Azure Monitor (AMA) et les règles de collecte de données (DCR). Cet article sera mis à jour lorsque de nouveaux outils de migration seront disponibles.


## <a name="review"></a>Révision
- Pour vous aider à décider si vous devez migrer vers le nouvel agent Azure Monitor maintenant ou plus tard, consultez les conseils dans [Vue d’ensemble de l’agent Azure Monitor](./azure-monitor-agent-overview.md#should-i-switch-to-the-azure-monitor-agent).
- Pour l’agent Azure Monitor, passez en revue les nouvelles fonctionnalités, la disponibilité des fonctionnalités, des services et des solutions existants, et les limitations actuelles dans [Vue d’ensemble des agents Azure Monitor](./agents-overview.md#azure-monitor-agent).


## <a name="test-migration-by-using-the-azure-portal"></a>Tester la migration à l’aide du portail Azure
1. Pour garantir un déploiement sécurisé pendant la migration, commencez à tester avec quelques ressources dans votre environnement de non-production qui exécute l’agent Log Analytics existant. Une fois que vous pouvez valider les données collectées sur ces ressources de test, déployez en production en suivant la même procédure.
1. Accédez à **Superviser** > **Paramètres** > **Règles de collecte des données** et [créez de nouvelles règles de collecte des données](./data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) pour commencer à collecter certains des types de données existants. Lorsque vous l’utilisez, l’interface utilisateur graphique du portail effectue pour vous les opérations suivantes sur toutes les ressources cibles :
    - Active l’identité managée attribuée par le système
    - Installe l’extension de l’agent Azure Monitor
    - Crée et déploie des associations de règles de collecte de données
1. Confirmez que les données circulent comme prévu via l’agent Azure Monitor. Vérifiez les valeurs de la nouvelle version de l’agent dans la table **Pulsations**. Assurez-vous qu’elles correspondent aux données qui transitent par l’agent Log Analytics existant.


## <a name="at-scale-migration-by-using-policies"></a>Migration à grande échelle à l’aide de stratégies
1. Commencez par analyser votre configuration actuelle de la supervision avec MMA/OMS à l’aide des critères suivants :
    - Sources, comme des machines virtuelles, des groupes de machines virtuelles identiques et des serveurs locaux
    - Sources de données, comme des compteurs de performances, des journaux des événements Windows et Syslog
    - Destinations, comme des espaces de travail Log Analytics
1. [Créez de nouvelles règles de collecte de données](/rest/api/monitor/datacollectionrules/create#examples) à l’aide de la configuration précédente. Il est recommandé de disposer d’une règle de collecte de données distincte pour les sources Windows et Linux. Vous pouvez également utiliser des règles de collecte de données distinctes pour les équipes individuelles ayant des besoins différents en matière de collecte de données.
1. [Activez une identité managée attribuée par le système](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#system-assigned-managed-identity) sur les ressources cibles.
2. Installez l’extension de l’agent Azure Monitor. Déployez les associations de règles de collecte de données sur toutes les ressources cibles à l’aide de l’[initiative de la stratégie intégrée](azure-monitor-agent-install.md#install-with-azure-policy). Fournissez la règle de collecte de données précédente en tant que paramètre d’entrée. 
1. Confirmez que les données circulent comme prévu via l’agent Azure Monitor. Vérifiez les valeurs de la nouvelle version de l’agent dans la table **Pulsations**. Assurez-vous qu’elles correspondent aux données qui transitent par l’agent Log Analytics existant.
2. Validez toutes les dépendances en aval, comme les tableaux de bord, les alertes et les Runbook Workers. Tous les workbooks continuent à fonctionner maintenant en utilisant les données du nouvel agent.
3. [Désinstallez l’agent Log Analytics](./agent-manage.md#uninstall-agent) sur les ressources. Ne le désinstallez pas si vous avez besoin de l’utiliser pour des scénarios System Center Operations Manager ou d’autres solutions qui ne sont pas encore disponibles sur l’agent Azure Monitor.
4. Nettoyez les fichiers de configuration, les clés d’espace de travail ou les certificats qui ont été utilisés précédemment par l’agent Log Analytics.


