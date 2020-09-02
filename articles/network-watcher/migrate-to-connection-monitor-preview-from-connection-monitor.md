---
title: Migrer vers le Moniteur de connexion (préversion) à partir du Moniteur de connexion
titleSuffix: Azure Network Watcher
description: Découvrez comment migrer vers le Moniteur de connexion (préversion) à partir du Moniteur de connexion.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701250"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>Migrer vers le Moniteur de connexion (préversion) à partir du Moniteur de connexion

En un clic et sans temps d’arrêt, vous pouvez migrer des moniteurs de connexion vers le nouveau Moniteur de connexion amélioré (en préversion). Pour en savoir plus sur les avantages, vous pouvez lire [Moniteur de connexion (préversion)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

## <a name="key-points-to-note"></a>Points clés à noter

* Les agents et les paramètres de pare-feu fonctionnent tels quels (aucune touche requise). 
* Les moniteurs de connexion existants seront mappés vers le Moniteur de connexion (préversion)-> Groupe de test-> Format de test. Les utilisateurs peuvent cliquer sur *Modifier* pour visualiser et modifier les propriétés du nouveau Moniteur de connexion et télécharger le modèle pour apporter des modifications au Moniteur de connexion et le soumettre via Azure Resource Manager. 
* Les machines virtuelles Azure avec l’extension Network Watcher envoient des données à l’espace de travail et aux métriques. Les analyses de connexion mettent les données à disposition via les nouvelles métriques (ChecksFailedPercent (préversion) et RoundTripTimeMs (préversion)) à la place des mesures d’arrêt anciennes (ProbesFailedPercent et AverageRoundtripMs). 
* Données de surveillance
    * Alertes : elles seront migrées vers de nouvelles métriques dans le cadre de la migration
    * Tableaux de bord et intégrations : vous devez modifier manuellement l’ensemble de métriques. 
    
## <a name="prerequisites"></a>Prérequis

En cas d’utilisation d’un espace de travail personnalisé, s’assurer que Network Watcher est activé dans l’abonnement et la région de l’espace de travail Log Analytics 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>Étapes de migration vers le Moniteur de connexion (préversion) à partir du Moniteur de connexion

1. Cliquez sur « Moniteur de connexion », accédez à « Migrer les moniteurs de connexion » pour migrer les moniteurs de connexion de la solution la plus ancienne à la solution la plus récente.

    ![Capture d’écran montrant la migration des moniteurs de connexion vers Moniteur de connexion en préversion](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Sélectionnez les moniteurs d’abonnement et de connexion, puis cliquez sur « Migrer la sélection ». En un clic, migrer les moniteurs de connexion existants vers Moniteur de connexion (préversion) 
1. Vous pouvez personnaliser les propriétés du moniteur de connexion, modifier l’espace de travail par défaut, télécharger le modèle et vérifier l’état de la migration. 
1. Une fois que la migration commence, les changements suivants se produisent : 
    1. Modifications apportées aux ressources Azure Resource Manager pour tenir compte de l’installation du dernier moniteur de connexion
        1. Le nom, la région et l’abonnement du moniteur de connexion restent inchangés. Par conséquent, il n’y a aucun impact sur l’ID de ressource.
        1. Sauf s’ils sont personnalisés, un espace de travail Log Analytics par défaut est créé dans la région et l’abonnement du moniteur de connexion. Cet espace de travail est l’emplacement où sont stockées les données d’analyse. Les données de résultat de test seront également stockées dans les métriques.
        1. Chaque test est migré vers un groupe de test appelé * defaultTestGroup*
        1.  Les points de terminaison source et de destination sont créés et utilisés dans le groupe de test créé. Les noms par défaut sont *defaultSourceEndpoint* et *defaultDestinationEndpoint*
        1. Le port de destination et l’intervalle de détection sont déplacés vers la configuration de test appelée *defaultTestConfiguration*. Le protocole est défini en fonction des valeurs de port. Les seuils de réussite et autres propriétés facultatives sont laissés en blanc.
    1. Les alertes de métriques sont migrées vers les alertes de métriques du Moniteur de connexion (préversion). Les mesures sont différentes <link to metric section in the doc>, par conséquent la modification
    1. Les analyses de connexion migrées n’apparaissent pas dans l’ancienne solution du moniteur de connexion. elles ne seront désormais disponibles que dans le Moniteur de connexion (préversion)
    1. Toutes les intégrations externes, telles que les tableaux de bord dans Power BI, Grafana, les intégrations aux systèmes SIEM, devront être migrées directement par l’utilisateur. Il s’agit de la seule étape manuelle que l’utilisateur doit effectuer pour migrer son installation.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment migrer des tests de Network Performance Monitor vers le Moniteur de connexion (préversion)](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* Découvrez [comment créer une instance Moniteur de connexion (préversion) à l’aide du portail Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal).
