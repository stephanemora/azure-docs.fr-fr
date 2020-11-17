---
title: Créer un Moniteur de connexion – PowerShell
titleSuffix: Azure Network Watcher
description: Découvrez comment créer un Moniteur de connexion à l’aide de PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 532f045233f26a9a2933a19ae7a0a893195ad33f
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384053"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Créer un Moniteur de connexion à l’aide de PowerShell

Découvrez comment créer une instance Moniteur de connexion pour surveiller la communication entre vos ressources à l’aide de PowerShell.

## <a name="before-you-begin"></a>Avant de commencer 

Dans les moniteurs de connexion que vous créez à l’aide de la fonctionnalité Moniteur de connexion, vous pouvez ajouter des machines locales et des machines virtuelles Azure en tant que sources. Ces moniteurs de connexion peuvent également surveiller la connectivité aux points de terminaison. Les points de terminaison peuvent se trouver sur Azure ou sur toute autre URL ou adresse IP.

Le Moniteur de connexion inclut les entités suivantes :

* **Ressource de moniteur de connexion** : ressource Azure spécifique à la région. Toutes les entités ci-dessous sont des propriétés d'une ressource de moniteur de connexion.
* **Point de terminaison** : source ou destination qui participe aux vérifications de la connectivité. Les machines virtuelles Azure, les agents locaux, les URL et les adresses IP sont des exemples de points de terminaison.
* **Configuration de test** : configuration spécifique à un protocole dans le cadre d'un test. En fonction du protocole que vous avez choisi, vous pouvez définir le port, les seuils, la fréquence de test et d'autres paramètres.
* **Groupe de tests** : groupe contenant les points de terminaison sources, les points de terminaison de destination et les configurations de test. Un moniteur de connexion peut contenir plusieurs groupes de tests.
* **Test** : combinaison d'un point de terminaison source, d'un point de terminaison de destination et d'une configuration de test. Un test correspond au niveau le plus granulaire auquel les données de surveillance sont disponibles. Les données de surveillance incluent le pourcentage de vérifications qui ont échoué et la durée des boucles.

    ![Diagramme illustrant un moniteur de connexion, avec définition de la relation entre les groupes de tests et les tests](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Étapes de création avec PowerShell

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

* connectionMonitorName - Nom de la ressource du moniteur de connexion

* SUB - ID de l’abonnement dans lequel vous souhaitez créer un moniteur de connexion (CM)

* NW - ID de ressource Network Watcher dans lequel CM sera créé 

* location - Région dans laquelle le moniteur de connexion sera créé

* Points de terminaison
    * name - Nom unique de chaque point de terminaison
    * resourceId - Pour les points de terminaison Azure, l’ID de ressource fait référence à l’ID de ressource d’Azure Resource Manager pour les machines virtuelles. Pour les points de terminaison non Azure, l’ID de ressource fait référence à l’ID de ressource d’Azure Resource Manager pour l’espace de travail Log Analytics lié aux agents non Azure.
    * adress - Applicable uniquement lorsque l’ID de ressource n’est pas spécifié ou si l’ID de ressource est l’espace de travail Log Analytics. Si utilisée avec l’ID de ressource Log Analytics, fait référence au nom de domaine complet de l’agent qui peut être utilisé pour l’analyse. Si utilisée sans ID de ressource, il peut s’agir de l’URL ou de l’adresse IP d’un point de terminaison public.
    * filter - Pour les points de terminaison non-Azure, utilisez filter pour sélectionner des agents dans l’espace de travail Log Analytics qui sera utilisé pour l’analyse dans la ressource du moniteur de connexion. Si les filtres ne sont pas définis, tous les agents appartenant à l’espace de travail Log Analytics peuvent être utilisés pour l’analyse.
        * type – Définissez le type comme « Agent Adress » (Adresse de l’agent).
        * adress – Définissez l’adresse comme nom de domaine complet de votre agent local.

* Groupes de test
    * name - Donnez un nom à votre groupe de test.
    * testConfigurations - Configurations de test basées sur les points de terminaison sources qui se connectent aux points de terminaison de destination.
    * sources - Choisissez parmi les points de terminaison créés ci-dessus. Les points de terminaison sources basés sur Azure doivent être dotés de l’extension Azure Network Watcher, et les points de terminaison sources non basés sur Azure doivent avoir l’agent Azure Log Analytics installé. Pour installer un agent pour votre source, consultez [Installer des agents de surveillance](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
    * destinations - Choisissez parmi les points de terminaison créés ci-dessus. Vous pouvez surveiller la connectivité aux machines virtuelles Azure ou aux points de terminaison (adresses IP publiques, URL ou FQDN) en les spécifiant en tant que destinations. Au sein d'un même groupe de tests, vous pouvez ajouter des machines virtuelles Azure, des URL Office 365, des URL Dynamics 365 et des points de terminaison personnalisés.
    * disable - Utilisez ce champ pour désactiver la surveillance de toutes les sources et destinations spécifiées par le groupe de tests.

* Configurations de test
    * name - Nom de la configuration de test.
    * testFrequencySec - Choisissez la fréquence à laquelle les sources effectueront un test Ping des destinations à l’aide du protocole et du port que vous avez spécifiés. Vous pouvez choisir 30 secondes, 1 minute, 5 minutes, 15 minutes ou 30 minutes. Les sources testeront la connectivité aux destinations en fonction de la valeur que vous avez choisie. Par exemple, si vous sélectionnez 30 secondes, les sources vérifieront la connectivité à la destination au moins une fois toutes les 30 secondes.
    * protocol - Vous pouvez choisir entre TCP, ICMP, HTTP ou HTTPS. Selon le protocole, vous pouvez effectuer des configurations spécifiques au protocole.
        * preferHTTPS - Spécifiez s’il faut utiliser HTTPS plutôt que HTTP.
        * port - Spécifiez le port de destination de votre choix.
        * disableTraceRoute - Ce champ s’applique aux groupes de tests utilisant le protocole TCP ou ICMP. Cela empêcher les sources de découvrir la topologie et la durée des boucles tronçon par tronçon.
    * successThreshold - Vous pouvez définir des seuils sur les paramètres réseau suivants :
        * checksFailedPercent - Définissez le pourcentage de vérifications qui peuvent échouer lorsque les sources vérifient la connectivité aux destinations à l’aide des critères que vous avez spécifiés. Pour le protocole TCP ou ICMP, le pourcentage de vérifications qui ont échoué peut être égal au pourcentage de perte de paquets. Pour le protocole HTTP, ce champ représente le pourcentage de requêtes HTTP qui n'ont reçu aucune réponse.
        * roundTripTimeMs - Définissez la durée des boucles en millisecondes pour déterminer combien de temps les sources peuvent prendre pour se connecter à la destination sur la configuration de test.

## <a name="scale-limits"></a>Limites de mise à l’échelle

Les moniteurs de connexion présentent les limites suivantes en termes de mise à l'échelle :

* Nombre maximum de moniteurs de connexion par abonnement et par région : 100
* Nombre maximum de groupes de tests par moniteur de connexion : 20
* Nombre maximum de sources et de destinations par moniteur de connexion : 100
* Nombre maximum de configurations de test par moniteur de connexion :

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment analyser les données de surveillance et définir des alertes](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts).
* Découvrez [comment diagnostiquer des problèmes dans votre réseau](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network).
