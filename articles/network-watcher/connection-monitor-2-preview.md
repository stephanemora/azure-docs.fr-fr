---
title: Moniteur de connexion (préversion) | Microsoft Docs
description: Découvrez comment utiliser le Moniteur de connexion (préversion) pour superviser les communications réseau dans un environnement distribué
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: c993a08a4163d50a9632055da355e39b5bdde004
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026449"
---
# <a name="overview"></a>Vue d’ensemble

Le Moniteur de connexion (préversion) fournit des fonctionnalités unifiées de contrôle de la connexion de bout en bout dans Azure Network Watcher pour les déploiements de solutions cloud hybride et Azure. Azure Network Watcher fournit des outils pour superviser, diagnostiquer et voir les métriques de connectivité de vos déploiements Azure.

Cas d’usage clés :

- Vous disposez d’une machine virtuelle de serveur web front-end qui communique avec une machine virtuelle de serveur de base de données dans une application multiniveau. Vous souhaitez vérifier la connectivité réseau entre les deux machines virtuelles.
- Vous souhaitez que les machines virtuelles de la région USA Est puissent effectuer un test ping ciblant les machines virtuelles de la région USA Centre pour comparer les temps de réponse du réseau interrégion
- Vous avez plusieurs bureaux locaux dans des villes comme Seattle. connexion aux URL Office 365. Vous souhaitez comparer les latences rencontrées par les utilisateurs qui accèdent aux URL Office 365 depuis Seattle et Ashburn.
- Vous avez configuré une application hybride qui nécessite une connectivité à un point de terminaison Stockage Azure. Vous souhaitez comparer les latences entre un site local et l’application Azure, qui se connectent tous les deux au même point de terminaison Stockage Azure.
- Vous souhaitez vérifier la connectivité entre les machines virtuelles Azure qui hébergent votre application cloud et vos configurations locales.

Dans cette phase de préversion, la solution rassemble le meilleur des deux fonctionnalités clés : le [Moniteur de connexion](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) de Network Watcher et le [Moniteur de connectivité de service](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) de NPM (Network Performance Monitor).

Points forts :

* Expérience unifiée et intuitive pour les besoins de supervision des clouds Azure et hybride
* Supervision de la connectivité interrégion et entre les espaces de travail
* Fréquences de sondage supérieures et meilleure visibilité des performances du réseau
* Alertes plus rapides pour vos déploiements hybrides
* Prise en charge des vérifications de la connectivité HTTP, TCP et ICMP
* Prise en charge des métriques et de Log Analytics pour les initialisations (tearDown) de test Azure et non Azure

![Moniteur de connexion](./media/connection-monitor-2-preview/hero-graphic.png)

Suivez les étapes indiquées ci-dessous pour démarrer la supervision à l’aide du Moniteur de connexion (préversion)

## <a name="step-1-install-monitoring-agents"></a>Étape 1 : Installer des agents de supervision

Le moniteur de connexion s’appuie sur des exécutables légers pour effectuer des vérifications de la connectivité.  Nous prenons en charge les vérifications de la connectivité à partir des environnements Azure et locaux. L’exécutable spécifique à utiliser varie selon que votre machine virtuelle est hébergée dans Azure ou en local.

### <a name="agents-for-azure-virtual-machines"></a>Agents pour les machines virtuelles Azure

Pour que le moniteur de connexion reconnaisse vos machines virtuelles Azure en tant que sources de supervision, vous devez installer l’extension de machine virtuelle Agent Network Watcher (également appelée extension Network Watcher) sur ces machines. L’extension Agent Network Watcher est obligatoire pour déclencher la supervision de bout en bout ainsi que d’autres fonctionnalités avancées sur les machines virtuelles Azure. Vous pouvez [créer une machine virtuelle et installer l’extension Network Watcher](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[sur celle-ci](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm).  Vous pouvez également effectuer l’installation, la configuration et la résolution des problèmes de l’extension Network Watcher pour [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) et [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) séparément.

Si des règles NSG ou des règles de pare-feu bloquent la communication entre la source et la destination, le moniteur de connexion détecte le problème et l’indique sous forme de message de diagnostic dans la topologie. Pour activer le contrôle de la connexion, vérifiez que les règles NSG et les règles de pare-feu autorisent les paquets sur TCP ou ICMP entre la source et la destination.

### <a name="agents-for-on-premise-machines"></a>Agents pour les machines locales

Pour que le moniteur de connexion reconnaisse vos machines locales en tant que sources de supervision, vous devez installer l’agent Log Analytics sur ces machines et activer la solution Network Performance Monitor. Ces agents sont liés aux espaces de travail Log Analytics. Ils nécessitent la configuration d’un ID d’espace de travail et d’une clé primaire pour permettre le démarrage de la supervision.

Si vous souhaitez installer l’agent Log Analytics pour les machines Windows, suivez les instructions indiquées dans [ce lien](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Vérifiez si la destination est accessible, et s’il existe des pare-feu ou des appliances de réseau virtuel (NVA) dans le chemin.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Étape 2 : Activer Network Watcher dans votre abonnement

Tous les abonnements incluant un réseau virtuel sont activés avec Network Watcher. Quand vous créez un réseau virtuel dans votre abonnement, Network Watcher est automatiquement activé dans la région et l’abonnement qui correspondent à ce réseau virtuel. L’activation automatique de Network Watcher n’a aucun impact sur vos ressources ou les frais associés. Vérifiez que Network Watcher n’est pas explicitement désactivé dans votre abonnement. Pour plus d’informations, consultez [Activer Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Étape 3 : Créer un moniteur de connexion 

Le _moniteur de connexion_ supervise la communication à intervalles réguliers. Il vous informe sur les changements liés à l’accessibilité, la latence et la topologie de réseau entre les agents sources et les points de terminaison de destination. Les sources peuvent être des machines virtuelles Azure ou des machines locales sur lesquelles un agent de supervision est installé. Les points de terminaison de destination peuvent être des URL Office 365, des URL Dynamics 365, des URL personnalisées, des ID de ressources de machine virtuelle Azure, des adresses IPv4 ou IPv6, un FQDN ou un nom de domaine.

### <a name="accessing-connection-monitor-preview"></a>Accès au Moniteur de connexion (préversion)

1. Accédez à Network Watcher à l’aide du lien suivant :[https://ms.portal.azure.com/?Microsoft\_Azure\_Network\_connectionmonitorpreview=true#blade/Microsoft\_Azure\_Network/NetworkWatcherMenuBlade/connectionMonitorPreview](https://ms.portal.azure.com/?Microsoft_Azure_Network_connectionmonitorpreview=true#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/connectionMonitorPreview)
2. Cliquez sur l’onglet Moniteur de connexion (préversion) dans la section Supervision du volet gauche de Network Watcher. Cet onglet est visible uniquement si Network Watcher est accessible à l’aide du lien spécifié à l’étape 1.
3. Vous pouvez voir tous les moniteurs de connexion créés à l’aide de l’expérience utilisateur du Moniteur de connexion (préversion). Tous les moniteurs de connexion créés à l’aide de l’expérience utilisateur classique de l’onglet Moniteur de connexion sont visibles sous l’onglet Moniteur de connexion.

    ![Créer un moniteur de connexion](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Création d’un moniteur de connexion

Les moniteurs de connexion créés à l’aide du Moniteur de connexion (préversion) permettent d’ajouter des machines virtuelles locales et Azure en tant que sources, et de superviser la connectivité aux points de terminaison, lesquels peuvent s’étendre à Azure ou toute autre URL/adresse IP.

Voici les entités d’un moniteur de connexion :

* Ressource de moniteur de connexion - Ressource Azure spécifique à la région. Toutes les entités indiquées ci-dessous sont des propriétés d’une ressource de moniteur de connexion.
* Points de terminaison - Toutes les sources et destinations qui participent aux vérifications de la connectivité sont appelées des points de terminaison. Exemples de point de terminaison - Machines virtuelles Azure, agents locaux, URL, adresses IP.
* Configuration de test - Chaque configuration de test est spécifique au protocole. En fonction du protocole choisi, vous pouvez définir le port, les seuils, la fréquence de test et d’autres paramètres.
* Groupe de tests - Chaque groupe de tests contient des points de terminaison sources, des points de terminaison de destination et des configurations de test. Chaque moniteur de connexion peut contenir plusieurs groupes de tests
* Test - La combinaison d’un point de terminaison source, d’un point de terminaison de destination et d’une configuration de test constitue un test. Le test est le niveau le plus bas auquel les données de supervision (pourcentage d’échecs de vérification et délai RTT) sont disponibles

 ![Créer un moniteur de connexion](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>À partir du portail

Pour créer un moniteur de connexion, suivez les étapes ci-dessous :

1. Dans le tableau de bord du Moniteur de connexion (préversion), cliquez dans le coin supérieur gauche sur Créer.
2. Sous l’onglet De base, entrez les informations de votre moniteur de connexion
   1. Nom du moniteur de connexion - Nom de votre moniteur de connexion. Les règles de nommage standard des ressources Azure s’appliquent ici.
   2. Abonnement - Choisissez un abonnement pour votre moniteur de connexion.
   3. Région - Choisissez une région pour votre ressource de moniteur de connexion. Vous pouvez uniquement sélectionner les machines virtuelles sources créées dans cette région.
   4. Configuration de l’espace de travail - Vous pouvez utiliser l’espace de travail par défaut créé par le moniteur de connexion pour stocker vos données de supervision en cochant la case correspondant à l’option par défaut. Pour choisir un espace de travail personnalisé, décochez cette case. Choisissez l’abonnement et la région appropriés pour sélectionner l’espace de travail qui va contenir vos données de supervision.
   5. Cliquez sur Suivant : Groupes de tests pour ajouter des groupes de tests

      ![Créer un moniteur de connexion](./media/connection-monitor-2-preview/create-cm-basics.png)

3. Sous l’onglet Groupes de tests, cliquez sur + Groupe de tests pour ajouter un groupe de tests. Utilisez _Création de groupes de tests dans le moniteur de connexion_ pour ajouter des groupes de tests. Cliquez sur Vérifier + créer pour passer en revue votre moniteur de connexion.

   ![Créer un moniteur de connexion](./media/connection-monitor-2-preview/create-tg.png)

4. Sous l’onglet Vérifier + créer, passez en revue les informations de base et les groupes de tests avant de créer le moniteur de connexion. Pour modifier le moniteur de connexion à partir de la vue Vérifier + créer :
   1. Pour modifier les détails de base, utilisez l’icône en forme de crayon, comme indiqué dans la zone 1 de l’image 2
   2. Pour modifier les groupes de tests de manière individuelle, cliquez sur le groupe de tests à modifier afin de l’ouvrir en mode édition.
   3. La valeur du coût actuel/mois indique le coût durant la phase de préversion. Dans la mesure où l’utilisation du moniteur de connexion est gratuite, la colonne correspondante affiche zéro. La valeur du coût réel/mois indique le prix facturé dans la version en disponibilité générale. Notez que les frais d’ingestion Log Analytics s’appliquent même en phase de préversion.

5. Sous l’onglet Vérifier + créer, cliquez sur le bouton Créer pour créer le moniteur de connexion.

   ![Créer un moniteur de connexion](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Le Moniteur de connexion (préversion) va créer la ressource de moniteur de connexion en arrière-plan.

#### <a name="from-armclient"></a>Depuis Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Commande de déploiement :
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Création de groupes de tests dans le moniteur de connexion

Chaque groupe de tests du moniteur de connexion comprend des sources et des destinations dont les paramètres réseau sont testés pour les échecs de vérification et les délais RTT des configurations de test.

#### <a name="from-portal"></a>À partir du portail

Pour créer un groupe de tests dans un moniteur de connexion, spécifiez la valeur des champs indiqués ci-dessous :

1. Désactiver le groupe de tests - Cochez cette case pour désactiver la supervision de toutes les sources et destinations spécifiées dans le groupe de tests. Cette option est décochée par défaut.
2. Nom - Nom de votre groupe de tests
3. Sources - Vous pouvez spécifier des machines virtuelles Azure et des machines locales en tant que sources, si des agents y sont installés. Consultez l’étape 1 pour installer l’agent spécifique à votre source.
   1. Cliquez sur l’onglet Agents Azure pour sélectionner les agents Azure. Vous voyez uniquement la liste des machines virtuelles liées à la région que vous avez spécifiée au moment de la création du moniteur de connexion. Par défaut, les machines virtuelles sont regroupées dans l’abonnement auquel elles appartiennent, et les groupes sont réduits. Vous pouvez passer du niveau Abonnements à d’autres niveaux de la hiérarchie :

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      Vous pouvez également changer la valeur du champ Grouper par pour démarrer l’arborescence à partir d’un autre niveau. Par exemple :   Grouper par - Réseau virtuel affiche les machines virtuelles qui comportent des agents dans la hiérarchie Réseaux virtuels -\&gt; Sous-réseaux -\&gt; Machines virtuelles avec agents.

      ![Ajouter des sources](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Cliquez sur l’onglet Agents non Azure pour sélectionner des agents locaux. Par défaut, les agents sont regroupés en espaces de travail au sein d’une région. Seuls les espaces de travail pour lesquels la solution Network Performance Monitor est configurée sont listés. Ajoutez la solution Network Performance Monitor à votre espace de travail à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Vous pouvez également utiliser le processus décrit dans [Ajouter des solutions Azure Monitor à partir de la Galerie Solutions](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). Par défaut, la région est sélectionnée sous l’onglet Informations de base de la vue Créer un moniteur de connexion. Vous pouvez changer la région et choisir des agents dans les espaces de travail de la nouvelle région sélectionnée. Vous pouvez également changer la valeur du champ Grouper par pour effectuer un regroupement en fonction des sous-réseaux.

      ![Sources non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Cliquez sur Vérifier pour passer en revue les agents Azure et non Azure sélectionnés.

      ![Passer en revue les sources](./media/connection-monitor-2-preview/review-sources.png)

   4. Cliquez sur Terminé, une fois que vous avez fini de sélectionner les sources.

4. Destinations - Pour superviser la connectivité aux machines virtuelles Azure ou aux points de terminaison (adresses IP publiques, URL, FQDN), spécifiez-les en tant que destinations. Dans un seul groupe de tests, vous pouvez ajouter des machines virtuelles Azure, des URL O365, des URL D365 ou des points de terminaison personnalisés.

   1. Cliquez sur l’onglet Machines virtuelles Azure pour sélectionner des machines virtuelles Azure en tant que destinations. Par défaut, les machines virtuelles Azure sont regroupées dans la hiérarchie d’abonnement de la région sélectionnée sous l’onglet Informations de base, dans la vue Créer un moniteur de connexion. Vous pouvez changer la région, et choisir des machines virtuelles Azure dans la région que vous venez de sélectionner. Vous pouvez descendre dans la hiérarchie (« drill down ») à partir du niveau Abonnements vers d’autres niveaux, par exemple Agents Azure.

      ![Ajouter des destinations](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Ajouter des destinations 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Cliquez sur l’onglet Points de terminaison pour sélectionner des machines virtuelles Azure en tant que destinations. La liste des points de terminaison contient des URL de test O365 et D365, regroupées par nom.  Vous pouvez également choisir un point de terminaison créé dans d’autres groupes de tests du même moniteur de connexion. Pour ajouter un nouveau point de terminaison, cliquez sur + Point de terminaison dans le coin supérieur droit de l’écran, puis indiquez l’URL/l’adresse IP/le FQDN et le nom du point de terminaison

      ![Ajouter des points de terminaison](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Cliquez sur Vérifier pour passer en revue les agents Azure et non Azure sélectionnés.
   4. Cliquez sur Terminé, une fois que vous avez fini de sélectionner les sources.

5. Configuration de test - Vous pouvez associer n’importe quel nombre de configurations de test dans un groupe de tests donné. Le portail fixe la limite à une seule configuration de test par groupe de tests, mais vous pouvez utiliser Armclient pour en ajouter d’autres.
   1. Nom - Nom de la configuration de test
   2. Protocole - Vous pouvez choisir entre TCP, ICMP ou HTTP. Pour remplacer HTTP par HTTPS, sélectionnez la valeur HTTP en tant que protocole, et la valeur 443 en tant que port
   3. Créer une configuration de test TCP - Cette case à cocher est visible uniquement si vous sélectionnez la valeur HTTP dans le champ Protocole. Activez ce champ afin de créer une autre configuration de test en utilisant les mêmes sources et destinations spécifiées aux étapes 3 et 4 pour le protocole TCP/ICMP. La configuration de test créée se nomme "\&lt;nom spécifié dans 5.a\&gt;\_networkTestConfig"
   4. Désactiver traceroute - Ce champ s’applique aux groupes de tests utilisant le protocole TCP ou ICMP.  Cochez cette case pour empêcher les sources de découvrir la topologie et la durée d’aller-retour tronçon par tronçon.
   5. Port de destination - Vous pouvez personnaliser ce champ pour insérer le port de destination de votre choix.
   6. Fréquence de test - Ce champ détermine la fréquence à laquelle les sources vont effectuer un test Ping des destinations à l’aide du protocole et du port spécifiés ci-dessus. Vous pouvez choisir entre 30 secondes, 1 minute, 5 minutes, 15 minutes et 30 minutes. Les sources vont tester la connectivité aux destinations en fonction de la valeur que vous choisissez.  Par exemple, si vous sélectionnez 30 secondes, les sources vérifient la connectivité à la destination au moins une fois par période de 30 secondes.
   7. Seuils de succès - Vous pouvez définir des seuils pour les paramètres réseau indiqués ci-dessous
      1. % de vérifications ayant échoué - Pourcentage de vérifications non réussies quand les sources vérifient la connectivité aux destinations en fonction des critères spécifiés ci-dessus. Pour le protocole TCP/ICMP, le pourcentage d’échecs de vérification peut être égal au pourcentage de pertes de paquets. Pour le protocole HTTP, ce champ représente le nombre de requêtes HTTP qui n’ont pas obtenu de réponse.
      2. Délai RTT en millisecondes - Durée d’aller-retour en millisecondes au moment où les sources se connectent aux destinations via la configuration de test spécifiée ci-dessus.

      ![Ajouter un groupe de tests](./media/connection-monitor-2-preview/add-test-config.png)

Toutes les sources et destinations ajoutées à un groupe de tests avec la configuration de test spécifiée se décomposent en tests individuels. Par exemple :

* Groupe de tests : Groupe de tests 1
* Sources : 3 (A, B, C)
* Destinations : 2 (D, E)
* Configuration de test : 2 (Config 1, Config 2)
* Tests créés : Total = 12

| **Numéro de test** | **Source** | **Destination** | **Nom de la configuration de test** |
| --- | --- | --- | --- |
| 1 | Un | D | Config 1 |
| 2 | Un | D | Config 2 |
| 3 | Un | E | Config 1 |
| 4 | Un | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>Limites de mise à l’échelle

* Nombre maximal de moniteurs de connexion par abonnement et par région - 100
* Nombre maximal de groupes de tests par moniteur de connexion - 20
* Nombre maximal de sources + destinations par moniteur de connexion - 100
* Nombre maximal de configurations de test par moniteur de connexion - 20 via Armclient. 2 via le portail.

## <a name="step-4--data-analysis-and-alerts"></a>Étape 4 :  Analyse des données et alertes

Une fois qu’un moniteur de connexion est créé, les sources vérifient la connectivité aux destinations en fonction de la configuration de test spécifiée.

### <a name="checks-in-a-test"></a>Vérifications dans un test

En fonction du protocole sélectionné par un utilisateur dans la configuration de test, le Moniteur de connexion (préversion) exécute une série de vérifications pour la paire source/destination selon la fréquence de test choisie.

Si le protocole HTTP est sélectionné, le service calcule le nombre de réponses HTTP ayant retourné un code de réponse pour déterminer le pourcentage d’échecs de vérification.  Pour calculer le délai RTT, nous mesurons le temps nécessaire à la réception de la réponse à un appel HTTP.

Si le protocole TCP ou ICMP est sélectionné, le service calcule le pourcentage de paquets pour déterminer le pourcentage d’échecs de vérification. Pour calculer le délai RTT, nous mesurons le temps nécessaire à la réception de l’ACK pour les paquets envoyés. Si vous avez activé les données traceroute pour vos tests réseau, vous pouvez voir les pertes et la latence tronçon par tronçon de votre réseau local.

### <a name="states-of-a-test"></a>États d’un test

En fonction des données retournées par les vérifications dans un test, chaque test peut avoir les états suivants :

* Réussite = Quand les valeurs réelles du pourcentage d’échecs de vérification et du délai RTT se situent dans les seuils spécifiés
* Échec = quand les valeurs réelles du pourcentage d’échecs de vérification ou du délai RTT se situent au-delà des seuils spécifiés. Si aucun seuil n’est spécifié, un test est marqué à l’état d’échec quand le pourcentage d’échecs de vérification est égal à 100 %
* Avertissement - Quand les critères du pourcentage d’échecs de vérification ne sont pas spécifiés. Dans ce cas, le Moniteur de connexion (préversion) utilise un critère automatiquement défini en tant que valeur seuil. Quand ce seuil est dépassé, l’état du test a la valeur Avertissement

### <a name="data-collection-analysis-and-alerts"></a>Collecte de données, analyse et alertes

Toutes les données collectées par le Moniteur de connexion (préversion) sont stockées dans l’espace de travail Log Analytics configuré au moment de la création du moniteur de connexion. Les données de supervision sont également disponibles dans les métriques d’Azure Monitor. Vous pouvez utiliser Log Analytics pour conserver vos données de supervision aussi longtemps que vous le souhaitez. Toutefois, par défaut, Azure Monitor stocke les métriques pendant 30 jours **.** Vous pouvez ensuite [définir des alertes basées sur des métriques pour les données](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Supervision des tableaux de bord dans la solution Moniteur de connexion

Vous voyez s’afficher une liste de moniteurs de connexion auxquels vous avez accès pour les abonnements, les régions, l’horodatage ainsi que les types de source et de destination sélectionnés.

Quand vous accédez au Moniteur de connexion (préversion) à partir du service Network Watcher, vous pouvez choisir d’**Afficher par** :

* Moniteur de connexion (par défaut) - Liste de tous les moniteurs de connexion créés pour les abonnements, les régions, l’horodatage ainsi que les types de source et de destination choisis.
* Groupes de tests - Liste de tous les groupes de tests créés pour les abonnements, les régions, l’horodatage ainsi que les types de source et de destination choisis. Ces groupes de tests ne sont pas filtrés sur le moniteur de connexion
* Tests - Liste de tous les tests en cours d’exécution pour les abonnements, les régions, l’horodatage ainsi que les types de source et de destination choisis. Ces tests ne sont pas filtrés sur le moniteur de connexion ou les groupes de tests.

Dans le tableau de bord, vous pouvez développer chaque moniteur de connexion en groupes de tests, et chaque groupe de tests en divers tests individuels qui s’y exécutent. Indiqué par [1] dans l’image ci-dessous.

Vous pouvez filtrer cette liste en fonction des critères suivants :

* Filtres de niveau supérieur - Abonnements, régions, horodatage, types de source et de destination. Indiqué par [2] dans l’image ci-dessous.
* Filtres basés sur l’état - Filtres de deuxième niveau basés sur l’état du moniteur de connexion, du groupe de tests ou du test. Indiqué par [3] dans l’image ci-dessous.
* Champ de recherche - Choisissez Tout pour effectuer une recherche générique. Pour effectuer une recherche sur une entité spécifique, utilisez la liste déroulante afin d’affiner les résultats de la recherche. Indiqué par [4] dans l’image ci-dessous.

![Filtrer les tests](./media/connection-monitor-2-preview/cm-view.png)

Par exemple :

1. Pour consulter tous les tests présents dans le Moniteur de connexion (préversion) où l’adresse IP source = 10.192.64.56 :
   1. Passez à une vue par « Tests »
   2. Champ de recherche = 10.192.64.56
   3. Utilisez la liste déroulante à côté de la valeur pour sélectionner Sources
2. Pour filtrer uniquement les tests non réussis dans le Moniteur de connexion (préversion) où l’adresse IP source = 10.192.64.56
   1. Passez à une vue par « Tests »
   2. Sélectionnez Échec dans les filtres basés sur l’état.
   3. Champ de recherche = 10.192.64.56
   4. Utilisez la liste déroulante à côté de la valeur pour sélectionner Sources
3. Pour filtrer uniquement les tests non réussis dans le Moniteur de connexion (préversion) où la destination a la valeur outlook.office365.com
   1. Passez à une vue par « Tests »
   2. Sélectionnez Échec dans les filtres basés sur l’état.
   3. Champ de recherche = outlook.office365.com
   4. Utilisez la liste déroulante située à côté de la valeur pour sélectionner Destinations

   ![Échecs des tests](./media/connection-monitor-2-preview/tests-view.png)

Si vous souhaitez afficher les tendances du pourcentage d’échecs de vérification et du délai RTT pour :

1. Moniteur de connexion
   1. Cliquez sur le moniteur de connexion à examiner en détail
   2. Par défaut, vous voyez les données de supervision par « Groupes de tests »

      ![Voir les métriques par](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Choisissez le groupe de tests à examiner en détail

      ![Métriques par groupes de tests](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Vous voyez le top 5 des tests non réussis en fonction du pourcentage d’échecs de vérification ou du délai RTT en ms pour le groupe de tests choisi à l’étape précédente. Pour chaque test, vous voyez des courbes de tendance relatives au pourcentage d’échecs de vérification et au délai RTT en ms
   5. Sélectionnez un test dans la liste ci-dessus, ou choisissez un autre test à examiner en détail.
   6. Pour l’intervalle de temps sélectionné et le pourcentage d’échecs de vérification, vous voyez les valeurs seuil et réelles. Pour le délai RTT en ms, vous voyez les valeurs seuil, moyennes, minimales et maximales.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Changez l’intervalle de temps pour voir plus de données
  8. Vous pouvez changer la vue à l’étape b et visualiser les informations en fonction des sources, des destinations ou des configurations de test. Choisissez ensuite une source en fonction des tests non réussis, puis recherchez le top 5 des tests non réussis.  Par exemple :  Choisissez Afficher par : Sources et destinations pour examiner tous les tests qui s’exécutent entre cette combinaison dans le moniteur de connexion sélectionné.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Groupe de tests
   1. Cliquez sur le groupe de tests à examiner en détail
   2. Par défaut, vous voyez les données de supervision par « Source + Destination + Configuration de test (test) »

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Choisissez le test à examiner en détail.
   4. Pour l’intervalle de temps sélectionné et le pourcentage d’échecs de vérification, vous voyez les valeurs seuil et réelles. Pour le délai RTT en ms, vous voyez les valeurs seuil, moyennes, minimales et maximales. Vous voyez également les alertes déclenchées spécifiques au test que vous avez sélectionné.
   5. Changez l’intervalle de temps pour voir plus de données
   6. Vous pouvez changer la vue à l’étape b et visualiser les informations en fonction des sources, des destinations ou des configurations de test. Choisissez ensuite une entité pour examiner le top 5 des tests non réussis.  Par exemple :  Choisissez Afficher par : Sources et destinations pour examiner tous les tests qui s’exécutent entre cette combinaison dans le moniteur de connexion sélectionné.

3. Test
   1. Cliquez sur la source + la destination + la configuration de test à examiner en détail.
   2. Pour l’intervalle de temps sélectionné et le pourcentage d’échecs de vérification, vous voyez les valeurs seuil et réelles. Pour le délai RTT en ms, vous voyez les valeurs seuil, moyennes, minimales et maximales. Vous voyez également les alertes déclenchées spécifiques au test que vous avez sélectionné.

      ![Test 1](./media/connection-monitor-2-preview/test-drill.png)

   3. Vous pouvez également cliquer sur Topologie pour voir la topologie de réseau à tout moment.

      ![Test 2](./media/connection-monitor-2-preview/test-topo.png)

   4. Vous pouvez cliquer sur un tronçon de lien du réseau Azure pour voir les problèmes identifiés par le moniteur de connexion. Cette fonctionnalité n’est pas encore disponible pour les réseaux locaux.

       ![Test 3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Journaliser les requêtes dans Azure Monitor Log Analytics

Utilisez Log Analytics pour créer des vues personnalisées de vos données de supervision. Toutes les données affichées dans l’IU proviennent de Log Analytics. Vous pouvez effectuer une analyse interactive des données du dépôt, et corréler les données de différentes sources telles qu’Agent Health et d’autres applications basées sur Log Analytics. Vous pouvez également exporter les données vers Excel, Power BI ou un lien partageable.

#### <a name="metrics-in-azure-monitor"></a>Mesures dans Azure Monitor

Pour les moniteurs de connexion créés avant l’expérience utilisateur du Moniteur de connexion (préversion), les 4 métriques sont disponibles. Pour les moniteurs de connexion créés via l’expérience utilisateur du Moniteur de connexion (préversion), les données sont disponibles uniquement dans le cadre des métriques portant l’étiquette « (préversion) ».

Type de ressource - Microsoft.Network/networkWatchers/connectionMonitors

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % de sondes ayant échoué | Pourcentage | Average | % de sondes de surveillance de connectivité ayant échoué | Aucune dimension |
| AverageRoundtripMs | Avg. Durée aller-retour (ms) | Millisecondes | Average | Durée aller-retour réseau moyenne (ms) pour les sondes de surveillance de connectivité envoyées entre la source et la destination |             Aucune dimension |
| ChecksFailedPercent (préversion) | Pourcentage d’échecs de vérification (préversion) | Pourcentage | Average | Pourcentage d’échecs de vérification pour un test |Liste : - ConnectionMonitorResourceId - SourceAddress - SourceName - SourceResourceId - SourceType - Protocol - DestinationAddress - DestinationName - DestinationResourceId - DestinationType - DestinationPort - TestGroupName - TestConfigurationName - Region |
| RoundTripTimeMs (préversion) | Durée d’aller-retour (ms) (préversion) | Millisecondes | Average | Durée d’aller-retour (ms) pour les vérifications envoyées entre la source et la destination. Cette valeur ne fait pas l’objet d’une moyenne | Liste : - ConnectionMonitorResourceId - SourceAddress - SourceName - SourceResourceId - SourceType - Protocol - DestinationAddress - DestinationName - DestinationResourceId - DestinationType - DestinationPort - TestGroupName - TestConfigurationName - Region |

 ![Métriques de supervision](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Alertes de métrique dans Azure Monitor

Pour créer une alerte :

1. Choisissez la ressource de moniteur de connexion créée à l’aide du Moniteur de connexion (préversion).
2. Vérifiez que « Métrique » s’affiche en tant que type de signal pour la ressource sélectionnée à l’étape précédente.
3. Dans ajouter une condition, choisissez nom du signal comme ChecksFailedPercent (version préliminaire) ou RoundTripTimeMs (préversion) et type de signal comme métriques. Par exemple : Choisissez ChecksFailedPercent(Preview)
4. Toutes les dimensions applicables en fonction des métriques sont listées.  Choisissez le nom et la valeur de dimension. Par exemple : Choisissez l’adresse source, puis indiquez l’adresse IP d’une source impliquée dans la ressource de moniteur de connexion choisie à l’étape 1.
5. Dans Logique d’alerte, choisissez :
   1. Type de condition - Statique
   2. Condition et Seuil
   3. Précision d’agrégation et Fréquence d’évaluation - Le Moniteur de connexion (préversion) met à jour les données à chaque intervalle de 1 minute.
6.  Dans Actions, choisissez votre groupe d’actions.
7. Indiquez les détails de l’alerte.
8. Créer une règle d’alerte

   ![Alertes](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Étape 5 : Diagnostiquer les problèmes de votre réseau

Le moniteur de connexion vous aide à diagnostiquer les problèmes liés à la ressource de moniteur de connexion et à votre réseau. Les problèmes relatifs à votre réseau hybride sont détectés par les agents Log Analytics que vous avez installés à l’étape 1. Les problèmes relatifs à Azure sont détectés par l’extension Network Watcher.  Les problèmes relatifs au réseau hybride sont visibles dans la page Diagnostics. Les problèmes relatifs au réseau Azure sont visibles dans la topologie de réseau.

Pour les réseaux qui comportent des machines virtuelles locales en tant que sources, nous détectons les problèmes suivants :

* Expiration de la requête.
* Point de terminaison non résolu par le DNS : temporaire ou persistant. URL non valide.
* Hôte introuvable.
* Impossibilité pour la source de se connecter à la destination. Cible inaccessible via ICMP.
* Problème de certificat : le certificat client est obligatoire pour l’authentification de l’agent, la liste de révocation de certificats est inaccessible, le nom d’hôte du point de terminaison ne correspond pas au sujet du certificat ou à son autre nom de sujet, le certificat racine est manquant dans le magasin d’autorités de certification de confiance de l’ordinateur local de la source, le certificat SSL a expiré/est non valide/a été révoqué/est incompatible.

Pour les réseaux qui comportent des machines virtuelles Azure en tant que sources, nous détectons les problèmes suivants :

* Problèmes d’agent : arrêt de l’agent, échec de la résolution DNS, absence d’application/d’écouteur à l’écoute sur le port de destination, ouverture du socket impossible
* Problèmes d’état de la machine virtuelle : en cours de démarrage, en cours d’arrêt, arrêtée, en cours de libération, libérée, en cours de redémarrage, non allouée
* Entrée de table ARP manquante
* Blocage du trafic en raison de problèmes liés au pare-feu local ou aux règles NSG
* Passerelle de réseau virtuel : routes manquantes, tunnel entre deux passerelles déconnecté ou manquant, ou deuxième passerelle introuvable par le tunnel, informations d’appairage introuvables
* Route manquante dans MS Edge
* Arrêt du trafic lié aux routes système ou à des règles UDR
* Protocole BGP non activé sur la connexion de passerelle
* Sonde DIP en panne sur Load Balancer
