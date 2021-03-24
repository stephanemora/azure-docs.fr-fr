---
title: Créer un moniteur de connexion - PowerShell
titleSuffix: Azure Network Watcher
description: Découvrez comment créer un moniteur de connexion à l’aide de PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833029"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Créer un moniteur de connexion à l’aide de PowerShell

> [!IMPORTANT]
> À compter du 1er juillet 2021, vous ne pourrez plus ajouter de nouveaux tests dans un espace de travail existant ni activer un nouvel espace de travail dans Network Performance Monitor. Vous ne pourrez pas non plus ajouter de nouveaux moniteurs de connexion dans le moniteur de connexion (classique). Vous pouvez continuer d’utiliser les tests et moniteurs de connexion créés avant le 1er juillet 2021. Pour réduire l’interruption de service de vos charges de travail actuelles, [migrez vos tests de Network Performance Monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou [depuis le moniteur de connexion (classique)](migrate-to-connection-monitor-from-connection-monitor-classic.md) vers le nouveau moniteur de connexion dans Azure Network Watcher avant le 29 février 2024.


Découvrez comment superviser la communication entre vos ressources à l’aide de la fonctionnalité Moniteur de connexion d’Azure Network Watcher.


## <a name="before-you-begin"></a>Avant de commencer

Dans les moniteurs de connexion que vous créez avec Moniteur de connexion, vous pouvez ajouter à la fois des machines locales et des machines virtuelles Azure en tant que sources. Ces moniteurs de connexion peuvent également surveiller la connectivité aux points de terminaison. Les points de terminaison peuvent se trouver sur Azure ou sur toute autre URL ou adresse IP.

Un moniteur de connexion inclut les entités suivantes :

* **Ressource de moniteur de connexion** : Ressource Azure spécifique à une région. Toutes les entités ci-dessous sont des propriétés de la ressource de moniteur de connexion.
* **Point de terminaison** : Source ou destination qui participe aux vérifications de la connectivité. Les machines virtuelles Azure, les agents locaux, les URL et les adresses IP sont des exemples de points de terminaison.
* **Configuration de test** : Configuration spécifique à un protocole dans le cadre d’un test. En fonction du protocole que vous choisissez, vous pouvez définir le port, les seuils, la fréquence de test et d’autres paramètres.
* **Groupe de tests** : Groupe contenant les points de terminaison sources, les points de terminaison de destination et les configurations de test. Un moniteur de connexion peut contenir plusieurs groupes de tests.
* **Test** : Combinaison d’un point de terminaison source, d’un point de terminaison de destination et d’une configuration de test. Un test correspond au niveau le plus granulaire auquel les données de surveillance sont disponibles. Les données de surveillance incluent le pourcentage de vérifications qui ont échoué et la durée des boucles.

    ![Diagramme illustrant un moniteur de connexion, avec définition de la relation entre les groupes de tests et les tests.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>Étapes pour créer un moniteur de connexion

Utilisez les suivantes pour créer un moniteur de connexion à l’aide de PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Description des propriétés

* **ConnectionMonitorName** : nom de la ressource de moniteur de connexion.

* **SUB** : ID de l’abonnement dans lequel vous souhaitez créer un moniteur de connexion.

* **NW** : ID de ressource Network Watcher dans laquelle un moniteur de connexion est créé.

* **Emplacement** : région dans laquelle un moniteur de connexion est créé.

* **Points de terminaison**
    * **Nom** : nom unique de chaque point de terminaison.
    * **ID de ressource** : pour les points de terminaison Azure, l’ID de ressource fait référence à l’ID de ressource Azure Resource Manager des machines virtuelles. Pour les points de terminaison non-Azure, l’ID de ressource fait référence à l’ID de ressource Azure Resource Manager de l’espace de travail Log Analytics lié aux agents non-Azure.
    * **Adresse** : applicable uniquement lorsque l’ID de ressource n’est pas spécifié, ou si l’ID de ressource est dans l’espace de travail Log Analytics. En cas d’utilisation sans ID de ressource, il peut s’agir de l’URL ou de l’adresse IP d’un point de terminaison public. En cas d’utilisation avec un ID de ressource Log Analytics, cela fait référence au nom de domaine complet de l’agent de supervision.
    * **Filtre** : pour les points de terminaison non-Azure, utilisez des filtres pour sélectionner des agents de supervision à partir de l’espace de travail Log Analytics dans la ressource du moniteur de connexion. Si aucun filtre n’est défini, tous les agents appartenant à l’espace de travail Log Analytics peuvent être utilisés pour la supervision.
        * **Type** : affectez comme valeur l’**adresse de l’agent**.
        * **Adresse** : affectez comme valeur le nom de domaine complet de votre agent local.

* **Groupes de tests**
    * **Nom** : Donnez un nom à votre groupe de tests.
    * **Sources** : choisissez parmi les points de terminaison que vous avez créés. Les points de terminaison sources basés sur Azure doivent être dotés de l’extension Azure Network Watcher, et les points de terminaison sources non basés sur Azure doivent avoir un agent Azure Log Analytics installé. Pour installer un agent pour votre source, consultez [Installer des agents de surveillance](./connection-monitor-overview.md#install-monitoring-agents).
    * **Destinations** :  choisissez parmi les points de terminaison que vous avez créés. Vous pouvez surveiller la connectivité aux machines virtuelles Azure ou aux points de terminaison (adresses IP publiques, URL ou FQDN) en les spécifiant en tant que destinations. Au sein d'un même groupe de tests, vous pouvez ajouter des machines virtuelles Azure, des URL Office 365, des URL Dynamics 365 et des points de terminaison personnalisés.
    * **Désactiver** : désactivez la supervision de toutes les sources et destinations spécifiées par le groupe de tests.

* **Configurations de test**
    * **Nom** : donnez un nom à la configuration de test.
    * **TestFrequencySec** : choisissez la fréquence à laquelle les sources effectuent un test Ping des destinations à l’aide du protocole et du port que vous avez spécifiés. Vous pouvez choisir 30 secondes, 1 minute, 5 minutes, 15 minutes ou 30 minutes. Les sources testent la connectivité aux destinations en fonction de la valeur que vous avez choisie. Par exemple, si vous sélectionnez 30 secondes, les sources vérifient la connectivité à la destination au moins une fois toutes les 30 secondes.
    * **Protocole** : choisissez TCP, ICMP, HTTP ou HTTPS. En fonction du protocole, vous pouvez également sélectionner les configurations propres au protocole suivantes :
        * **preferHTTPS** : spécifiez s’il faut utiliser HTTPS plutôt que HTTP.
        * **port** : spécifiez le port de destination de votre choix.
        * **disableTraceRoute** : empêchez les sources de découvrir la topologie et la durée des boucles tronçon par tronçon. Cela s’applique aux groupes de tests avec TCP ou ICMP.
        * **method** : sélectionnez la méthode de requête HTTP (GET ou POST). Cela s’applique aux configurations de test avec HTTP.
        * **path** : spécifiez les paramètres de chemin à ajouter à l’URL.
        * **validStatusCodes** : choisissez les codes d’état applicables. Si le code de réponse ne correspond pas, un message de diagnostic s’affiche.
        * **requestHeaders** : spécifiez les chaînes d’en-tête de requête personnalisées qui sont passées à la destination.
    * **Seuil de succès** : définissez des seuils sur les paramètres réseau suivants :
        * **checksFailedPercent** : Définissez le pourcentage de vérifications qui peuvent échouer lorsque les sources vérifient la connectivité aux destinations à l’aide des critères que vous avez spécifiés. Pour le protocole TCP ou ICMP, le pourcentage de vérifications qui ont échoué peut être égal au pourcentage de perte de paquets. Pour le protocole HTTP, ce champ représente le pourcentage de requêtes HTTP qui n'ont reçu aucune réponse.
        * **roundTripTimeMs** : définissez combien de temps les sources peuvent prendre pour se connecter à la destination sur la configuration de test, en millisecondes.

## <a name="scale-limits"></a>Limites de mise à l’échelle

Les moniteurs de connexion présentent les limites suivantes en termes de mise à l'échelle :

* Nombre maximum de moniteurs de connexion par abonnement et par région : 100
* Nombre maximum de groupes de tests par moniteur de connexion : 20
* Nombre maximum de sources et de destinations par moniteur de connexion : 100
* Nombre maximum de configurations de test par moniteur de connexion :

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment analyser les données de surveillance et définir des alertes](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Découvrez [comment diagnostiquer des problèmes dans votre réseau](./connection-monitor-overview.md#diagnose-issues-in-your-network).
