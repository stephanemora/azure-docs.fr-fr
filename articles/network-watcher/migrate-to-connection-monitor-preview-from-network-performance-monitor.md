---
title: Migration de Network Performance Monitor vers le Moniteur de connexion (préversion)
titleSuffix: Azure Network Watcher
description: Découvrez comment migrer de Network Performance Monitor vers Moniteur de connexion (préversion).
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: dcbb82c1315e6150ddcfadbb52b2976447329b87
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441831"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migration de Network Performance Monitor vers le Moniteur de connexion (préversion)

En un clic et sans temps d’arrêt, vous pouvez migrer des tests de la solution Network Performance Monitor (NPM) vers le nouveau Moniteur de connexion (préversion) amélioré. Pour en savoir plus sur les avantages, consultez [Moniteur de connexion (préversion)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview).

>[!NOTE]
> Seuls des tests du Moniteur de connectivité de service peuvent être migrés vers le Moniteur de connexion (préversion).
>

## <a name="key-points-to-note"></a>Points clés à noter

La migration produit les résultats suivants :

* Les agents locaux et les paramètres de pare-feu fonctionnent tels quels. Aucune modification n’est nécessaire. Les agents Log Analytics installés sur des machines virtuelles Azure doivent être remplacés par l’extension Network Watcher.
* Les tests existants sont mappés à Moniteur de connexion (préversion) > Groupe de test > Format de test. En sélectionnant **Modifier**, vous pouvez afficher et modifier les propriétés du nouveau Moniteur de connexion (préversion), télécharger un modèle pour y apporter des modifications, et soumettre le modèle via Azure Resource Manager.
* Les agents envoient des données à l’espace de travail et aux métriques Log Analytics.
* Analyse des données :
   * **Données dans Log Analytics** : avant la migration, les données restent dans l’espace de travail dans lequel NPM est configuré dans la table NetworkMonitoring. Après la migration, les données sont placées dans les tables NetworkMonitoring et ConnectionMonitor_CL dans le même espace de travail. Une fois les tests désactivés dans NPM, les données sont stockées uniquement dans la table ConnectionMonitor_CL.
   * **Alertes, tableaux de bord et intégrations basés sur un journal** : vous devez modifier manuellement les requêtes en fonction de la nouvelle table ConnectionMonitor_CL. Pour recréer les alertes dans les métriques, consultez [Surveillance de la connectivité réseau à l’aide du Moniteur de connexion (préversion)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Prérequis

* Assurez-vous que Network Watcher est activé dans votre abonnement et la région de l’espace de travail Log Analytics.
* Les machines virtuelles Azure sur lesquelles les agents Log Analytics sont installés doivent être activées avec l’extension Network Watcher.

## <a name="migrate-the-tests"></a>Migrer les tests

Pour migrer les tests de Network Performance Monitor vers le Moniteur de connexion (préversion), procédez comme suit :

1. Dans Network Watcher, sélectionnez **Moniteur de connexion**, puis sélectionnez l’onglet **Migrer les tests de NPM**. 

    ![Capture d’écran montrant le volet « Migrer les tests de NPM » dans Network Watcher | Moniteur de connexion (préversion).](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1. Dans les listes déroulantes, sélectionnez votre abonnement et votre espace de travail, puis la fonctionnalité NPM que vous souhaitez migrer. Actuellement, vous pouvez migrer des tests uniquement à partir du Moniteur de connectivité de service.  
1. Sélectionnez **Importer** pour migrer les tests.

Après le début de la migration, les modifications suivantes ont lieu : 
* Une nouvelle ressource de moniteur de connexion est créée.
   * Une seule analyse de connexion par région et un abonnement est créé. Pour les tests avec des agents locaux, le nouveau nom du moniteur de connexion est au format `<workspaceName>_"on-premises"`. Pour les tests avec des agents Azure, le nouveau nom du moniteur de connexion est au format `<workspaceName>_<Azure_region_name>`.
   * Les données de surveillance sont désormais stockées dans l’espace de travail Log Analytics dans lequel NPM est activé, dans une nouvelle table appelée Connectionmonitor_CL. 
   * Le nom du test est transféré en tant que nom du groupe de test. La description du test n’est pas migrée.
   * Les points de terminaison source et de destination sont créés et utilisés dans le groupe de test. Pour les agents locaux, les points de terminaison on le format `<workspaceName>_"endpoint"_<FQDN of on-premises machine>`. Pour Azure, si les tests de migration contiennent des agents qui ne sont pas en cours d’exécution, vous devez activer les agents et les migrer à nouveau.
   * Le port de destination et l’intervalle de détection sont déplacés vers une configuration de test, sous les noms *TC_\<testname>* et *TC_\<testname>_AppThresholds*. Le protocole est défini sur la base des valeurs de port. Les seuils de réussite et autres propriétés facultatives sont laissés en blanc.
* NPM n’étant pas désactivé. les tests migrés peuvent donc continuer d’envoyer des données aux tables NetworkMonitoring et ConnectionMonitor_CL. Cette approche garantit que les alertes et intégrations basées sur un journal existantes ne sont pas affectées.
* Le moniteur de connexion nouvellement créé est visible dans Moniteur de connexion (préversion).

Après la migration, veillez à effectuer les opérations suivantes :
* Désactivez manuellement les tests dans NPM. Tant que vous ne le faites pas, vous continuerez d’être facturé pour ceux-ci. 
* Lors de la désactivation de NPM, recréez vos alertes sur la table ConnectionMonitor_CL ou utilisez des métriques. 
* Migrez les intégrations externes vers la table ConnectionMonitor_CL. Les intégrations externes sont, par exemple, les tableaux de bord dans Power BI et Grafana, ainsi que les intégrations avec les systèmes Security Information and Event Management (SIEM).


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le Moniteur de connexion (préversion), consultez :
* [Migration d’un Moniteur de connexion vers un Moniteur de connexion (préversion)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* [Créer un Moniteur de connexion (préversion) à l’aide du portail Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
