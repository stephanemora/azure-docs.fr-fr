---
title: Migration de Network Performance Monitor vers le Moniteur de connexion (préversion)
titleSuffix: Azure Network Watcher
description: Découvrez comment migrer de Network Performance Monitor vers Moniteur de connexion (préversion).
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701249"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migration de Network Performance Monitor vers le Moniteur de connexion (préversion)

En un clic et sans temps d’arrêt, vous pouvez migrer des tests Network Performance Monitor vers le nouveau moniteur de connexion amélioré (en préversion). Pour en savoir plus sur les avantages, vous pouvez lire [Moniteur de connexion (préversion)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> Seuls les tests du Moniteur de connectivité de service peuvent être migrés vers le Moniteur de connexion (préversion).
>

## <a name="key-points-to-note"></a>Points clés à noter

* Les agents locaux et les paramètres de pare-feu fonctionnent tels quels. Aucune modification n’est requise. Les agents Log Analytics installés sur des machines virtuelles Azure doivent être remplacés par une extension Network Watcher
* Les tests existants seront mappés vers le Moniteur de connexion (préversion)-> Groupe de test-> Format de test. Les utilisateurs peuvent cliquer sur *Modifier* pour visualiser et modifier les propriétés du nouveau Moniteur de connexion et télécharger le modèle pour apporter des modifications au Moniteur de connexion et le soumettre via Azure Resource Manager.
* Les agents envoient des données à la fois à l’espace de travail et aux métriques Log Analytics.
* Données de surveillance
    * Données dans Log Analytics : toutes les prémigrations de données continuent de résider dans l’espace de travail de NPM défini dans la table NetworkMonitoring. Après la migration, les données sont placées dans la table NetworkMonitoring et dans la table ConnectionMonitor_CL dans le même espace de travail. Une fois les tests désactivés à partir de NPM, les données sont stockées uniquement dans la table ConnectionMonitor_CL
    * Alertes, tableaux de bord et intégrations basés sur le journal : vous devez modifier manuellement les requêtes en fonction de la nouvelle table ConnectionMonitor_CL. Vous pouvez également recréer les alertes dans les métriques à l’aide de ce lien. La possibilité de migrer automatiquement des alertes basées sur les journaux sur la table NetworkMonitoring vers des alertes basées sur des métriques dans le cadre de la migration sera bientôt disponible.
    
## <a name="prerequisites"></a>Prérequis

*   S’assurer que Network Watcher est activé dans l’abonnement et la région de l’espace de travail Log Analytics
*   Les machines virtuelles Azure avec les agents Log Analytics doivent être activées avec l’extension Network Watcher

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Étapes de migration des tests de Network Performance Monitor vers le Moniteur de connexion (préversion)

1.  Cliquez sur « Moniteur de connexion », accédez à « Migrer les tests de NPM » pour migrer les tests vers le Moniteur de connexion (préversion)

    ![Capture d’écran montrant la migration des tests de NPM vers Moniteur de connexion en préversion](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  Sélectionnez l’abonnement, l’espace de travail et la fonctionnalité NPM que vous souhaitez migrer. Actuellement, vous pouvez migrer uniquement les tests à partir du Moniteur de connectivité de service.  
1.  Cliquez sur « Importer » pour migrer les tests
1.  Une fois que la migration commence, les changements suivants se produisent : 
    1. Une nouvelle ressource du moniteur de connexion est créée
        1. Une seule analyse de connexion par région et un abonnement est créé. Pour les tests avec les agents locaux, le nouveau nom du moniteur de connexion est au format <workspaceName> _« local ». Pour les tests avec les agents Azure, le nouveau nom du moniteur de connexion est au format <workspaceName>_ < nom_région_Azure >
        1. Les données de surveillance sont désormais stockées dans le même espace de travail Log Analytics dans lequel NPM est activé, dans une nouvelle table appelée table Connectionmonitor_CL. 
        1. Le nom du test est transféré vers le nom du groupe de test. La description du test ne sera pas migrée.
        1. Les points de terminaison source et de destination sont créés et utilisés dans le groupe de test créé. Pour les agents locaux, les points de terminaison sont nommés au format <workspaceName> _« point de terminaison »_ <FQDN of on-premises machine>. Pour Azure, si les tests de migration contiennent des agents qui ne sont pas en cours d’exécution, vous devez activer les agents et migrer à nouveau.
        1. Le port de destination et l’intervalle de détection sont déplacés vers la configuration de test, à savoir « TC » _<testname>» et « TC »_ <testname>_« AppThresholds ». Le protocole est défini en fonction des valeurs de port. Les seuils de réussite et autres propriétés facultatives sont laissés en blanc.
    1. NPM n’est pas désactivé. Les tests migrés continuent donc à envoyer des données à la table NetworkMonitoring, ainsi qu’à la table ConnectionMonitor_CL. Cette étape permet de s’assurer que les alertes et les intégrations basées sur le journal existantes ne sont pas impactées. La possibilité de migrer automatiquement des alertes basées sur les journaux sur la table NetworkMonitoring vers des alertes basées sur des métriques dans le cadre de la migration sera bientôt disponible.
    1. Le moniteur de connexion nouvellement créé sera visible dans Moniteur de connexion (préversion)
1.  Après la migration, vous devez désactiver manuellement les tests dans NPM. Tant que vous ne l’aurez pas fait, vous continuerez à être facturé pour les mêmes services. Lors de la désactivation de NPM, veillez à recréer vos alertes sur la table ConnectionMonitor_CL ou à utiliser des métriques. Assurez-vous que toutes les intégrations externes, telles que les tableaux de bord dans Power BI, Grafana, les intégrations aux systèmes SIEM, devront être migrées vers la table ConnectionMonitor_CL.


## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment migrer vers le Moniteur de connexion (préversion) à partir du Moniteur de connexion](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* Découvrez [comment créer une instance Moniteur de connexion (préversion) à l’aide du portail Azure](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal).
