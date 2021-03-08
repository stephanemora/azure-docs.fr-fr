---
title: Migration de Network Performance Monitor vers Moniteur de connexion
titleSuffix: Azure Network Watcher
description: Découvrez comment migrer de Network Performance Monitor vers Moniteur de connexion.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: e5053284de18740b761df3e5df256cc79d2e8f1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698686"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migration de Network Performance Monitor vers Moniteur de connexion

> [!IMPORTANT]
> À compter du 1er juillet 2021, vous ne pourrez plus ajouter de tests dans un espace de travail existant ou activer un nouvel espace de travail avec Network Performance Monitor. Vous pouvez continuer à utiliser les tests créés avant le 1er juillet 2021. Pour réduire l’interruption de service de vos charges de travail actuelles, migrez vos tests de Network Performance Monitor vers le nouveau moniteur de connexion dans Azure Network Watcher avant le 29 février 2024.

En un clic et sans temps d’arrêt, vous pouvez migrer des tests de la solution Network Performance Monitor (NPM) vers la nouvelle fonctionnalité Moniteur de connexion améliorée. Pour en savoir plus sur les avantages, consultez [Moniteur de connexion](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Points clés à noter

La migration produit les résultats suivants :

* Les agents locaux et les paramètres de pare-feu fonctionnent tels quels. Aucune modification n’est nécessaire. Les agents Log Analytics installés sur des machines virtuelles Azure doivent être remplacés par l’[extension Network Watcher](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).
* Les tests existants sont mappés à Moniteur de connexion > Groupe de test > Format de test. En sélectionnant **Modifier**, vous pouvez afficher et modifier les propriétés du nouveau moniteur de connexion, télécharger un modèle pour y apporter des modifications et soumettre le modèle via Azure Resource Manager.
* Les agents envoient des données à l’espace de travail et aux métriques Log Analytics.
* Analyse des données :
   * **Données dans Log Analytics** : avant la migration, les données restent dans l’espace de travail dans lequel NPM est configuré dans la table NetworkMonitoring. Après la migration, les données sont placées dans les tables NetworkMonitoring, NWConnectionMonitorTestResult et NWConnectionMonitorPathResult, dans le même espace de travail. Une fois les tests désactivés dans NPM, les données sont uniquement stockées dans les tables NWConnectionMonitorTestResult et NWConnectionMonitorPathResult.
   * **Alertes, tableaux de bord et intégrations basés sur un journal** : vous devez modifier manuellement les requêtes en fonction des nouvelles tables NWConnectionMonitorTestResult et NWConnectionMonitorPathResult. Pour recréer les alertes dans les métriques, consultez [Surveillance de la connectivité réseau à l’aide de Moniteur de connexion](./connection-monitor-overview.md#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Prérequis

* Assurez-vous que Network Watcher est activé dans votre abonnement et la région de l’espace de travail Log Analytics. 
* Si vous utilisez une machine virtuelle Azure appartenant à une autre région ou un autre abonnement que l’espace de travail Log Analytics, assurez-vous que Network Watcher est activé pour cet abonnement et cette région.   
* Les machines virtuelles Azure sur lesquelles les agents Log Analytics sont installés doivent être activées avec l’extension Network Watcher.

## <a name="migrate-the-tests"></a>Migrer les tests

Pour migrer les tests de Network Performance Monitor vers Moniteur de connexion, procédez comme suit :

1. Dans Network Watcher, sélectionnez **Moniteur de connexion**, puis sélectionnez l’onglet **Migrer les tests de NPM**. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrer des tests de Network Performance Monitor vers le Moniteur de connexion" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Dans les listes déroulantes, sélectionnez votre abonnement et votre espace de travail, puis la fonctionnalité NPM que vous souhaitez migrer. 
1. Sélectionnez **Importer** pour migrer les tests.

Après le début de la migration, les modifications suivantes ont lieu : 
* Une nouvelle ressource de moniteur de connexion est créée.
   * Une seule analyse de connexion par région et un abonnement est créé. Pour les tests avec des agents locaux, le nouveau nom du moniteur de connexion est au format `<workspaceName>_"workspace_region_name"`. Pour les tests avec des agents Azure, le nouveau nom du moniteur de connexion est au format `<workspaceName>_<Azure_region_name>`.
   * Les données de surveillance sont désormais stockées dans le même espace de travail Log Analytics dans lequel NPM est activé, dans de nouvelles tables appelées NWConnectionMonitorTestResult et NWConnectionMonitorPathResult. 
   * Le nom du test est transféré en tant que nom du groupe de test. La description du test n’est pas migrée.
   * Les points de terminaison source et de destination sont créés et utilisés dans le groupe de test. Pour les agents locaux, les points de terminaison on le format `<workspaceName>_<FQDN of on-premises machine>`.
   * Le port de destination et l’intervalle de détection sont déplacés vers une configuration de test, sous les noms `TC_<protocol>_<port>` et `TC_<protocol>_<port>_AppThresholds`. Le protocole est défini sur la base des valeurs de port. Pour ICMP, les configurations de test sont nommées `TC_<protocol>` et `TC_<protocol>_AppThresholds` . Seuils de réussite et autres propriétés facultatives si le jeu est migré ; sinon, ces champs sont laissés vides.
   * Si les tests de migration contiennent des agents qui ne sont pas en cours d’exécution, vous devez activer les agents et les migrer à nouveau.
* Comme NPM n’est pas désactivé, les tests migrés peuvent continuer à envoyer des données aux tables NetworkMonitoring, NWConnectionMonitorTestResult et NWConnectionMonitorPathResult. Cette approche garantit que les alertes et intégrations basées sur un journal existantes ne sont pas affectées.
* Le moniteur de connexion nouvellement créé est visible dans Moniteur de connexion.

Après la migration, veillez à effectuer les opérations suivantes :
* Désactivez manuellement les tests dans NPM. Tant que vous ne le faites pas, vous continuerez d’être facturé pour ceux-ci. 
* Lorsque vous désactivez NPM, recréez vos alertes sur les tables NWConnectionMonitorTestResult et NWConnectionMonitorPathResult, ou utilisez des métriques. 
* Migrez les intégrations externes vers les tables NWConnectionMonitorTestResult et NWConnectionMonitorPathResult. Les intégrations externes sont, par exemple, les tableaux de bord dans Power BI et Grafana, ainsi que les intégrations avec les systèmes Security Information and Event Management (SIEM).


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le Moniteur de connexion, consultez :
* [Migrer vers le Moniteur de connexion à partir du Moniteur de connexion (classique)](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Créer un Moniteur de connexion à l’aide du portail Azure](./connection-monitor-create-using-portal.md)
