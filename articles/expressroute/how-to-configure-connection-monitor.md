---
title: Configurer le Moniteur de connexion pour Azure ExpressRoute
description: Configurez la supervision de la connectivité réseau basée sur le cloud pour les circuits Azure ExpressRoute. Ce document traite de la surveillance via le peering privé ExpressRoute et le peering Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/28/2021
ms.author: duau
ms.openlocfilehash: 794e841d8d50683374c86301ec55bc4f6ab53ec6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532442"
---
# <a name="configure-connection-monitor-for-expressroute"></a>Configurer le Moniteur de connexion pour ExpressRoute

Cet article vous permet de configurer une extension Moniteur de connexion pour superviser ExpressRoute. Le Moniteur de connexion est une solution de supervision réseau basée sur le cloud qui supervise la connectivité entre les déploiements cloud Azure et les emplacements locaux (succursales, etc.). Le Moniteur de connexion fait partie des journaux Azure Monitor.  L’extension vous permet également de superviser la connectivité réseau pour vos connexions de peering privées et Microsoft. Quand vous configurez le Moniteur de connexion pour ExpressRoute, vous pouvez détecter les problèmes de réseau à identifier et à éliminer.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Avec le Moniteur de connexion pour ExpressRoute, vous pouvez :

* Superviser les pertes et la latence entre différents réseaux virtuels et définir des alertes.

* Surveiller tous les chemins (notamment les chemins redondants) sur le réseau.

* Résoudre les problèmes réseau temporaires et ponctuels qui sont difficiles à répliquer.

* Déterminer plus facilement un segment spécifique sur le réseau qui est responsable de la dégradation des performances.

## <a name="workflow"></a>flux de travail <a name="workflow"></a>

Des agents de surveillance sont installés sur plusieurs serveurs, en local et sur Azure. Les agents communiquent entre eux en envoyant des paquets d’établissement de liaison TCP. La communication entre les agents permet à Azure de mapper la topologie réseau et le chemin d’accès que le trafic peut prendre.

1. Créez un espace de travail Log Analytics.

1. Installez et configurez les agents logiciels (Si vous voulez superviser uniquement par le biais du peering Microsoft, cette étape est inutile.) :

    * Installez des agents de surveillance sur les serveurs locaux et les machines virtuelles Azure (pour le peering privé).
    * Configurez les paramètres sur les serveurs de l’agent de surveillance pour autoriser les agents de surveillance à communiquer. (Ouvrez les ports du pare-feu, etc.)

1. Configurez des règles de groupe de sécurité réseau pour permettre à l’agent de surveillance installé sur des machines virtuelles Azure de communiquer avec des agents de surveillance locaux.

1. Activez Network Watcher sur votre abonnement.

1. Configurez la supervision : créez des moniteurs de connexion avec des groupes de test pour superviser les points de terminaison source et de destination sur votre réseau.

Si vous utilisez déjà Network Performance Monitor (déprécié) ou le Moniteur de connexion pour superviser d’autres objets ou services, et que vous disposez déjà d’un espace de travail Log Analytics dans l’une des régions prises en charge, vous pouvez ignorer les étapes 1 et 2 et commencer votre configuration à l’étape 3.

## <a name="create-a-workspace"></a><a name="configure"></a> Créer un espace de travail

Créez un espace de travail dans l’abonnement où les réseaux virtuels sont liés aux circuits ExpressRoute.

1. Connectez-vous au [portail Azure](https://portal.azure.com). Dans l’abonnement dans lequel les réseaux virtuels sont connectés à votre circuit ExpressRoute, sélectionnez **+ Créer une ressource**. Recherchez *Espace de travail Log Analytics*, puis sélectionnez **Créer**.

   >[!NOTE]
   >Vous pouvez créer un espace de travail ou utiliser un espace de travail existant. Si vous souhaitez utiliser un espace de travail existant, vous devez vérifier qu’il a été migré vers le nouveau langage de requête. [Plus d’informations...](../azure-monitor/logs/log-query-overview.md)
   >

    :::image type="content" source="./media/how-to-configure-connection-monitor/search-log-analytics.png" alt-text="Capture d’écran de la recherche de Log Analytics dans Créer une ressource.":::

1. Créez un espace de travail en entrant ou en sélectionnant les informations suivantes.  

    | Paramètres | Valeur |
    | -------- | ----- |
    | Abonnement | Sélectionnez l’abonnement avec le circuit ExpressRoute. |
    | Groupe de ressources | Créez un groupe de ressources ou sélectionnez-en un existant. |
    | Name | Entrez un nom pour identifier cet espace de travail. |
    | Région | Sélectionnez une région dans laquelle l’espace de travail sera créé. |

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-workspace-basic.png" alt-text="Capture d’écran de l’onglet Informations de base de la page Créer un espace de travail Log Analytics.":::

     >[!NOTE]
     >Le circuit ExpressRoute peut être n’importe où dans le monde. Il ne doit pas nécessairement se trouver dans la même région que l’espace de travail.
     >

1. Sélectionnez **Vérifier + créer** pour valider, puis **Créer** pour déployer l’espace de travail. Une fois l’espace de travail déployé, passez à la section suivante pour configurer la solution de supervision.

## <a name="configure-monitoring-solution"></a><a name="npm"></a>Configurer des solutions de supervision

Terminez le script Azure PowerShell ci-dessous en remplaçant les valeurs *$SubscriptionId*, *$location*, *$resourceGroup* et *$workspaceName*. Exécutez ensuite le script pour configurer la solution de supervision.

```azurepowershell-interactive
$subscriptionId = "Subscription ID should come here"
Select-AzSubscription -SubscriptionId $subscriptionId

$location = "Workspace location should come here"
$resourceGroup = "Resource group name should come here"
$workspaceName = "Workspace name should come here"

$solution = @{
    Location          = $location
    Properties        = @{
        workspaceResourceId = "/subscriptions/$($subscriptionId)/resourcegroups/$($resourceGroup)/providers/Microsoft.OperationalInsights/workspaces/$($workspaceName)"
    }
    Plan              = @{
        Name          = "NetworkMonitoring($($workspaceName))" 
        Publisher     = "Microsoft"
        Product       = "OMSGallery/NetworkMonitoring"
        PromotionCode = ""
    }
    ResourceName      = "NetworkMonitoring($($workspaceName))" 
    ResourceType      = "Microsoft.OperationsManagement/solutions" 
    ResourceGroupName = $resourceGroup
}

New-AzureRmResource @solution -Force
```

Une fois que vous avez configuré la solution de supervision, passez à l’étape suivante de la procédure d’installation et de configuration des agents de supervision sur vos serveurs.

## <a name="install-and-configure-agents-on-premises"></a><a name="agents"></a>Installer et configurer des agents localement

### <a name="download-the-agent-setup-file"></a><a name="download"></a>Télécharger le fichier de configuration de l’agent

1. Accédez à l’**espace de travail Log Analytics**, puis sélectionnez **Gestion des agents** sous *Paramètres*. Téléchargez l’agent qui correspond au système d’exploitation de votre machine.

    :::image type="content" source="./media/how-to-configure-connection-monitor/download-agent.png" alt-text="Capture d’écran de la page Gestion des agents dans l’espace de travail.":::

1. Ensuite, copiez et collez **l’ID d’espace de travail** et la **clé primaire** dans le bloc-notes.

    :::image type="content" source="./media/how-to-configure-connection-monitor/copy-id-key.png" alt-text="Capture d’écran de l’ID et de la clé primaire de l’espace de travail.":::

1. Pour les machines Windows, téléchargez et exécutez ce script PowerShell [*EnableRules.ps1*](https://aka.ms/npmpowershellscript) dans une fenêtre PowerShell avec des privilèges d’administrateur. Le script PowerShell ouvre le port de pare-feu approprié pour les transactions TCP. 
 
    Pour les machines Linux, le numéro de port doit être modifié manuellement à l’aide des étapes suivantes :

    * Accédez au chemin suivant : /var/opt/microsoft/omsagent/npm_state.
    * Ouvrez le fichier : npmdregistry.
    * Modifiez la valeur du numéro de port `PortNumber:<port of your choice>`.

### <a name="install-log-analytics-agent-on-each-monitoring-server"></a><a name="installagentonprem"></a>Installer l’agent Log Analytics sur chaque serveur de supervision

À des fins de redondance, il est recommandé d’installer l’agent Log Analytics sur au moins deux serveurs des deux côtés de la connexion ExpressRoute. Par exemple, votre réseau virtuel local et votre réseau virtuel Azure. Pour installer les agents, procédez comme suit :

1. Sélectionnez le système d’exploitation approprié ci-dessous pour effectuer les étapes d’installation de l’agent Log Analytics sur vos serveurs.

    * [Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)
    * [Linux](../azure-monitor/agents/agent-linux.md)

1. Lorsque vous avez terminé, Microsoft Monitoring Agent apparaît dans le Panneau de configuration. Vous pouvez passer en revue votre configuration, puis [vérifier la connectivité de l’agent](../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor) aux journaux Azure Monitor.

1. Répétez les étapes 1 et 2 pour les autres machines locales que vous souhaitez utiliser pour la supervision.

### <a name="install-network-watcher-agent-on-each-monitoring-server"></a><a name="installagentazure"></a>Installer l’Agent Network Watcher sur chaque serveur de supervision

#### <a name="new-azure-virtual-machine"></a>Nouvelle machine virtuelle Azure

Si vous créez une machine virtuelle Azure pour la supervision de la connectivité de votre réseau virtuel, vous pouvez installer l’Agent Network Watcher lors de la [création de la machine virtuelle](../network-watcher/connection-monitor.md#create-the-first-vm).

#### <a name="existing-azure-virtual-machine"></a>Machine virtuelle Azure existante

Si vous utilisez une machine virtuelle existante pour la supervision de la connectivité, vous pouvez installer l’Agent réseau séparément pour [Linux](../virtual-machines/extensions/network-watcher-linux.md) et pour [Windows](../virtual-machines/extensions/network-watcher-windows.md).

### <a name="open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>Ouvrir les ports de pare-feu sur les serveurs de l’agent de supervision

Les règles définies pour un pare-feu peuvent bloquer la communication entre les serveurs source et de destination. Le Moniteur de connexion détecte ce problème et l’affiche sous la forme d’un message de diagnostic dans la topologie. Pour activer la vérification de la connectivité, veillez à ce que les règles de pare-feu autorisent les paquets sur TCP ou ICMP entre la source et la destination.

#### <a name="windows"></a>Windows

Pour les machines Windows, vous pouvez exécuter un script PowerShell pour créer les clés de Registre exigées par le Moniteur de connexion. Ce script crée également des règles de pare-feu Windows pour autoriser les agents de surveillance à créer des connexions TCP entre eux. Les clés de Registre créées par le script spécifient également s’il faut enregistrer les journaux d’activité de débogage et le chemin d’accès des fichiers journaux. Le script définit également le port TCP de l’agent utilisé pour la communication. Les valeurs de ces clés sont définies automatiquement par le script. Vous ne devez pas changer manuellement ces clés.

Le port 8084 est ouvert par défaut. Vous pouvez utiliser un port personnalisé en ajoutant le paramètre « portNumber » au script. Toutefois, si vous le faites, vous devez spécifier le même port pour tous les serveurs sur lesquels vous exécutez le script.

>[!NOTE]
>Le script PowerShell « EnableRules » configure des règles de pare-feu Windows uniquement sur le serveur exécutant le script. Si vous avez un pare-feu réseau, vous devez vérifier qu’il autorise le trafic destiné au port TCP que le Moniteur de connexion utilise.
>

Sur les serveurs d’agent, ouvrez une fenêtre PowerShell avec des privilèges administratifs. Exécutez le script PowerShell [EnableRules](https://aka.ms/npmpowershellscript) (que vous avez téléchargé précédemment). N’utilisez pas de paramètres.

:::image type="content" source="./media/how-to-configure-connection-monitor/enable-rules-script.png" alt-text="Capture d’écran de l’exécution du script Activer les règles dans la fenêtre PowerShell.":::

#### <a name="linux"></a>Linux

Pour les machines Linux, les numéros de port utilisés doivent être changés manuellement :

1. Accédez au chemin suivant : /var/opt/microsoft/omsagent/npm_state.
1. Ouvrez le fichier : npmdregistry.
1. Changez la valeur du numéro de port `PortNumber:\<port of your choice\>`. Les numéros de port utilisés doivent être identiques pour tous les agents utilisés dans un espace de travail

## <a name="configure-network-security-group-rules"></a><a name="opennsg"></a>Configurer les règles du groupe de sécurité réseau

Pour superviser des serveurs se trouvant dans Azure, vous devez configurer les règles du groupe de sécurité réseau (NSG) afin d’autoriser le trafic TCP ou ICMP en provenance du Moniteur de connexion. Le port par défaut est **8084, ce qui permet à l’agent de supervision installé sur la machine virtuelle Azure de communiquer avec un agent de supervision local.

Pour plus d’informations sur les groupes de sécurité réseau (NSG), consultez le tutoriel sur le [filtrage du trafic réseau](../virtual-network/tutorial-filter-network-traffic.md).

> [!NOTE]
> Vérifiez que vous avez installé les agents (l’agent de serveur local et l’agent de serveur Azure) et que vous avez exécuté le script PowerShell avant de continuer avec cette étape.
>

## <a name="enable-network-watcher"></a><a name="enablenetworkwatcher"></a>Activer Network Watcher

Tous les abonnements disposant d'un réseau virtuel sont activés avec Network Watcher. Vérifiez que Network Watcher n’est pas explicitement désactivé pour votre abonnement. Pour plus d’informations, consultez [Activer Network Watcher](../network-watcher/network-watcher-create.md).

## <a name="create-a-connection-monitor"></a><a name="createcm"></a> Créer un moniteur de connexion

Pour obtenir une vue d’ensemble globale de la création d’un moniteur de connexion, de tests et de groupes de tests sur des points de terminaison source et de destination dans votre réseau, consultez [Créer un moniteur de connexion](../network-watcher/connection-monitor-create-using-portal.md). Effectuez les étapes suivantes afin de configurer la vérification de la connectivité pour le peering privé et le peering Microsoft.

1. Dans le portail Azure, accédez à votre ressource **Network Watcher**, puis sélectionnez **Moniteur de connexion** sous *Supervision*. Sélectionnez ensuite **Créer** pour créer un moniteur de connexion.

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-connection-monitor.png" alt-text="Capture d’écran du moniteur de connexion dans Network Watcher.":::

1. Sous l’onglet **Informations de base** du workflow de création, sélectionnez la région où vous avez déployé votre espace de travail Log Analytics pour le champ *Région*. Pour *Configuration de l’espace de travail*, sélectionnez l’espace de travail Log Analytics que vous avez créé précédemment. Sélectionnez ensuite **Suivant : Groupes de tests >>** .

    :::image type="content" source="./media/how-to-configure-connection-monitor/connection-monitor-basic.png" alt-text="Capture d’écran de l’onglet Informations de base pour la création du Moniteur de connexion.":::

1. Dans la page Ajouter les détails du groupe de tests, vous allez ajouter les points de terminaison source et de destination de votre groupe de tests. Vous allez également configurer les configurations de test entre elles. Entrez un **Nom** pour ce groupe de tests.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details.png" alt-text="Capture d’écran de la page Ajouter les détails du groupe de tests.":::

1. Sélectionnez **Ajouter une source**, puis accédez à l’onglet **Points de terminaison non-Azure**. Choisissez les ressources locales sur lesquelles l’Agent Log Analytics est installé et dont vous voulez superviser la connectivité, puis sélectionnez **Ajouter des points de terminaison**.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-source-endpoints.png" alt-text="Capture d’écran de l’ajout de points de terminaison sources.":::

1. Sélectionnez ensuite **Ajouter des destinations**. 

    Pour superviser la connectivité sur le **peering privé** ExpressRoute, accédez à l’onglet **Points de terminaison Azure**. Choisissez les ressources Azure sur lesquelles l’agent Network Watcher est installé et dont vous voulez superviser la connectivité à vos réseaux virtuels dans Azure. Veillez à sélectionner l’adresse IP privée de chacune de ces ressources dans la colonne *IP*. Sélectionnez **Ajouter des points de terminaison** pour ajouter ces points de terminaison à votre liste de destinations pour le groupe de tests.
    
    :::image type="content" source="./media/how-to-configure-connection-monitor/add-destination-endpoints.png" alt-text="Capture d’écran de l’ajout de points de terminaison de destination Azure.":::

    Pour superviser la connectivité sur le **peering Microsoft** ExpressRoute, accédez à l’onglet **Adresses externes**. Sélectionnez les points de terminaison de services Microsoft pour lesquels vous souhaitez superviser la connectivité via le peering Microsoft. Sélectionnez **Ajouter des points de terminaison** pour ajouter ces points de terminaison à votre liste de destinations pour le groupe de tests.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-external-destination-endpoints.png" alt-text="Capture d’écran de l’ajout de points de terminaison de destination externes.":::

1. À présent, sélectionnez **Ajouter une configuration de test**. Sélectionnez **TCP** comme protocole, puis entrez le **port de destination** que vous avez ouvert sur vos serveurs. Ensuite, configurez la **fréquence de test** et les **seuils pour les vérifications ayant échoué et la durée d’aller-retour** souhaités. Sélectionnez ensuite **Ajouter une configuration de test**.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-configuration.png" alt-text="Capture d’écran de la page Ajouter une configuration de test.":::

1. Sélectionnez **Ajouter un groupe de tests** une fois que vous avez ajouté vos sources, vos destinations et votre configuration de test.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details-configured.png" alt-text="Capture d’écran de la page Ajouter les détails du groupe de tests configurée." lightbox="./media/how-to-configure-connection-monitor/add-test-group-details-configured-expanded.png":::

1. Sélectionnez **Suivant : Créer une alerte >>** si vous voulez créer une alerte. Une fois l’opération terminée, sélectionnez **Vérifier + créer**, puis **Créer**.

## <a name="view-results"></a>Afficher les résultats

1. Accédez à votre ressource **Network Watcher**, puis sélectionnez **Moniteur de connexion** sous *Supervision*. Vous devriez voir votre nouveau moniteur de connexion au bout de 5 minutes. Pour afficher la topologie du réseau et les graphiques de performances du moniteur de connexion, sélectionnez le test dans la liste déroulante des groupes de tests.

    :::image type="content" source="./media/how-to-configure-connection-monitor/overview.png" alt-text="Capture d’écran de la page de vue d’ensemble du moniteur de connexion." lightbox="./media/how-to-configure-connection-monitor/overview-expanded.png":::

1. Dans le panneau **Analyse des performances**, vous pouvez voir le pourcentage de vérification ayant échoué et les résultats de chaque test pour une durée d’aller-retour. Vous pouvez ajuster le délai d’exécution des données affichées en sélectionnant la liste déroulante située en haut du panneau.

    :::image type="content" source="./media/how-to-configure-connection-monitor/performance-analysis.png" alt-text="Capture d’écran du panneau Analyse des performances." lightbox="./media/how-to-configure-connection-monitor/performance-analysis-expanded.png":::

1. La fermeture du panneau **Analyse des performances** révèle la topologie du réseau détectée par le moniteur de connexion entre les points de terminaison source et de destination que vous avez sélectionnés. Cette vue vous montre les chemins bidirectionnels du trafic entre vos points de terminaison source et de destination. Vous pouvez également voir la latence tronçon par tronçon des paquets avant qu’ils n’atteignent le réseau de périphérie de Microsoft. 

    :::image type="content" source="./media/how-to-configure-connection-monitor/topology.png" alt-text="Capture d’écran de la topologie du réseau dans le Moniteur de connexion." lightbox="./media/how-to-configure-connection-monitor/topology-expanded.png":::

    Si vous sélectionnez un tronçon dans la vue de la topologie, des informations supplémentaires sur le tronçon s’affichent. Tous les problèmes détectés par le Moniteur de connexion concernant le tronçon s’affichent également ici.

    :::image type="content" source="./media/how-to-configure-connection-monitor/hop-details.png" alt-text="Capture d’écran d’informations supplémentaires concernant un tronçon réseau.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [supervision d’Azure ExpressRoute](monitor-expressroute.md)