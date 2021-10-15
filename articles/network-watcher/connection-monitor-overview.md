---
title: Moniteur de connexion dans Azure | Microsoft Docs
description: Apprenez à utiliser la fonctionnalité Moniteur de connexion pour surveiller les communications réseau dans un environnement distribué.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 7f7043af634145da0f0edff2d485882bee2fb9fb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275883"
---
# <a name="monitor-network-connectivity-by-using-connection-monitor"></a>Surveiller la connectivité réseau à l’aide du Moniteur de connexion

> [!IMPORTANT]
> Depuis le 1er juillet 2021, vous ne pouvez plus ajouter de tests dans un espace de travail existant ou activer un nouvel espace de travail dans Network Performance Monitor (NPM). Vous ne pouvez pas non plus ajouter de nouveaux moniteurs de connexion dans Moniteur de connexion (classique). Vous pouvez continuer d’utiliser les tests et moniteurs de connexion créés avant le 1er juillet 2021. 
> 
> Pour réduire l’interruption de service de vos charges de travail actuelles, [migrez vos tests de Network Performance Monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou [effectuez une migration à partir du Moniteur de connexion (classique)](migrate-to-connection-monitor-from-connection-monitor-classic.md) vers le nouveau Moniteur de connexion dans Azure Network Watcher avant le 29 février 2024.

La fonctionnalité Moniteur de connexion permet une vérification unifiée et de bout en bout de la connectivité dans Azure Network Watcher. Elle prend en charge les déploiements hybrides et cloud Azure. Network Watcher fournit des outils pour surveiller, diagnostiquer et consulter les métriques de connectivité de vos déploiements Azure.

Voici quelques cas d’usage de Moniteur de connexion :

- Votre machine virtuelle de serveur web front-end communique avec une machine virtuelle de serveur de base de données dans une application multiniveau. Vous souhaitez vérifier la connectivité réseau entre les deux machines virtuelles.
- Vous souhaitez par exemple que les machines virtuelles de la région USA Est puissent effectuer un test Ping ciblant les machines virtuelles de la région USA Centre, et vous souhaitez comparer les temps de réponse du réseau entre les régions.
- Vous disposez de plusieurs sites locaux : un à Seattle (Washington) par exemple et l’autre à Ashburn (Virginie). Vos sites de bureau se connectent à des URL Microsoft 365. Pour vos utilisateurs d’URL Microsoft 365, vous voulez comparer les latences entre Seattle et Ashburn.
- Votre application hybride doit être connectée à un point de terminaison de compte de stockage Azure. Votre site local et votre application Azure se connectent au même point de terminaison. Vous souhaitez comparer les temps de réponse du site local avec ceux de l'application Azure.
- Vous souhaitez vérifier la connectivité entre vos installations locales et les machines virtuelles Azure qui hébergent votre application cloud.

Moniteur de connexion allie le meilleur des deux fonctionnalités suivantes : la fonctionnalité [Moniteur de connexion (Classique)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) de Network Watcher et les fonctionnalités [Moniteur de connectivité de service](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [Surveillance ExpressRoute](../expressroute/how-to-npm.md) et [Analyse des performances](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) de Network Performance Monitor (NPM).

Moniteur de connexion présente notamment les avantages suivants :

* Expérience unifiée et intuitive pour les besoins de supervision des clouds Azure et hybride
* Surveillance de la connectivité entre les régions et entre les espaces de travail
* Fréquences de sondage supérieures et meilleure visibilité des performances du réseau
* Alertes plus rapides pour vos déploiements hybrides
* Prise en charge des vérifications de connectivité basées sur les protocoles HTTP, TCP (Transmission Control Protocol) et ICMP (Internet Control Message Protocol) 
* Prise en charge des métriques et de Log Analytics pour les initialisations (tearDown) de test Azure et non Azure

![Diagramme montrant comment le Moniteur de connexion interagit avec les machines virtuelles Azure, les hôtes non Azure, les points de terminaison et les emplacements de stockage de données.](./media/connection-monitor-2-preview/hero-graphic.png)

Pour commencer à utiliser Moniteur de connexion à des fins de surveillance, procédez comme suit : 

1. [Installez des agents de surveillance.](#install-monitoring-agents)
1. [Activez Network Watcher sur votre abonnement.](#enable-network-watcher-on-your-subscription)
1. [Créez un moniteur de connexion.](#create-a-connection-monitor)
1. [Analysez les données de surveillance et définissez des alertes.](#analyze-monitoring-data-and-set-alerts)
1. [Diagnostiquez les problèmes liés à votre réseau](#diagnose-issues-in-your-network).

Pour plus d'informations, lisez les sections suivantes.

## <a name="install-monitoring-agents"></a>Installer des agents de supervision

Le Moniteur de connexion s'appuie sur des exécutables légers pour effectuer les vérifications de la connectivité. Il prend en charge les vérifications de la connectivité à partir des environnements Azure et des environnements locaux. L'exécutable à utiliser varie selon que votre machine virtuelle est hébergée sur Azure ou en local.

### <a name="agents-for-azure-virtual-machines"></a>Agents pour les machines virtuelles Azure

Pour que le Moniteur de connexion reconnaisse vos machines virtuelles Azure comme sources de surveillance, installez l'extension de machine virtuelle Network Watcher Agent sur celles-ci. Cette extension est également connue sous le nom d'*extension Network Watcher*. Les machines virtuelles Azure ont besoin de cette extension pour déclencher une surveillance de bout en bout et d'autres fonctionnalités avancées. 

Vous pouvez installer l'extension Network Watcher au moment de la [création d'une machine virtuelle](./connection-monitor.md#create-the-first-vm). Vous pouvez également procéder à l'installation, à la configuration et à la résolution des problèmes liés à l'extension Network Watcher séparément pour [Linux](../virtual-machines/extensions/network-watcher-linux.md) et [Windows](../virtual-machines/extensions/network-watcher-windows.md).

Les règles d'un groupe de sécurité réseau (NSG) ou d'un pare-feu peuvent empêcher la communication entre la source et la destination. Le Moniteur de connexion détecte ce problème et le signale sous la forme d'un message de diagnostic dans la topologie. Pour activer la vérification de la connectivité, vérifiez que les règles NSG et les règles de pare-feu autorisent les paquets sur TCP ou ICMP entre la source et la destination.

### <a name="agents-for-on-premises-machines"></a>Agents pour machines locales

Pour que le Moniteur de connexion reconnaisse vos machines locales en tant que sources de surveillance, installez l'agent Log Analytics sur les machines. Puis activez la solution [Network Performance Monitor](/azure/azure-monitor/insights/network-performance-monitor#configure-the-solution). Ces agents sont liés aux espaces de travail Log Analytics. Par conséquent, vous devez configurer l'ID de l'espace de travail et la clé primaire pour permettre aux agents d'entamer la surveillance.

Pour installer l’agent Log Analytics pour des machines Windows, consultez [Installer l’agent Log Analytics sur Windows](../azure-monitor/agents/agent-windows.md).

Si le chemin inclut des pare-feu ou des appliances virtuelles réseau (NVA), assurez-vous que la destination est accessible.

Pour ouvrir le port :

* Pour les ordinateurs Windows, exécutez le script PowerShell [EnableRules.ps1](https://aka.ms/npmpowershellscript) sans paramètre dans une fenêtre PowerShell avec des privilèges Administrateur.

* Pour les machines Linux, modifiez la valeur de numéro de port manuellement. Pour cela, procédez de la façon suivante :
   1. Accédez à */var/opt/microsoft/omsagent/npm_state*. 
   1. Ouvrez le fichier *npmdregistry*.
   1. Modifiez la valeur de numéro de port : ```“PortNumber:<port of your choice>”```.

   > [!NOTE]
   > Les numéros de port que vous utilisez doivent être identiques pour tous les agents utilisés dans un espace de travail. 

Le script crée les clés de Registre requises par la solution. Il crée également des règles de pare-feu Windows pour autoriser les agents à créer des connexions TCP entre eux. Les clés de Registre créées par le script spécifient également s’il faut enregistrer les journaux d’activité de débogage et le chemin d’accès des fichiers journaux. Le script définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés sont définies automatiquement par le script. Ne modifiez pas manuellement ces clés. Par défaut, le port ouvert est 8084. Vous pouvez utiliser un port personnalisé en ajoutant le paramètre portNumber au script. Utilisez le même port sur tous les ordinateurs exécutant le script. 

Pour plus d’informations, consultez la section « Configuration réseau requise » de la page [Vue d’ensemble de l’agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md#network-requirements).

Le script configure uniquement le pare-feu Windows en local. Si vous avez un pare-feu réseau, assurez-vous qu’il autorise le trafic destiné au port TCP que Network Performance Monitor utilise.

L’agent Windows Log Analytics peut être multirésident afin d’envoyer des données à plusieurs espaces de travail et groupes d’administration System Center Operations Manager. L’agent Linux ne peut envoyer des données qu’à une seule destination, qu’il s’agisse d’un espace de travail ou d’un groupe d’administration.

#### <a name="enable-the-npm-solution-for-on-premises-machines"></a>Activer la solution NPM pour les ordinateurs locaux 

Pour activer la solution NPM pour les ordinateurs locaux, procédez comme suit : 

1. Dans le portail Azure, accédez à **Network Watcher**.
1. Sur la gauche, sous **Surveillance**, sélectionnez **Network Performance Monitor**. 

   Une liste d’espaces de travail avec la solution NPM activée s’affiche, filtrée par **Abonnements**. 
1. Pour ajouter la solution NPM dans un nouvel espace de travail, sélectionnez **Ajouter NPM** en haut à gauche. 
1. Sélectionnez l’abonnement et l’espace de travail dans lesquels vous souhaitez activer la solution, puis sélectionnez **Créer**.
   
   Une fois que vous avez activé la solution, l’espace de travail prend quelques minutes pour s’afficher.

   :::image type="content" source="./media/connection-monitor/network-performance-monitor-solution-enable.png" alt-text="Capture d’écran montrant comment ajouter la solution NPM dans le Moniteur de connexion" lightbox="./media/connection-monitor/network-performance-monitor-solution-enable.png":::

Contrairement aux agents Log Analytics, la solution NPM peut être configurée pour envoyer des données uniquement à un seul espace de travail Log Analytics.

## <a name="enable-network-watcher-on-your-subscription"></a>Activer Network Watcher dans votre abonnement

Tous les abonnements disposant d'un réseau virtuel sont activés avec Network Watcher. Lorsque vous créez un réseau virtuel sur votre abonnement, Network Watcher est automatiquement activé dans la région et sur l'abonnement correspondant à ce réseau virtuel. Cette activation automatique n'affecte pas vos ressources et n'entraîne pas de frais. Vérifiez que Network Watcher n’est pas explicitement désactivé dans votre abonnement. 

Assurez-vous que Network Watcher est [disponible pour votre région](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher&regions=all). Pour plus d’informations, consultez [Activer Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion 

Le Moniteur de connexion surveille la communication à intervalles réguliers. Il vous informe des changements en matière d'accessibilité et de latence. Vous pouvez également vérifier la topologie actuelle et historique du réseau entre les agents sources et les points de terminaison de destination.

Les sources peuvent être des machines virtuelles Azure ou des machines locales sur lesquelles un agent de surveillance est installé. Les points de terminaison de destination peuvent être des URL Microsoft 365, des URL Dynamics 365, des URL personnalisées, des ID de ressources de machine virtuelle Azure, des adresses IPv4 ou IPv6, un FQDN ou un nom de domaine.

### <a name="access-connection-monitor"></a>Accéder à Moniteur de connexion

1. Dans le portail Azure, accédez à **Network Watcher**.
1. Dans le volet gauche, sous **Surveillance**, sélectionnez **Moniteur de connexion**.

   Tous les moniteurs de connexion créés dans Moniteur de connexion sont répertoriés. Pour afficher les moniteurs de connexion créés dans l'expérience utilisateur classique du Moniteur de connexion, sélectionnez l’onglet **Moniteur de connexion**.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Capture d’écran illustrant des moniteurs de connexion créés dans Moniteur de connexion" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion

Dans les moniteurs de connexion que vous créez à l’aide de la fonctionnalité Moniteur de connexion, vous pouvez ajouter des machines locales et des machines virtuelles Azure en tant que sources. Ces moniteurs de connexion peuvent également surveiller la connectivité aux points de terminaison. Les points de terminaison peuvent se trouver sur Azure ou sur toute autre URL ou adresse IP.

Le Moniteur de connexion inclut les entités suivantes :

* **Ressource de moniteur de connexion** : Ressource Azure spécifique à une région. Toutes les entités ci-dessous sont des propriétés d’une ressource de moniteur de connexion.
* **Point de terminaison** : Source ou destination qui participe aux vérifications de la connectivité. Les machines virtuelles Azure, les agents locaux, les URL et les adresses IP sont des exemples de points de terminaison.
* **Configuration de test** : Configuration spécifique à un protocole dans le cadre d’un test. En fonction du protocole que vous sélectionnez, vous pouvez définir le port, les seuils, la fréquence de test et d’autres propriétés.
* **Groupe de tests** : Groupe contenant les points de terminaison sources, les points de terminaison de destination et les configurations de test. Un moniteur de connexion peut contenir plusieurs groupes de tests.
* **Test** : Combinaison d’un point de terminaison source, d’un point de terminaison de destination et d’une configuration de test. Un test correspond au niveau le plus granulaire auquel les données de surveillance sont disponibles. Les données de surveillance incluent le pourcentage de vérifications qui ont échoué et la durée des boucles.

 ![Diagramme illustrant un moniteur de connexion, avec définition de la relation entre les groupes de tests et les tests.](./media/connection-monitor-2-preview/cm-tg-2.png)

Vous pouvez créer un moniteur de connexion à l’aide du [portail Azure](./connection-monitor-create-using-portal.md), d’[ARMClient](./connection-monitor-create-using-template.md) ou d’[Azure PowerShell](connection-monitor-create-using-powershell.md).

Toutes les sources, destinations et configurations de test que vous ajoutez à un groupe de tests sont réparties en tests individuels. Voici un exemple d’organisation des sources et des destinations :

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
| | |

### <a name="scale-limits"></a>Limites de mise à l’échelle

Les moniteurs de connexion présentent les limites suivantes en termes de mise à l'échelle :

* Nombre maximum de moniteurs de connexion par abonnement et par région : 100
* Nombre maximum de groupes de tests par moniteur de connexion : 20
* Nombre maximum de sources et de destinations par moniteur de connexion : 100
* Nombre maximum de configurations de test par moniteur de connexion :

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analyser les données de surveillance et définir des alertes

Après la création d'un moniteur de connexion, les sources vérifient la connectivité aux destinations en fonction de votre configuration de test.

### <a name="checks-in-a-test"></a>Vérifications dans un test

En fonction du protocole que vous avez sélectionné dans la configuration de test, Moniteur de connexion exécute une série de vérifications pour la paire source-destination. Les vérifications sont exécutées en fonction de la fréquence de test que vous avez sélectionnée.

Si vous utilisez HTTP, le service calcule le nombre de réponses HTTP qui ont renvoyé un code de réponse valide. Vous pouvez définir des codes de réponse valides à l’aide de PowerShell et d’Azure CLI. Le résultat détermine le pourcentage de vérifications qui ont échoué. Pour calculer la durée des boucles, le service mesure le délai entre un appel HTTP et la réponse.

Si vous utilisez TCP ou ICMP, le service calcule le pourcentage de perte de paquets pour déterminer le pourcentage de vérifications qui ont échoué. Pour calculer la durée des boucles, le service mesure le temps nécessaire à la réception de l'accusé de réception (ACK) pour les paquets qui ont été envoyés. Si vous avez activé les données traceroute pour vos tests réseau, vous pouvez voir les pertes et la latence tronçon par tronçon de votre réseau local.

### <a name="states-of-a-test"></a>États d’un test

Sur la base des données renvoyées par les vérifications, les tests peuvent présenter les états suivants :

* **Réussite** : les valeurs réelles du pourcentage de vérifications qui ont échoué et de la durée des boucles se situent dans les seuils spécifiés.
* **Échec** : les valeurs réelles du pourcentage de vérifications qui ont échoué et de la durée des boucles ont dépassé les seuils spécifiés. Si aucun seuil n’est spécifié, un test atteint l’état *Échec* lorsque le pourcentage de vérifications qui ont échoué est de 100.
* **Avertissement** : 
     * Si un seuil est spécifié et que Moniteur de connexion constate un pourcentage d’échec de vérification supérieur à 80 % du seuil, le test est marqué en tant qu’*avertissement*.
     * En l’absence de seuils spécifiés, Moniteur de connexion attribue automatiquement un seuil. Lorsque ce seuil est dépassé, l'état du test passe à *Avertissement*.Pour la durée de l’aller-retour dans les tests TCP ou ICMP, le seuil est de 750 millisecondes (ms). Pour le pourcentage d’échec de vérification, le seuil est de 10 pour cent. 
* **Indéterminé** : aucune donnée dans l’espace de travail Log Analytics.Vérifiez les métriques. 
* **Pas en cours d’exécution** : désactivé par désactivation du groupe de tests.  

### <a name="data-collection-analysis-and-alerts"></a>Collecte de données, analyse et alertes

Les données collectées par Moniteur de connexion sont stockées dans l’espace de travail Log Analytics. Vous avez configuré cet espace de travail lors de la création du Moniteur de connexion. 

Les données de supervision sont également disponibles dans les métriques d’Azure Monitor. Vous pouvez utiliser Log Analytics pour conserver vos données de surveillance aussi longtemps que vous le souhaitez. Par défaut, Azure Monitor ne stocke les données que pendant 30 jours. 

Vous pouvez [définir des alertes basées sur des métriques pour les données](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Tableaux de bord de surveillance

Les tableaux de bord de surveillance présentent la liste des moniteurs de connexion auxquels vous pouvez accéder pour vos abonnements, régions, horodatages, sources et types de destination.

Lorsque vous accédez à Moniteur de connexion à partir de Network Watcher, vous pouvez afficher les données par :

* **Moniteur de connexion** : liste de tous les moniteurs de connexion créés pour vos abonnements, régions, horodatages, sources et types de destination. Il s'agit de la vue par défaut.
* **Groupes de tests** : liste de tous les groupes de tests créés pour vos abonnements, régions, horodatages, sources et types de destination. Ces groupes de tests ne sont pas filtrés par moniteurs de connexion.
* **Test** : liste de tous les tests qui s'exécutent pour vos abonnements, régions, horodatages, sources et types de destination. Ces tests ne sont pas filtrés par moniteurs de connexion ou groupes de tests.

Sur l'illustration suivante, les trois vues de données sont signalées par la flèche 1.

Sur le tableau de bord, vous pouvez développer chaque moniteur de connexion pour afficher ses groupes de tests. Vous pouvez ensuite développer chaque groupe de tests pour afficher les tests qui y sont exécutés. 

Vous pouvez filtrer une liste en fonction des critères suivants :

* **Filtres de niveau supérieur** : liste de recherche par texte, type d’entité (Moniteur de connexion, groupe de test ou test), horodateur et étendue. L’étendue comprend les abonnements, les régions, les sources et les types de destinations. Voir la zone 1 dans l’image suivante.
* **Filtres basés sur l'état** : filtres basés sur l'état du moniteur de connexion, du groupe de tests ou du test. Voir la zone 2 dans l'image suivante.
* **Filtre basé sur des alertes** : filtre par alertes déclenchées sur la ressource du moniteur de connexion. Voir la zone 3 dans l’image suivante.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Capture d’écran montrant comment filtrer les vues des moniteurs de connexion, des groupes de tests et des tests dans Moniteur de connexion" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Par exemple, dans Moniteur de connexion, pour afficher tous les tests dont l’adresse IP source correspond à 10.192.64.56, procédez comme suit :
1. Remplacez la vue par **Test**.
1. Dans la zone **Rechercher**, entrez **10.192.64.56**.
1. Sous **Étendue** dans le filtre de niveau supérieur, sélectionnez **Sources**.

Pour n’afficher dans Moniteur de connexion que les tests qui ont échoué et dont l’adresse IP source correspond à 10.192.64.56, procédez comme suit :
1. Remplacez la vue par **Test**.
1. Pour le filtre basé sur l'état, sélectionnez **Échec**.
1. Dans la zone **Rechercher**, entrez **10.192.64.56**.
1. Sous **Étendue** dans le filtre de niveau supérieur, sélectionnez **Sources**.

Pour n’afficher dans Moniteur de connexion que les tests qui ont échoué et dont la destination est outlook.office365.com, procédez comme suit :
1. Remplacez la vue par **Test**.
1. Pour le filtre basé sur l'état, sélectionnez **Échec**.
1. Dans la zone **Rechercher**, entrez **office.live.com**.
1. Sous **Étendue**, dans le filtre de niveau supérieur, sélectionnez **Destinations**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Capture d'écran montrant une vue filtrée pour n'afficher que les tests qui ont échoué et dont la destination est Outlook.Office365.com" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Pour connaître la raison de l’échec d’un moniteur de connexion, d’un groupe de test ou d’un test, cliquez sur la colonne intitulée **Raison**. Cela indique le seuil (pourcentage d’échec de vérification ou RTT) qui a été atteint et affiche les messages de diagnostics associés.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Capture d’écran montrant la raison de l’échec d’un moniteur de connexion, d’un test ou d’un groupe de test" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Pour afficher les tendances relatives à la durée des boucles et le pourcentage de vérifications qui ont échoué pour un moniteur de connexion, procédez comme suit :

1. Sélectionnez le moniteur de connexion à examiner.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Capture d'écran illustrant les métriques d'un moniteur de connexion, affichées par groupe de tests." lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

   Le volet inclut les sections suivantes :  
    * **Essentiels** : propriétés spécifiques de la ressource du Moniteur de connexion sélectionné. 
    * **Résumé**: 
        * Courbes de tendance agrégées pour RTT et pourcentage de vérifications ayant échoué pour tous les tests dans le moniteur de connexion. Vous pouvez définir une heure spécifique pour afficher les détails.
        * Top 5 des groupes de test, des sources et des destinations en fonction du RTT ou du pourcentage de vérifications ayant échoué. 
    * **Onglets** : groupes de test, sources, destinations et configurations de test. Répertorie les groupes de test, les sources ou les destinations dans le moniteur de connexion. Vérifiez les échecs de tests, la valeur RTT agrégée et les valeurs de pourcentage d’échecs de vérification. Vous pouvez également remonter dans le temps pour afficher les données. 
    * **Problèmes** : problèmes au niveau tronçon pour chaque test dans le moniteur de connexion. 

   :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Capture d’écran illustrant les métriques d’un moniteur de connexion, affichées par groupe de tests partie 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. Dans ce volet, voici les opérations que vous pouvez effectuer :

    * Cliquez sur **Afficher tous les tests** pour afficher tous les tests dans le Moniteur de connexion.

      * Sélectionnez des tests et comparez-les.
    
        :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Capture d’écran montrant la comparaison de 2 tests" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
      * Utilisez des clusters pour étendre des ressources composées telles que des réseaux virtuels et des sous-réseaux à ses ressources enfants.

      * Affichez la topologie des tests en sélectionnant la topologie.    

    * Sélectionnez **Afficher tous les groupes de test**, **Afficher les configurations de test**, **Afficher les sources** **Afficher les destinations** pour afficher les détails spécifiques à chacun. 

    * Sélectionnez un groupe de test, une configuration de test, une source ou une destination pour afficher tous les tests de l’entité.

Pour afficher les tendances relatives à la durée des boucles et le pourcentage de vérifications qui ont échoué pour un groupe de tests, procédez comme suit :

* Sélectionnez le groupe de tests à examiner. 

   Vous pouvez les afficher et naviguer entre eux comme vous le feriez dans le moniteur de connexion : Essentials, résumé, table pour les groupes de test, sources, destinations et configurations de test. 

Pour afficher les tendances relatives à la durée des boucles et le pourcentage de vérifications qui ont échoué pour un test, procédez comme suit :

1. Choisissez le test à examiner. Vous pouvez afficher la topologie de réseau et les graphiques de tendance de bout en bout pour le pourcentage de vérifications ayant échoué et la durée aller-retour. 

1. Pour afficher les problèmes identifiés, dans la topologie, sélectionnez un tronçon. Les tronçons sont des ressources Azure. 

   > [!NOTE]
   > Cette fonctionnalité n'est actuellement pas disponible pour les réseaux locaux.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Capture d’écran montrant l’affichage de la topologie d’un test" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Consigner les requêtes dans Log Analytics

Utilisez Log Analytics pour créer des vues personnalisées de vos données de supervision. Toutes les données affichées sont issues de Log Analytics. Vous pouvez analyser les données de manière interactive dans le référentiel. Mettez en corrélation les données provenant d'Agent Health ou d'autres solutions basées sur Log Analytics. Exportez les données vers Excel ou Power BI, ou créez un lien partageable.

#### <a name="network-topology-in-connection-monitor"></a>Topologie du réseau dans le Moniteur de connexion 

En général, vous créez la topologie du Moniteur de connexion en utilisant le résultat d’une commande traceroute effectuée par l’agent. La commande traceroute récupère fondamentalement tous les tronçons de la source à la destination.

Toutefois, dans les instances où la source ou la destination se trouve dans les limites Azure, vous générez la topologie en fusionnant les résultats de deux opérations distinctes.
La première opération est le résultat de la commande traceroute. La deuxième opération est le résultat d’une commande interne qui identifie une route logique en fonction de la configuration réseau (du client) au sein des limites d’Azure. Cette commande interne est similaire à l’outil de diagnostic de tronçon suivant Network Watcher.  

Étant donné que la deuxième opération est logique et que la première opération n’identifie généralement aucun tronçon dans les limites Azure, quelques tronçons du résultat fusionné (principalement ceux qui se trouvent dans les limites Azure) n’affichent pas les valeurs de latence.

#### <a name="metrics-in-azure-monitor"></a>Mesures dans Azure Monitor

Dans les moniteurs de connexion créés avant le lancement de la fonctionnalité Moniteur de connexion, les quatre métriques suivantes sont disponibles : % Probes Failed, AverageRoundtripMs, ChecksFailedPercent et RoundTripTimeMs. 

Dans les moniteurs de connexion créés à partir de la fonctionnalité Moniteur de connexion, seules les données des métriques ChecksFailedPercent, RoundTripTimeMs et Test Result sont disponibles.

Les métriques sont générées en fonction de la fréquence de surveillance et décrivent l’aspect d’un moniteur de connexion à un moment donné. Les métriques du Moniteur de connexion ont également plusieurs dimensions, telles que SourceName, DestinationName, TestConfiguration et TestGroup. Vous pouvez utiliser ces dimensions pour visualiser des données spécifiques et les cibler lors de la définition d’alertes.

Les métriques Azure autorisent actuellement une granularité minimale de 1 minute. Si la fréquence est inférieure à 1 minute, des résultats agrégés s’affichent.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Capture d’écran illustrant les métriques dans Moniteur de connexion" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Lorsque vous utilisez des métriques, définissez le type de ressource sur **Microsoft.Network/networkWatchers/connectionMonitors**.

| Métrique | Nom complet | Unité | Type d’agrégation | Description | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (classique) | % de sondes ayant échoué (classique) | Pourcentage | Average | Pourcentage de sondes de surveillance de connectivité ayant échoué<br>Cette métrique est uniquement disponible pour le moniteur de connexion (classique).  | Aucune dimension |
| AverageRoundtripMs (classique) | Avg. Durée d’aller-retour (ms) (classique) | Millisecondes | Average | Durée moyenne des boucles réseau pour les sondes de surveillance de la connectivité envoyées entre la source et la destination.<br>Cette métrique est uniquement disponible pour le moniteur de connexion (classique). |             Aucune dimension |
| ChecksFailedPercent | % de vérifications ayant échoué | Pourcentage | Average | Pourcentage de vérifications ayant échoué pour un test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Région <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| RoundTripTimeMs | Durée d’aller-retour (ms) | Millisecondes | Average | Durée des boucles pour les vérifications envoyées entre la source et la destination. Cette valeur ne fait pas l'objet d'une moyenne. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Région <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| TestResult | Résultat de test | Count | Average | Résultats du test du moniteur de connexion. <br>L'interprétation des valeurs de résultat est la suivante : <br>0-&nbsp;Indéterminé <br>1 - Réussite <br>2 - Avertissement <br>3 - Échec| SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| | |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Alertes basées sur des métriques pour le Moniteur de connexion

Vous pouvez créer des alertes de métriques sur des moniteurs de connexion à l’aide des méthodes ci-dessous : 

* Dans le Moniteur de connexion, créez des alertes de métriques lors de la création de moniteurs de connexion à l’aide du [portail Azure](connection-monitor-preview-create-using-portal.md#). 
* Dans Moniteur de connexion, créez des alertes de métrique à l’aide de **Configurer des alertes** dans le tableau de bord. 
* À partir d’Azure Monitor, créez des alertes de métriques en procédant comme suit : 

    1. Sélectionnez la ressource de moniteur de connexion que vous avez créée dans Moniteur de connexion.
    1. Assurez-vous que **Métrique** est sélectionné comme type de signal pour le moniteur de connexion.
    1. Dans **Ajouter une condition**, pour le **Nom du signal**, sélectionnez **ChecksFailedPercent** ou **RoundTripTimeMs**.
    1. Pour **Type de signal**, sélectionnez **Métriques**. Par exemple, sélectionnez **ChecksFailedPercent**.
    1. Toutes les dimensions de la métrique sont répertoriées. Sélectionnez le nom et la valeur de dimension. Par exemple, sélectionnez **Adresse source**, puis entrez l’adresse IP d’une source dans votre moniteur de connexion.
    1. Dans **Logique d’alerte**, entrez les valeurs suivantes :
        * **Type de condition** : **Statique**.
        * **Condition** et **Seuil**.
        * **Granularité d'agrégation et fréquence d'évaluation** : Moniteur de connexion met à jour les données toutes les minutes.
    1. Dans **Actions**, sélectionnez votre groupe d’actions.
    1. Indiquez les détails de l'alerte.
    1. Créez la règle d’alerte

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Capture d’écran montrant la zone Créer une règle dans Azure Monitor, avec « Adresse source » et « Nom du point de terminaison source » en surbrillance." lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnostiquer les problèmes de votre réseau

Moniteur de connexion vous aide à diagnostiquer les problèmes liés à votre moniteur de connexion et à votre réseau. Les problèmes liés à votre réseau hybride sont détectés par les agents Log Analytics que vous avez précédemment installés. Les problèmes liés à Azure sont détectés par l'extension Network Watcher. 

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

  > [!NOTE]
  > S’il y a 2 passerelles connectées et que l’une d’entre elles n’est pas dans la même région que le point de terminaison source, le Moniteur de connexion l’identifie comme « aucun itinéraire appris » pour la vue topologique. La connectivité n’est pas affectée. Il s’agit d’un problème connu et nous sommes en train de le résoudre. 

  * L’itinéraire était manquant dans Microsoft Edge.
  * Le trafic s’est arrêté en raison d’itinéraires système ou d’un itinéraire défini par l’utilisateur (UDR).
  * Border Gateway Protocol (BGP) n’est pas activé sur la connexion de passerelle.
  * La sonde d’adresse IP dynamique (DIP) est inactive au niveau de l’équilibreur de charge.

## <a name="compare-azure-connectivity-monitoring-support-types"></a>Comparer les types de prise en charge de la surveillance de la connectivité Azure 

En un clic et sans temps d’arrêt, vous pouvez migrer des tests depuis Network Performance Monitor et Moniteur de connexion (classique) vers la dernière fonctionnalité Moniteur de connexion.
 
La migration produit les résultats suivants :

* Les agents et les paramètres de pare-feu fonctionnent tels quels. Aucune modification n’est nécessaire. 

* Les moniteurs de connexion existants seront mappés vers Moniteur de connexion > Groupe de test > Format de test. En sélectionnant **Modifier**, vous pouvez afficher et modifier les propriétés de la dernière fonctionnalité Moniteur de connexion, télécharger un modèle pour apporter des modifications à Moniteur de connexion et le soumettre via Azure Resource Manager. 
* Les machines virtuelles Azure avec l’extension Network Watcher envoient des données à l’espace de travail et aux métriques. Le Moniteur de connexion met les données à disposition par le biais des nouvelles métriques (ChecksFailedPercent et RoundTripTimeMs) à la place des anciennes métriques (ProbesFailedPercent et AverageRoundtripMs). Les anciennes métriques seront migrées vers les nouvelles métriques en tant que ProbesFailedPercent > ChecksFailedPercent et AverageRoundtripMs > RoundTripTimeMs.
* Analyse des données :
   * **Alertes** : Migrées automatiquement vers les nouvelles métriques.
   * **Tableaux de bord et intégrations** : Nécessite la modification manuelle de l’ensemble de métriques. 
   
Il existe plusieurs raisons de migrer de Network Performance Monitor et du Moniteur de connexion (classique) vers le Moniteur de connexion. Le tableau suivant répertorie quelques cas d’usage qui montrent comment le Moniteur de connexion le plus récent s’exécute sur Network Performance Monitor et le Moniteur de connexion (classique). 

 | Fonctionnalité  | Network Performance Monitor | Moniteur de connexion (classique) | Moniteur de connexion |
 | -------  | --------------------------- | -------------------------- | ------------------ | 
 | Expérience unifiée pour la supervision Azure et hybride | Non disponible | Non disponible | Disponible |
 | Surveillance interabonnements, interrégions, interespaces de travail | Autorise la supervision interabonnements et interrégions, mais n’autorise pas la supervision interespaces de travail |   Non disponible | Autorise la supervision interabonnements et interespaces de travail ; la supervision interabonnements a une limite régionale  |
 | Prise en charge centralisée des espaces de travail |  Non disponible | Non disponible   | Disponible |
 | Plusieurs sources peuvent effectuer un test ping sur plusieurs destinations | L’analyse des performances permet à plusieurs sources d’effectuer un test ping sur plusieurs destinations. L’analyse de la connectivité du service permet à plusieurs sources d’effectuer un test ping sur un service ou une URL unique. Express Route permet à plusieurs sources d’effectuer un test ping sur plusieurs destinations. | Non disponible | Disponible |
 | Topologie unifiée entre un environnement local, des tronçons Internet et Azure | Non disponible | Non disponible    | Disponible |
 | Vérifications des codes d’état HTTP | Non disponible  | Non disponible | Disponible |
 | Diagnostics de connectivité | Non disponible | Disponible | Disponible |
 | Ressources composées : réseaux virtuels, sous-réseaux et réseaux personnalisés locaux | L’analyse des performances prend en charge les sous-réseaux, les réseaux locaux et les groupes de réseaux logiques. La surveillance de la connectivité du service et Express Route prennent uniquement en charge la surveillance locale et entre les espaces de travail. | Non disponible | Disponible |
 | Métriques de connectivité et mesures des dimensions |   Non disponible | Perte, latence et RTT. | Disponible |
 | Automation – PowerShell, Azure CLI, Terraform | Non disponible | Disponible | Disponible |
 | Pris en charge de Linux | L’analyse des performances prend en charge Linux. Le Moniteur de connectivité de service et Express Route ne prennent pas en charge Linux. | Disponible | Disponible |
 | Prise en charge du cloud public, Government, Mooncake et « air-gapped » | Disponible | Disponible | Disponible|
 | | |


## <a name="faq"></a>Forum aux questions

### <a name="does-connection-monitor-support-classic-vms"></a>Le Moniteur de connexion prend-il en charge les machines virtuelles classiques ?
Non, le Moniteur de connexion ne prend pas en charge les machines virtuelles classiques. Nous vous recommandons de migrer les ressources IaaS (infrastructure as a service) de l’offre Classique vers Azure Resource Manager, car les ressources classiques [vont être dépréciées](../virtual-machines/classic-vm-deprecation.md). Pour plus d'informations, consultez [Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).

### <a name="what-if-my-topology-isnt-decorated-or-my-hops-have-missing-information"></a>Que se passe-t-il si ma topologie n’est pas décorée ou si mes tronçons présentent des informations manquantes ?
La topologie peut être décorée de Non-Azure en Azure uniquement si la ressource Azure de destination et la ressource du Moniteur de connexion se trouvent dans la même région. 

### <a name="what-happens-if-the-connection-monitor-creation-fails-with-the-following-error-we-dont-allow-creating-different-endpoints-for-the-same-vm"></a>Que se passe-t-il si la création du Moniteur de connexion échoue avec l’erreur suivante : « Nous n’autorisons pas la création de différents points de terminaison pour la même machine virtuelle » ?
La même machine virtuelle Azure ne peut pas être utilisée avec différentes configurations dans le même moniteur de connexion. Par exemple, l’utilisation de la même machine virtuelle avec un filtre et sans filtre dans le même moniteur de connexion n’est pas prise en charge.

### <a name="what-happens-if-the-test-failure-reason-is-nothing-to-display"></a>Que se passe-t-il si le motif de l’échec du test est « Rien à afficher » ?
Les problèmes affichés sur le tableau de bord du Moniteur de connexion sont détectés lors de la découverte de la topologie ou de l'exploration des tronçons. Il peut arriver que le seuil fixé pour le % de perte ou le RTT soit dépassé, mais qu'aucun problème ne soit détecté au niveau des tronçons.

### <a name="when-the-existing-connection-monitor-classic-is-migrated-to-the-latest-connection-monitor-what-happens-if-the-external-endpoint-tests-are-migrated-with-the-tcp-protocol-only"></a>Lorsque le Moniteur de connexion existant (Classic) est migré vers le Moniteur de connexion le plus récent, que se passe-t-il si les tests de point de terminaison externes sont migrés avec le protocole TCP uniquement ? 
Il n’y a pas d’option de sélection du protocole dans le Moniteur de connexion (classique). Par conséquent, une connexion à des points de terminaison externes ne peut pas être spécifiée à l’aide du protocole HTTP dans le Moniteur de connexion (classique). Tous les tests utilisent uniquement le protocole TCP dans le Moniteur de connexion (classique) et c’est pourquoi lors de la migration, nous créons une configuration TCP dans les tests du Moniteur de connexion. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer un moniteur de connexion à l’aide de :
    
   * [Le portail Azure](./connection-monitor-create-using-portal.md)  
   * [ARMClient](./connection-monitor-create-using-template.md)
