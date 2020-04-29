---
title: Moniteur de connexion (préversion) | Microsoft Docs
description: Apprenez à utiliser la fonctionnalité Moniteur de connexion (préversion) pour surveiller les communications réseau dans un environnement distribué.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: d926a9f686f0f4c39203b8a217a7c608cfad926e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548115"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Surveillance de la connectivité réseau à l'aide du Moniteur de connexion (préversion)

La fonctionnalité Moniteur de connexion (préversion) permet une surveillance unifiée et de bout en bout des connexions dans Azure Network Watcher. Elle prend en charge les déploiements hybrides et cloud Azure. Network Watcher fournit des outils pour surveiller, diagnostiquer et consulter les métriques de connectivité de vos déploiements Azure.

Voici quelques cas d'usage du Moniteur de connexion (préversion) :

- Votre machine virtuelle de serveur web front-end communique avec une machine virtuelle de serveur de base de données dans une application multiniveau. Vous souhaitez vérifier la connectivité réseau entre les deux machines virtuelles.
- Vous souhaitez que les machines virtuelles de la région USA Est puissent effectuer un test Ping ciblant les machines virtuelles de la région USA Centre, et vous souhaitez comparer les temps de réponse du réseau entre les régions.
- Vous disposez de plusieurs sites locaux à Seattle, Washington et Ashburn (Virginie). Vos sites se connectent aux URL Office 365. Les utilisateurs des URL Office 365 doivent comparer les temps de réponse entre Seattle et Ashburn.
- Votre application hybride doit être connectée à un point de terminaison Stockage Azure. Votre site local et votre application Azure se connectent au même point de terminaison Stockage Azure. Vous souhaitez comparer les temps de réponse du site local avec ceux de l'application Azure.
- Vous souhaitez vérifier la connectivité entre vos installations locales et les machines virtuelles Azure qui hébergent votre application cloud.

Actuellement en phase de préversion, le Moniteur de connexion allie le meilleur des deux fonctionnalités suivantes : la fonctionnalité [Moniteur de connexion](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) de Network Watcher et la fonctionnalité [Moniteur de connectivité de service](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) de Network Performance Monitor (NPM).

Le Moniteur de connexion (préversion) présente notamment les avantages suivants :

* Expérience unifiée et intuitive pour les besoins de supervision des clouds Azure et hybride
* Surveillance de la connectivité entre les régions et entre les espaces de travail
* Fréquences de sondage supérieures et meilleure visibilité des performances du réseau
* Alertes plus rapides pour vos déploiements hybrides
* Prise en charge des vérifications de la connectivité reposant sur HTTP, TCP et ICMP 
* Prise en charge des métriques et de Log Analytics pour les initialisations (tearDown) de test Azure et non Azure

![Diagramme montrant comment le Moniteur de connexion interagit avec les machines virtuelles Azure, les hôtes non Azure, les points de terminaison et les emplacements de stockage de données](./media/connection-monitor-2-preview/hero-graphic.png)

Pour commencer à utiliser le Moniteur de connexion (préversion) à des fins de surveillance, procédez comme suit : 

1. Installez des agents de surveillance.
1. Activez Network Watcher sur votre abonnement.
1. Créez un moniteur de connexion.
1. Configurez l'analyse des données et les alertes.
1. Diagnostiquez les problèmes liés à votre réseau.

Pour plus d'informations, lisez les sections suivantes.

## <a name="install-monitoring-agents"></a>Installer des agents de supervision

Le Moniteur de connexion s'appuie sur des exécutables légers pour effectuer les vérifications de la connectivité.  Il prend en charge les vérifications de la connectivité à partir des environnements Azure et des environnements locaux. L'exécutable à utiliser varie selon que votre machine virtuelle est hébergée sur Azure ou en local.

### <a name="agents-for-azure-virtual-machines"></a>Agents pour les machines virtuelles Azure

Pour que le Moniteur de connexion reconnaisse vos machines virtuelles Azure comme sources de surveillance, installez l'extension de machine virtuelle Network Watcher Agent sur celles-ci. Cette extension est également connue sous le nom d'*extension Network Watcher*. Les machines virtuelles Azure ont besoin de cette extension pour déclencher une surveillance de bout en bout et d'autres fonctionnalités avancées. 

Vous pouvez installer l'extension Network Watcher au moment de la [création d'une machine virtuelle](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). Vous pouvez également procéder à l'installation, à la configuration et à la résolution des problèmes liés à l'extension Network Watcher séparément pour [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) et [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Les règles d'un groupe de sécurité réseau (NSG) ou d'un pare-feu peuvent empêcher la communication entre la source et la destination. Le Moniteur de connexion détecte ce problème et le signale sous la forme d'un message de diagnostic dans la topologie. Pour activer la vérification de la connectivité, vérifiez que les règles NSG et les règles de pare-feu autorisent les paquets sur TCP ou ICMP entre la source et la destination.

### <a name="agents-for-on-premises-machines"></a>Agents pour machines locales

Pour que le Moniteur de connexion reconnaisse vos machines locales en tant que sources de surveillance, installez l'agent Log Analytics sur les machines. Puis activez la solution Network Performance Monitor. Ces agents sont liés aux espaces de travail Log Analytics. Par conséquent, vous devez configurer l'ID de l'espace de travail et la clé primaire pour permettre aux agents d'entamer la surveillance.

Pour installer l'agent Log Analytics pour machines Windows, consultez [Extension de machine virtuelle Azure Monitor pour Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Si le chemin inclut des pare-feu ou des appliances virtuelles réseau (NVA), assurez-vous que la destination est accessible.

## <a name="enable-network-watcher-on-your-subscription"></a>Activer Network Watcher dans votre abonnement

Tous les abonnements disposant d'un réseau virtuel sont activés avec Network Watcher. Lorsque vous créez un réseau virtuel sur votre abonnement, Network Watcher est automatiquement activé dans la région et sur l'abonnement correspondant à ce réseau virtuel. Cette activation automatique n'affecte pas vos ressources et n'entraîne pas de frais. Vérifiez que Network Watcher n’est pas explicitement désactivé dans votre abonnement. 

Pour plus d’informations, consultez [Activer Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion 

Le Moniteur de connexion surveille la communication à intervalles réguliers. Il vous informe des changements en matière d'accessibilité et de latence. Vous pouvez également vérifier la topologie actuelle et historique du réseau entre les agents sources et les points de terminaison de destination.

Les sources peuvent être des machines virtuelles Azure ou des machines locales sur lesquelles un agent de surveillance est installé. Les points de terminaison de destination peuvent être des URL Office 365, des URL Dynamics 365, des URL personnalisées, des ID de ressources de machine virtuelle Azure, des adresses IPv4 ou IPv6, un FQDN ou un nom de domaine.

### <a name="access-connection-monitor-preview"></a>Accéder au Moniteur de connexion (préversion)

1. Sur la page d'accueil du portail Azure, accédez à **Network Watcher**.
1. Sur la gauche, dans la section **Surveillance**, sélectionnez **Moniteur de connexion (préversion)** .
1. Tous les moniteurs de connexion créés dans la fonctionnalité Moniteur de connexion (préversion) sont répertoriés. Pour afficher les moniteurs de connexion créés dans l'expérience utilisateur classique du Moniteur de connexion, accédez à l'onglet **Moniteur de connexion**.

    ![Capture d'écran illustrant des moniteurs de connexion créés dans le Moniteur de connexion (préversion)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion

Dans les moniteurs de connexion que vous créez à partir de la fonctionnalité Moniteur de connexion (préversion), vous pouvez aussi bien ajouter des machines locales et des machines virtuelles Azure en tant que sources. Ces moniteurs de connexion peuvent également surveiller la connectivité aux points de terminaison. Les points de terminaison peuvent se trouver sur Azure ou sur toute autre URL ou adresse IP.

Le Moniteur de connexion (préversion) inclut les entités suivantes :

* **Ressource de moniteur de connexion** : ressource Azure spécifique à la région. Toutes les entités ci-dessous sont des propriétés d'une ressource de moniteur de connexion.
* **Point de terminaison** : source ou destination qui participe aux vérifications de la connectivité. Les machines virtuelles Azure, les agents locaux, les URL et les adresses IP sont des exemples de points de terminaison.
* **Configuration de test** : configuration spécifique à un protocole dans le cadre d'un test. En fonction du protocole que vous avez choisi, vous pouvez définir le port, les seuils, la fréquence de test et d'autres paramètres.
* **Groupe de tests** : groupe contenant les points de terminaison sources, les points de terminaison de destination et les configurations de test. Un moniteur de connexion peut contenir plusieurs groupes de tests.
* **Test** : combinaison d'un point de terminaison source, d'un point de terminaison de destination et d'une configuration de test. Un test correspond au niveau le plus granulaire auquel les données de surveillance sont disponibles. Les données de surveillance incluent le pourcentage de vérifications qui ont échoué et la durée des boucles.

 ![Diagramme illustrant un moniteur de connexion, avec définition de la relation entre les groupes de tests et les tests](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Créer un moniteur de connexion à partir du portail Azure

Pour créer un moniteur de connexion à partir du portail Azure, procédez comme suit :

1. En haut à gauche du tableau de bord **Moniteur de connexion (préversion)** , sélectionnez **Créer**.
1. Sous l'onglet **Informations de base**, entrez les informations relatives à votre moniteur de connexion :
   * **Nom du moniteur de connexion** : ajoutez le nom de votre moniteur de connexion. Appliquez les règles de nommage standard des ressources Azure.
   * **Abonnement** : choisissez un abonnement pour votre moniteur de connexion.
   * **Région** : choisissez une région pour votre moniteur de connexion. Vous ne pouvez sélectionner que les machines virtuelles sources créées dans cette région.
   * **Configuration de l'espace de travail** : votre espace de travail contient vos données de surveillance. Vous pouvez utiliser un espace de travail personnalisé ou l'espace de travail par défaut. 
       * Pour utiliser l'espace de travail par défaut, cochez la case. 
       * Pour choisir un espace de travail personnalisé, décochez la case. Choisissez ensuite l'abonnement et la région de votre espace de travail personnalisé. 
1. En bas de l'onglet, sélectionnez **Suivant : Groupes de tests**.

   ![Capture d'écran illustrant l'onglet Informations de base du Moniteur de connexion](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Sous l'onglet **Groupes de tests**, sélectionnez **+ Groupe de tests**. Pour configurer vos groupes de tests, consultez [Créer des groupes de tests dans le Moniteur de connexion](#create-test-groups-in-a-connection-monitor). 
1. En bas de l'onglet, sélectionnez **Suivant : Examiner + créer** pour examiner votre moniteur de connexion.

   ![Capture d'écran illustrant l'onglet Groupes de tests et le volet dans lequel vous ajoutez les informations relatives au groupe de tests](./media/connection-monitor-2-preview/create-tg.png)

1. Sous l'onglet **Examiner + créer**, passez en revue les informations de base et les groupes de tests avant de créer le moniteur de connexion. Si vous avez besoin d'apporter des modifications au moniteur de connexion :
   * Pour modifier les informations de base, sélectionnez l'icône Crayon.
   * Pour modifier un groupe de tests, sélectionnez-le.

   > [!NOTE] 
   > L'onglet **Examiner + créer** indique le coût mensuel pendant la phase de préversion du Moniteur de connexion. Actuellement, rien n'apparaît dans la colonne **COÛT ACTUEL**. Lorsque le Moniteur de connexion sera mis à la disposition générale, cette colonne affichera les frais mensuels. 
   > 
   > En revanche, des frais d'ingestion de Log Analytics s'appliquent pendant la phase de préversion du Moniteur de connexion.

1. Lorsque vous êtes prêt à créer le moniteur de connexion, sélectionnez **Créer** en bas de l'onglet **Examiner + créer**.

   ![Capture d'écran du Moniteur de connexion illustrant l'onglet Examiner + créer](./media/connection-monitor-2-preview/review-create-cm.png)

Le Moniteur de connexion (préversion) crée le moniteur de connexion en arrière-plan.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Créer un moniteur de connexion à l'aide d'ARMClient

Utilisez le code suivant pour créer un moniteur de connexion à l'aide d'ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

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

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

La commande de déploiement est la suivante :
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Créer des groupes de tests dans un moniteur de connexion

Chaque groupe de tests d'un moniteur de connexion comprend des sources et des destinations dont les paramètres réseau sont testés. Les tests concernent le pourcentage de vérifications qui échouent et la durée des boucles sur les configurations de test.

Sur le portail Azure, pour créer un groupe de tests relatif à un moniteur de connexion, vous devez spécifier des valeurs dans les champs suivants :

* **Désactiver le groupe de tests** : vous pouvez sélectionner ce champ pour désactiver la surveillance de toutes les sources et destinations spécifiées par le groupe de tests. Cette sélection est désactivée par défaut.
* **Nom** : donnez un nom à votre groupe de tests.
* **Sources** : vous pouvez spécifier des machines virtuelles Azure et des machines locales en tant que sources si des agents y sont installés. Pour installer un agent pour votre source, consultez [Installer des agents de surveillance](#install-monitoring-agents).
   * Pour choisir des agents Azure, sélectionnez l'onglet **Agents Azure**. Vous ne voyez ici que les machines virtuelles liées à la région que vous avez spécifiée lors de la création du moniteur de connexion. Par défaut, les machines virtuelles sont regroupées au sein de l'abonnement auquel elles appartiennent. Ces groupes sont réduits. 
   
       Vous pouvez passer du niveau Abonnement à d'autres niveaux de la hiérarchie :

      **Abonnement** > **Groupes de ressources** > **Réseaux virtuels** > **Sous-réseaux** > **Machines virtuelles avec agents**

      Vous pouvez également changer la valeur du champ **Grouper par** pour démarrer l'arborescence à partir d'un autre niveau. Par exemple, si vous effectuez un regroupement par réseau virtuel, vous obtenez la liste des machines virtuelles disposant d'agents dans la hiérarchie **Réseaux virtuels** > **Sous-réseaux** > **Machines virtuelles avec agents**.

      ![Capture d'écran du Moniteur de connexion illustrant le panneau Ajouter des sources et l'onglet Agents Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Pour choisir des agents locaux, sélectionnez l'onglet **Agents non Azure**. Par défaut, les agents sont regroupés par région dans les espaces de travail. La solution Network Performance Monitor est configurée pour tous ces espaces de travail. 
   
       Si nécessaire, procurez-vous Network Performance Monitor à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) pour l'ajouter à votre espace de travail. Pour plus d'informations sur l'ajout de Network Performance Monitor, consultez [Solutions de supervision dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Sur la vue **Créer un moniteur de connexion**, accédez à l'onglet **Informations de base**. La région par défaut est sélectionnée. Si vous changez la région, vous pouvez choisir des agents des espaces de travail de la nouvelle région. Vous pouvez également changer la valeur du champ **Grouper par** pour effectuer un regroupement par sous-réseaux.

      ![Capture d'écran du moniteur de connexion illustrant le panneau Ajouter des sources et l'onglet Agents non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Pour passer en revue les agents Azure et non Azure que vous avez sélectionnés, accédez à l'onglet **Examiner**.

      ![Capture d'écran du moniteur de connexion illustrant le panneau Ajouter des sources et l'onglet Examiner](./media/connection-monitor-2-preview/review-sources.png)

   * Une fois la configuration des sources terminée, sélectionnez **Terminé** en bas du panneau **Ajouter des sources**.

* **Destinations** : pour surveiller la connectivité aux machines virtuelles Azure ou aux points de terminaison (adresses IP publiques, URL ou FQDN), spécifiez-les en tant que destinations. Au sein d'un même groupe de tests, vous pouvez ajouter des machines virtuelles Azure, des URL Office 365, des URL Dynamics 365 et des points de terminaison personnalisés.

    * Pour choisir des machines virtuelles Azure comme destinations, sélectionnez l'onglet **Machines virtuelles Azure**. Par défaut, les machines virtuelles Azure sont regroupées dans une hiérarchie d'abonnement qui se trouve dans la même région que celle que vous avez sélectionnée dans la vue **Créer un moniteur de connexion**, sous l'onglet **Informations de base**. Vous pouvez changer la région, et choisir des machines virtuelles Azure dans la région que vous venez de sélectionner. Vous pouvez ensuite descendre dans la hiérarchie, du niveau Abonnement vers d'autres niveaux, par exemple le niveau Agents Azure.

       ![Capture d'écran du volet Ajouter des destinations illustrant l'onglet Machines virtuelles Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Capture d'écran du volet Ajouter des destinations illustrant le niveau Abonnement](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Pour choisir des points de terminaison en tant que destinations, sélectionnez l'onglet **Points de terminaison**. La liste des points de terminaison comprend les URL de test Office 365 et les URL de test Dynamics 365, regroupées par nom. En plus de ces points de terminaison, vous pouvez choisir un point de terminaison qui a été créé dans un autre groupe de tests du même moniteur de connexion. 
    
        Pour ajouter un nouveau point de terminaison, sélectionnez **+ Points de terminaison** en haut à droite. Fournissez ensuite un nom de point de terminaison ainsi qu'une URL, une adresse IP ou un FQDN.

       ![Capture d'écran montrant où ajouter des points de terminaison en tant que destinations dans le Moniteur de connexion](./media/connection-monitor-2-preview/add-endpoints.png)

    * Pour passer en revue les points de terminaison et les machines virtuelles Azure que vous avez choisis, sélectionnez l'onglet **Examiner**.
    * Une fois les destinations choisies, sélectionnez **Terminé**.

* **Configurations de test** : vous pouvez associer des configurations de test au sein d'un groupe de tests. Le portail Azure n'autorise qu'une seule configuration de test par groupe de tests, mais vous pouvez utiliser ARMClient pour en ajouter d'autres.

    * **Nom** : donnez un nom à la configuration de test.
    * **Protocole** : choisissez TCP, ICMP ou HTTP. Pour remplacer HTTP par HTTPS, sélectionnez le protocole **HTTP** et le port **443**.
        * **Créer une configuration de test de réseau** : cette case à cocher n'apparaît que si vous sélectionnez **HTTP** dans le champ **Protocole**. Cochez cette case pour créer une autre configuration de test utilisant les mêmes sources et destinations que celles que vous avez spécifiées ailleurs dans votre configuration. La configuration de test nouvellement créée est nommée `<the name of your test configuration>_networkTestConfig`.
        * **Désactiver traceroute** : ce champ s'applique aux groupes de tests utilisant le protocole TCP ou ICMP. Cochez cette case pour empêcher les sources de découvrir la topologie et la durée des boucles tronçon par tronçon.
    * **Port de destination** : vous pouvez personnaliser ce champ avec le port de destination de votre choix.
    * **Fréquence de test** : utilisez ce champ pour choisir la fréquence à laquelle les sources effectueront un test Ping des destinations à l'aide du protocole et du port que vous avez spécifiés. Vous pouvez choisir 30 secondes, 1 minute, 5 minutes, 15 minutes ou 30 minutes. Les sources testeront la connectivité aux destinations en fonction de la valeur que vous avez choisie.  Par exemple, si vous sélectionnez 30 secondes, les sources vérifieront la connectivité à la destination au moins une fois toutes les 30 secondes.
    * **Seuil de succès** - Vous pouvez définir des seuils sur les paramètres réseau suivants :
       * **Vérifications ayant échoué** : définissez le pourcentage de vérifications qui peuvent échouer lorsque les sources vérifient la connectivité aux destinations à l'aide des critères que vous avez spécifiés. Pour le protocole TCP ou ICMP, le pourcentage de vérifications qui ont échoué peut être égal au pourcentage de perte de paquets. Pour le protocole HTTP, ce champ représente le pourcentage de requêtes HTTP qui n'ont reçu aucune réponse.
       * **Durée des boucles** : définissez la durée des boucles en millisecondes pour déterminer combien de temps les sources peuvent prendre pour se connecter à la destination sur la configuration de test.
    
       ![Capture d'écran montrant où définir une configuration de test dans le Moniteur de connexion](./media/connection-monitor-2-preview/add-test-config.png)

Toutes les sources, destinations et configurations de test que vous ajoutez à un groupe de tests sont réparties en tests individuels. Voici un exemple de répartition des sources et des destinations :

* Groupe de tests : Groupe de tests 1
* Sources : 3 (A, B, C)
* Destinations : 2 (D, E)
* Configurations de test : 2 (Config 1, Config 2)
* Nombre total de tests créés : 12

| Numéro du test | Source | Destination | Configuration de test |
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

Les moniteurs de connexion présentent les limites suivantes en termes de mise à l'échelle :

* Nombre maximum de moniteurs de connexion par abonnement et par région : 100
* Nombre maximum de groupes de tests par moniteur de connexion : 20
* Nombre maximum de sources et de destinations par moniteur de connexion : 100
* Nombre maximum de configurations de test par moniteur de connexion : 
    * 20 via ARMClient
    * 2 via le portail Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analyser les données de surveillance et définir des alertes

Après la création d'un moniteur de connexion, les sources vérifient la connectivité aux destinations en fonction de votre configuration de test.

### <a name="checks-in-a-test"></a>Vérifications dans un test

En fonction du protocole que vous avez choisi dans la configuration de test, le Moniteur de connexion (préversion) exécute une série de vérifications pour la paire source-destination. Les vérifications sont exécutées en fonction de la fréquence de test que vous avez choisie.

Si vous utilisez HTTP, le service calcule le nombre de réponses HTTP qui ont renvoyé un code de réponse. Le résultat détermine le pourcentage de vérifications qui ont échoué. Pour calculer la durée des boucles, le service mesure le délai entre un appel HTTP et la réponse.

Si vous utilisez TCP ou ICMP, le service calcule le pourcentage de perte de paquets pour déterminer le pourcentage de vérifications qui ont échoué. Pour calculer la durée des boucles, le service mesure le temps nécessaire à la réception de l'accusé de réception (ACK) pour les paquets qui ont été envoyés. Si vous avez activé les données traceroute pour vos tests réseau, vous pouvez voir les pertes et la latence tronçon par tronçon de votre réseau local.

### <a name="states-of-a-test"></a>États d’un test

Sur la base des données renvoyées par les vérifications, les tests peuvent présenter les états suivants :

* **Réussite** : les valeurs réelles du pourcentage de vérifications qui ont échoué et de la durée des boucles se situent dans les seuils spécifiés.
* **Échec** : les valeurs réelles du pourcentage de vérifications qui ont échoué et de la durée des boucles ont dépassé les seuils spécifiés. Si aucun seuil n'est spécifié, un test atteint l'état Échec lorsque le pourcentage de vérifications qui ont échoué est de 100.
* **Avertissement** : aucun critère n'a été spécifié pour le pourcentage de vérifications qui ont échoué. En l'absence de critères spécifiés, le Moniteur de connexion (préversion) attribue automatiquement un seuil. Lorsque ce seuil est dépassé, l'état du test passe à Avertissement.

### <a name="data-collection-analysis-and-alerts"></a>Collecte de données, analyse et alertes

Les données collectées par le Moniteur de connexion (préversion) sont stockées dans l'espace de travail Log Analytics. Vous avez configuré cet espace de travail lors de la création du Moniteur de connexion. 

Les données de supervision sont également disponibles dans les métriques d’Azure Monitor. Vous pouvez utiliser Log Analytics pour conserver vos données de surveillance aussi longtemps que vous le souhaitez. Par défaut, Azure Monitor ne stocke les données que pendant 30 jours. 

Vous pouvez [définir des alertes basées sur des métriques pour les données](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Tableaux de bord de surveillance

Les tableaux de bord de surveillance présentent la liste des moniteurs de connexion auxquels vous pouvez accéder pour vos abonnements, régions, horodatages, sources et types de destination.

Lorsque vous accédez au Moniteur de connexion (préversion) à partir de Network Watcher, vous pouvez afficher les données par :

* **Moniteur de connexion** : liste de tous les moniteurs de connexion créés pour vos abonnements, régions, horodatages, sources et types de destination. Il s'agit de la vue par défaut.
* **Groupes de tests** : liste de tous les groupes de tests créés pour vos abonnements, régions, horodatages, sources et types de destination. Ces groupes de tests ne sont pas filtrés par moniteurs de connexion.
* **Test** : liste de tous les tests qui s'exécutent pour vos abonnements, régions, horodatages, sources et types de destination. Ces tests ne sont pas filtrés par moniteurs de connexion ou groupes de tests.

Sur l'illustration suivante, les trois vues de données sont signalées par la flèche 1.

Sur le tableau de bord, vous pouvez développer chaque moniteur de connexion pour afficher ses groupes de tests. Vous pouvez ensuite développer chaque groupe de tests pour afficher les tests qui y sont exécutés. 

Vous pouvez filtrer une liste en fonction des critères suivants :

* **Filtres de niveau supérieur** : choisissez des abonnements, régions, horodatages, sources et types de destination. Voir la zone 2 sur l'illustration suivante.
* **Filtres basés sur l'état** : filtres basés sur l'état du moniteur de connexion, du groupe de tests ou du test. Voir la flèche 3 sur l'illustration suivante.
* **Filtres personnalisés** : choisissez **Sélectionner tout** pour effectuer une recherche générique. Pour effectuer une recherche en fonction d'une entité spécifique, sélectionnez celle-ci dans la liste déroulante. Voir la flèche 4 sur l'illustration suivante.

![Capture d'écran montrant comment filtrer les vues des moniteurs de connexion, des groupes de tests et des tests dans le Moniteur de connexion (préversion)](./media/connection-monitor-2-preview/cm-view.png)

Par exemple, pour afficher dans le Moniteur de connexion (préversion) tous les tests dont l'adresse IP source correspond à 10.192.64.56 :
1. Remplacez la vue par **Test**.
1. Dans le champ de recherche, entrez *10.192.64.56*
1. Dans la liste déroulante, sélectionnez **Sources**.

Pour n'afficher dans le Moniteur de connexion (préversion) que les tests qui ont échoué et dont l'adresse IP source correspond à 10.192.64.56 :
1. Remplacez la vue par **Test**.
1. Pour le filtre basé sur l'état, sélectionnez **Échec**.
1. Dans le champ de recherche, entrez *10.192.64.56*
1. Dans la liste déroulante, sélectionnez **Sources**.

Pour n'afficher dans le Moniteur de connexion (préversion) que les tests qui ont échoué et dont la destination est outlook.office365.com :
1. Remplacez la vue par **Test**.
1. Pour le filtre basé sur l'état, sélectionnez **Échec**.
1. Dans le champ de recherche, entrez *outlook.office365.com*.
1. Dans la liste déroulante, sélectionnez **Destinations**.

   ![Capture d'écran montrant une vue filtrée pour n'afficher que les tests qui ont échoué et dont la destination est Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Pour afficher les tendances relatives à la durée des boucles et le pourcentage de vérifications qui ont échoué pour un moniteur de connexion :
1. Sélectionnez le moniteur de connexion à examiner. Par défaut, les données de surveillance sont classées par groupe de tests.

   ![Capture d'écran illustrant les métriques d'un moniteur de connexion, affichées par groupe de tests](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Choisissez le groupe de tests à examiner.

   ![Capture d'écran montrant où sélectionner un groupe de tests](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Vous pouvez afficher les cinq principaux tests qui ont échoué dans votre groupe de tests, en fonction de la durée des boucles ou du pourcentage de vérifications qui ont échoué. Pour chaque test, vous disposez de la durée des boucles et des tendances relatives au pourcentage de vérifications qui ont échoué.
1. Sélectionnez un test dans la liste, ou choisissez un autre test à examiner. Pour votre intervalle de temps et le pourcentage de vérifications qui ont échoué, vous voyez le seuil et les valeurs réelles. Pour la durée des boucles, vous voyez les valeurs correspondant au seuil, à la moyenne, au minimum et au maximum.

   ![Capture d'écran montrant les résultats d'un test en termes de durée des boucles et de pourcentage de vérifications qui ont échoué](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Changez l'intervalle de temps pour afficher plus de données.
1. Modifiez la vue pour afficher les sources, les destinations ou les configurations de test. 
1. Choisissez une source en fonction des tests qui ont échoué, puis recherchez les cinq principaux tests qui ont échoué. Par exemple, choisissez **Afficher par** > **Sources** et **Afficher par** > **Destinations** pour rechercher les tests pertinents dans le moniteur de connexion.

   ![Capture d'écran illustrant les mesures de performances des cinq principaux tests qui ont échoué](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Pour afficher les tendances relatives à la durée des boucles et le pourcentage de vérifications qui ont échoué pour un groupe de tests :

1. Sélectionnez le groupe de tests à examiner. 

    Par défaut, les données de surveillance sont classées par sources, destinations et configurations de test (tests). Plus tard, vous pourrez changer l'affichage en remplaçant les groupes de tests par les sources, les destinations ou les configurations de test. Choisissez ensuite une entité pour examiner les cinq principaux tests qui ont échoué. Par exemple, optez pour un affichage par sources et destinations pour rechercher les tests pertinents dans le moniteur de connexion sélectionné.
1. Choisissez le test à examiner.

   ![Capture d'écran montrant où sélectionner un test](./media/connection-monitor-2-preview/tg-drill.png)

    Pour votre intervalle de temps et votre pourcentage de vérifications qui ont échoué, vous voyez les valeurs de seuil et les valeurs réelles. Pour la durée des boucles, vous voyez des valeurs correspondant au seuil, à la moyenne, au minimum et au maximum. Vous voyez également les alertes déclenchées pour le test que vous avez sélectionné.
1. Changez l'intervalle de temps pour afficher plus de données.

Pour afficher les tendances relatives à la durée des boucles et le pourcentage de vérifications qui ont échoué pour un test :
1. Sélectionnez la source, la destination et la configuration de test à examiner.

    Pour votre intervalle de temps et pour le pourcentage de vérifications qui ont échoué, vous voyez les valeurs de seuil et les valeurs réelles. Pour la durée des boucles, vous voyez des valeurs correspondant au seuil, à la moyenne, au minimum et au maximum. Vous voyez également les alertes déclenchées pour le test que vous avez sélectionné.

   ![Capture d'écran montrant les métriques d'un test](./media/connection-monitor-2-preview/test-drill.png)

1. Pour afficher la topologie du réseau, sélectionnez **Topologie**.

   ![Capture d'écran illustrant l'onglet Topologie du réseau](./media/connection-monitor-2-preview/test-topo.png)

1. Pour afficher les problèmes identifiés, dans la topologie, sélectionnez un tronçon. (Ces tronçons sont des ressources Azure.) Cette fonctionnalité n'est actuellement pas disponible pour les réseaux locaux.

   ![Capture d'écran illustrant un lien de tronçon sélectionné sous l'onglet Topologie](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Consigner les requêtes dans Log Analytics

Utilisez Log Analytics pour créer des vues personnalisées de vos données de supervision. Toutes les données affichées par l'interface utilisateur proviennent de Log Analytics. Vous pouvez analyser les données de manière interactive dans le référentiel. Mettez en corrélation les données provenant d'Agent Health ou d'autres solutions basées sur Log Analytics. Exportez les données vers Excel ou Power BI, ou créez un lien partageable.

#### <a name="metrics-in-azure-monitor"></a>Mesures dans Azure Monitor

Dans les moniteurs de connexion créés avant le lancement de la fonctionnalité Moniteur de connexion (préversion), les quatre mesures suivantes sont disponibles : % Probes Failed, AverageRoundtripMs, ChecksFailedPercent (préversion) et RoundTripTimeMs (préversion). Dans les moniteurs de connexion créés à partir de la fonctionnalité Moniteur de connexion (préversion), les données sont uniquement disponibles pour les métriques portant l'étiquette *(Préversion)* .

![Capture d'écran illustrant les métriques dans le Moniteur de connexion (préversion)](./media/connection-monitor-2-preview/monitor-metrics.png)

Lorsque vous utilisez des métriques, définissez le type de ressource sur Microsoft.Network/networkWatchers/connectionMonitors

| Métrique | Nom complet | Unité | Type d’agrégation | Description | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % de sondes ayant échoué | Pourcentage | Average | Pourcentage de sondes de surveillance de connectivité ayant échoué | Aucune dimension |
| AverageRoundtripMs | Avg. Durée aller-retour (ms) | Millisecondes | Average | Durée moyenne des boucles réseau pour les sondes de surveillance de la connectivité envoyées entre la source et la destination. |             Aucune dimension |
| ChecksFailedPercent (préversion) | Pourcentage d’échecs de vérification (préversion) | Pourcentage | Average | Pourcentage de vérifications ayant échoué pour un test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Région |
| RoundTripTimeMs (préversion) | Durée d’aller-retour (ms) (préversion) | Millisecondes | Average | Durée des boucles pour les vérifications envoyées entre la source et la destination. Cette valeur ne fait pas l'objet d'une moyenne. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Région |

#### <a name="metric-alerts-in-azure-monitor"></a>Alertes de métrique dans Azure Monitor

Pour créer une alerte dans Azure Monitor :

1. Choisissez la ressource de moniteur de connexion que vous avez créée dans le Moniteur de connexion (préversion).
1. Assurez-vous que **Métrique** apparaît comme type de signal pour le moniteur de connexion.
1. Dans **Ajouter une condition**, pour le **Nom du signal**, sélectionnez **ChecksFailedPercent (préversion)** ou **RoundTripTimeMs (préversion)** .
1. Pour le **Type de signal**, choisissez **Métriques**. Par exemple, sélectionnez **ChecksFailedPercent (préversion)** .
1. Toutes les dimensions de la métrique sont répertoriées. Choisissez le nom et la valeur de dimension. Par exemple, sélectionnez **Adresse source**, puis entrez l'adresse IP d'une source dans votre moniteur de connexion.
1. Dans **Logique d'alerte**, renseignez les informations suivantes :
   * **Type de condition** : **Statique**.
   * **Condition** et **Seuil**.
   * **Granularité d'agrégation et fréquence d'évaluation** : le Moniteur de connexion (préversion) met à jour les données toutes les minutes.
1. Dans **Actions**, choisissez votre groupe d'actions.
1. Indiquez les détails de l'alerte.
1. Créez la règle d’alerte

   ![Capture d'écran montrant la zone Créer une règle dans Azure Monitor ; « Adresse source » et « Nom du point de terminaison source » sont en surbrillance](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnostiquer les problèmes de votre réseau

Le Moniteur de connexion (préversion) vous aide à diagnostiquer les problèmes liés à votre moniteur de connexion et à votre réseau. Les problèmes liés à votre réseau hybride sont détectés par les agents Log Analytics que vous avez précédemment installés. Les problèmes liés à Azure sont détectés par l'extension Network Watcher. 

Vous pouvez afficher les problèmes liés au réseau Azure dans la topologie du réseau.

Pour les réseaux dont les sources sont des machines virtuelles locales, les problèmes suivants peuvent être détectés :

* La requête a expiré.
* Point de terminaison non résolu par le DNS : temporaire ou persistant. URL non valide.
* Hôte introuvable.
* Impossibilité pour la source de se connecter à la destination. Cible inaccessible via ICMP.
* Problèmes liés au certificat : 
    * Certificat client requis pour authentifier l'agent. 
    * La liste de réadressage des certificats n'est pas accessible. 
    * Le nom d'hôte du point de terminaison ne correspond pas au sujet ou à l'autre nom du sujet du certificat. 
    * Le certificat racine ne figure pas dans le magasin des Autorités de certification de confiance de l'ordinateur local de la source. 
    * Le certificat SSL est arrivé à expiration, n'est pas valide, a été révoqué ou n'est pas compatible.

Pour les réseaux dont les sources sont des machines virtuelles Azure, les problèmes suivants peuvent être détectés :

* Problèmes liés à l'agent :
    * Agent arrêté.
    * Échec de la résolution DNS.
    * Aucune application ou aucun écouteur n'écoute sur le port de destination.
    * Impossible d'ouvrir le socket.
* Problèmes liés à l'état de la machine virtuelle : 
    * Démarrage en cours
    * En cours d’arrêt
    * Arrêté
    * Libération
    * Libéré
    * Redémarrage
    * Non alloué
* L'entrée de table ARP est manquante.
* Le trafic a été bloqué en raison de problèmes liés au pare-feu local ou aux règles NSG.
* Problèmes liés à la passerelle de réseau virtuel : 
    * Itinéraires manquants.
    * Le tunnel entre deux passerelles est déconnecté ou manquant.
    * Le tunnel n'a pas trouvé la deuxième passerelle.
    * Aucune information de peering n'a été trouvée.
* L'itinéraire était manquant dans Microsoft Edge.
* Le trafic a été interrompu à cause d'itinéraires système ou de règles UDR.
* Le protocole BGP n'est pas activé sur la connexion à la passerelle.
* La sonde DIP est en panne sur l'équilibreur de charge.
