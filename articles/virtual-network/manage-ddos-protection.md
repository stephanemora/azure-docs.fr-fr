---
title: Gérer le service Protection DDos Standard Azure à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment utiliser les données de télémétrie du service Protection DDos Standard Azure dans Azure Monitor pour atténuer une attaque.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: jdial
ms.openlocfilehash: 59cfcc72abee100b95cf17033083827fbb30f9f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986691"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gérer le service Protection DDos Standard Azure à l’aide du portail Azure

Découvrez comment activer et désactiver la protection contre les attaques par déni de service distribué (DDoS), et utiliser la télémétrie pour atténuer ce type d’attaque, grâce au service Protection DDos Standard Azure. Ce service protège les ressources Azure, telles que les machines virtuelles, les équilibreurs de charge et les passerelles d’application auxquels une [adresse IP publique](virtual-network-public-ip-address.md) a été affectée. Pour en savoir plus sur le service Protection DDos Standard et ses fonctionnalités, voir [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md).

Avant de suivre les procédures décrites dans ce tutoriel, connectez-vous au Portail Azure à l’adresse https://portal.azure.com avec un compte ayant le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans [Autorisations](#permissions).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-ddos-protection-plan"></a>Créer un plan de protection DDoS

Un plan de protection DDoS définit un ensemble de réseaux virtuels pour lesquels la protection DDoS standard est activée par le biais des abonnements. Vous pouvez configurer un plan de protection DDoS pour votre organisation et lier des réseaux virtuels à partir de plusieurs abonnements au même plan. Le plan Protection DDoS lui-même est également associé à un abonnement, que vous sélectionnez à la création du plan. L’abonnement auquel le plan est associé subit la facturation mensuelle périodique du plan, ainsi que les frais de dépassement, si le nombre d’adresses IP publiques protégées est supérieur à 100. Pour en savoir plus sur la tarification du service de protection DDoS, consultez la rubrique relative aux [détails de tarification](https://azure.microsoft.com/pricing/details/ddos-protection/).

La création de plusieurs plans n’est pas requise pour la plupart des organisations. Un plan ne peut pas être déplacé d’un abonnement vers un autre. Si vous souhaitez changer l’abonnement auquel un plan est rattaché, vous devez [supprimer le plan existant](#work-with-ddos-protection-plans) et en créer un.

1. Sélectionnez **Créer une ressource** en haut à gauche du portail Azure.
2. Recherchez *DDoS*. Quand **Plan de protection DDoS** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
3. Sélectionnez **Créer**.
4. Entrez ou sélectionnez vos propres valeurs ou les exemples de valeurs suivants, puis sélectionnez **Créer** :

    |Paramètre        |Valeur                                              |
    |---------      |---------                                          |
    |NOM           | myDdosProtectionPlan                              |
    |Abonnement   | Sélectionnez votre abonnement.                         |
    |Groupe de ressources | Sélectionnez **Créer** et entrez *myResourceGroup*. |
    |Lieu       | USA Est                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Activer la protection DDoS pour un nouveau réseau virtuel

1. Sélectionnez **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Entrez ou sélectionnez vos propres valeurs, ou entrez ou sélectionnez les exemples de valeur suivants, acceptez les valeurs par défaut restantes, puis sélectionnez **Créer** :

    | Paramètre         | Valeur                                                        |
    | ---------       | ---------                                                    |
    | Nom            | myVirtualNetwork                                             |
    | Abonnement    | Sélectionnez votre abonnement.                                    |
    | Groupe de ressources  | Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**. |
    | Lieu        | USA Est                                                      |
    | Protection DDoS | Sélectionnez **Standard** puis, sous **Protection DDoS**, sélectionnez **myDdosProtectionPlan**. Le plan que vous sélectionnez peut se trouver dans le même abonnement que le réseau virtuel, ou dans un abonnement différent, mais les deux abonnements doivent être associés au même locataire Azure Active Directory.|

Vous ne pouvez pas déplacer un réseau virtuel vers un autre groupe de ressources ou abonnement quand la protection DDoS Standard est activée pour le réseau virtuel. Si vous devez déplacer un réseau virtuel pour lequel la protection DDoS Standard est activée, désactivez d’abord celle-ci, déplacez le réseau virtuel, puis activez la protection DDoS Standard. Après le déplacement, les seuils de stratégie réglée automatiquement pour toutes les adresses IP publiques protégées dans le réseau virtuel sont réinitialisés.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Activer la protection DDoS pour un réseau virtuel existant

1. Créez un plan de protection DDoS en effectuant les étapes décrites dans [Créer un plan de protection DDoS](#create-a-ddos-protection-plan), si vous n’avez pas de plan de protection DDoS.
2. Sélectionnez **Créer une ressource** en haut à gauche du portail Azure.
3. Entrez le nom du réseau virtuel pour lequel vous souhaitez activer le service DDoS Protection Standard dans la zone **Rechercher dans les ressources, les services et les documents** en haut du portail. Quand le nom du réseau virtuel apparaît dans les résultats de la recherche, sélectionnez-le.
4. Sélectionnez **Protection DDoS**, sous **PARAMÈTRES**.
5. Sélectionnez **Standard**. Sous **Plan de protection DDoS**, sélectionnez un plan de protection DDoS existant, ou le plan que vous avez créé à l’étape 1, puis sélectionnez **Enregistrer**. Le plan que vous sélectionnez peut se trouver dans le même abonnement que le réseau virtuel, ou dans un abonnement différent, mais les deux abonnements doivent être associés au même locataire Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Désactiver la protection DDoS pour un réseau virtuel

1. Entrez le nom du réseau virtuel pour lequel vous souhaitez désactiver le service DDoS Protection Standard dans la zone **Rechercher dans les ressources, les services et les documents** en haut du portail. Quand le nom du réseau virtuel apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Protection DDoS**, sous **PARAMÈTRES**.
3. Sélectionnez **De base** sous **Plan de protection DDoS**, puis sélectionnez **Enregistrer**.

## <a name="work-with-ddos-protection-plans"></a>Utiliser des plans de protection DDoS

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *DDoS* dans la zone **Filtre**. Quand **Plans de protection DDoS** apparaît dans les résultats, sélectionnez cette entrée.
3. Dans la liste, sélectionnez le plan de protection que vous souhaitez voir.
4. Tous les réseaux virtuels associés au plan sont répertoriés.
5. Si vous souhaitez supprimer un plan, vous devez d’abord dissocier tous les réseaux virtuels de celui-ci. Pour dissocier un plan d’un réseau virtuel, consultez [Désactiver la protection DDoS pour un réseau virtuel](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurer des alertes pour les métriques du service de protection DDoS

À l’aide de la configuration des alertes Azure Monitor, vous pouvez sélectionner les métriques du service de protection DDoS disponibles de votre choix pour être averti quand il existe une atténuation active pendant une attaque. Quand les conditions sont remplies, vous recevez un e-mail d’alerte à l’adresse spécifiée :

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sélectionnez **Métriques** sous **SERVICES PARTAGÉS**.
4. Entrez ou sélectionnez vos propres valeurs, ou entrez les exemples de valeur suivants, acceptez les valeurs par défaut restantes, puis sélectionnez **OK** :

    |Paramètre                  |Valeur                                                                                               |
    |---------                |---------                                                                                           |
    |NOM                     | myDdosAlert                                                                                        |
    |Abonnement             | Sélectionnez l’abonnement qui contient l’adresse IP publique pour laquelle vous souhaitez recevoir des alertes.        |
    |Groupe de ressources           | Sélectionnez le groupe de ressources qui contient l’adresse IP publique pour laquelle vous souhaitez recevoir des alertes.      |
    |Ressource                 | Sélectionnez la ressource qui contient l’adresse IP publique pour laquelle vous souhaitez recevoir des alertes. Le service DDoS surveille les adresses IP publiques affectées aux ressources dans un réseau virtuel. Si aucune ressource dans le réseau virtuel ne possède une adresse IP publique, vous devez d’abord créer une ressource dotée d’une adresse IP publique. Vous pouvez surveiller l’adresse IP publique de toutes les ressources déployées par le biais de Resource Manager (non classique) qui sont répertoriées dans [Réseau virtuel pour services Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), à l’exception des environnements Azure App Service et de la passerelle VPN Azure. Pour poursuivre ce tutoriel, vous pouvez créer rapidement une machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Métrique                   | Sous attaque DDoS ou non                                                                            |
    |Seuil                | **1** signifie que vous faites l’objet d’une attaque. **0** signifie que vous ne faites pas l’objet d’une attaque.                         |
    |Période                   | Sélectionnez la valeur de votre choix.                                                                   |
    |Notifier via e-mail         | Cochez la case.                                                                                  |
    |Administrateur supplémentaire | Entrez votre adresse e-mail, si vous n’êtes pas propriétaire d’e-mail, collaborateur ou lecteur pour l’abonnement. |

    Dans les minutes qui suivent la détection d’une attaque, vous recevez un e-mail en provenance des métriques Azure Monitor qui ressemble à l’image suivante :

    ![Alerte en cas d’attaque](./media/manage-ddos-protection/ddos-alert.png)


Pour simuler une attaque DDoS afin de valider votre alerte, consultez [Valider la détection d’une attaque DDoS](#validate-ddos-detection).

Vous pouvez également découvrir plus en détail la [configuration de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et les [applications logiques](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour créer des alertes.

## <a name="use-ddos-protection-telemetry"></a>Utiliser les données de télémétrie du service de protection DDoS

Les données de télémétrie pour une attaque sont fournies par le biais d’Azure Monitor en temps réel. Les données de télémétrie ne sont disponibles que pendant l’atténuation dont fait l’objet une adresse IP publique. Vous ne les voyez pas avant ou après l’atténuation d’une attaque.

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sélectionnez **Métriques** sous **SERVICES PARTAGÉS**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique dont vous souhaitez les données de télémétrie.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource**, puis sélectionnez l’adresse IP publique dont vous souhaitez les données de télémétrie.
6. Une série de **métriques disponibles** s’affiche sur le côté gauche de l’écran. Quand elles sont sélectionnées, ces métriques sont représentées dans le **graphique des métriques Azure Monitor** sur l’écran de vue d’ensemble.

Les noms des métriques présentent différents types de paquets et les octets/paquets, et les noms de balise sur chaque métrique sont essentiellement construits comme suit :

- **Nom de balise des paquets transférés** (par exemple, **Paquets entrants ignorés DDoS**) : nombre de paquets supprimés/nettoyés par le système de protection DDoS.
- **Nom de balise des paquets transférés** (par exemple, **Paquets entrants transférés DDoS**) : nombre de paquets transférés par le système DDoS vers l’adresse IP virtuelle de destination (trafic qui n’a pas été filtré).
- **Nom de balise du nombre de paquets** (par exemple, **Paquets entrants DDoS**) : nombre total de paquets ayant atteint le système de nettoyage, représentant la somme des paquets supprimés et transférés.

Pour simuler une attaque DDoS afin de valider les données de télémétrie, consultez [Valider la détection d’une attaque DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Voir les stratégies d’atténuation des risques liés à DDoS

DDoS Protection Standard applique trois stratégies de prévention réglées automatiquement (TCP SYN, TCP et UDP) pour chaque adresse IP publique de la ressource protégée, dans le réseau virtuel sur lequel la protection DDoS est activée. Vous pouvez voir les seuils de stratégie en sélectionnant les métriques **Paquets TCP entrants pour déclencher l’atténuation des risques liés à DDoS** et **Paquets UDP entrants pour déclencher l’atténuation des risques liés à DDoS**, comme indiqué dans l’image suivante :

![Voir les stratégies d’atténuation des risques](./media/manage-ddos-protection/view-mitigation-policies.png)

Les seuils de stratégie sont configurés automatiquement par le biais du système de profilage du trafic réseau basé sur l’apprentissage automatique Azure. L’atténuation des risques liés à DDoS pour l’adresse IP n’a lieu que si le seuil de stratégie est franchi.

## <a name="configure-ddos-attack-analytics"></a>Configurer l’analyse des attaques DDoS
La norme Azure DDoS Protection fournit une visualisation et des insights détaillés sur les attaques avec l’analyse des attaques DDoS. Les clients qui protègent leurs réseaux virtuels contre les attaques DDoS ont une visibilité détaillée du trafic des attaques et des mesures prises pour leur prévention par le biais de rapports de prévention des attaques et de journaux de flux de prévention des attaques. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configurer les rapports de prévention des attaques DDoS
La fonctionnalité Rapports de prévention des attaques utilise les données de protocole Netflow qui sont ensuite regroupées pour fournir des informations détaillées concernant l’attaque de votre ressource. Chaque fois qu’une ressource IP publique est attaquée, la génération de rapport démarre en même temps que la prévention. Un rapport incrémentiel est généré toutes les 5 minutes et un rapport post-prévention concernant toute la période de prévention. Cela permet de garantir que dans le cas où l’attaque DDoS se poursuit pendant une durée plus longue, vous pourrez afficher l’instantané le plus récent du rapport de prévention toutes les 5 minutes et un récapitulatif complet une fois la prévention de l’attaque terminée. 

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sous **PARAMÈTRES**, sélectionnez **Paramètres de diagnostic**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique que vous souhaitez journaliser.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource**, puis sélectionnez l’adresse IP publique spécifique dont vous souhaitez journaliser les métriques.
6. Sélectionnez **Activer les diagnostics pour collecter le journal DDoSMitigationReports**, puis sélectionnez autant d’options que nécessaire parmi les suivantes :

    - **Archiver dans un compte de stockage** : les données sont écrites dans un compte Stockage Azure. Pour en savoir plus sur cette option, consultez [Archiver les journaux de diagnostic](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Diffuser sur un hub d’événements** : permet à un récepteur de journal de sélectionner les journaux à l’aide d’un hub d’événements Azure. Les hubs d’événements permettent l’intégration à Splunk ou à d’autres systèmes SIEM. Pour en savoir plus sur cette option, consultez [Diffuser les journaux de diagnostic sur un hub d’événements](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Envoyer à Log Analytics** : écrit les journaux dans le service Azure OMS Log Analytics. Pour en savoir plus sur cette option, consultez [Collecte des journaux à utiliser dans Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Le rapport incrémentiel et le rapport de prévention incluent tous les deux les champs suivants :
- Vecteurs d’attaque
- Statistiques de trafic
- Raison des paquets abandonnés
- Protocoles impliqués
- 10 principaux pays ou régions sources
- 10 principaux ASN sources

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurer les journaux de prévention des attaques DDoS
Les journaux de flux de prévention des attaques vous permettent de passer en revue le trafic abandonné, le trafic transféré et d’autres points de données intéressants pendant une attaque DDoS active en temps quasi réel. Vous pouvez recevoir le flux constant de ces données dans vos systèmes SIEM par le biais d’un hub d’événements pour la supervision en temps quasi réel, prendre des actions éventuelles et répondre aux besoins de vos opérations de défense. 

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sous **PARAMÈTRES**, sélectionnez **Paramètres de diagnostic**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique que vous souhaitez journaliser.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource**, puis sélectionnez l’adresse IP publique spécifique dont vous souhaitez journaliser les métriques.
6. Sélectionnez **Activer les diagnostics pour collecter le journal DDoSMitigationFlowLogs**, puis sélectionnez autant d’options que nécessaire parmi les suivantes :

    - **Archiver dans un compte de stockage** : les données sont écrites dans un compte Stockage Azure. Pour en savoir plus sur cette option, consultez [Archiver les journaux de diagnostic](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Diffuser sur un hub d’événements** : permet à un récepteur de journal de sélectionner les journaux à l’aide d’un hub d’événements Azure. Les hubs d’événements permettent l’intégration à Splunk ou à d’autres systèmes SIEM. Pour en savoir plus sur cette option, consultez [Diffuser les journaux de diagnostic sur un hub d’événements](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Envoyer à Log Analytics** : écrit les journaux dans le service Azure OMS Log Analytics. Pour en savoir plus sur cette option, consultez [Collecte des journaux à utiliser dans Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
7. Pour afficher les données des journaux de flux dans le tableau de bord d’analyse Azure, vous pouvez importer l’exemple de tableau de bord à partir de https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Les journaux de flux comportent les champs suivants : 
- IP Source
- IP de destination
- Port source 
- Port de destination 
- Type de protocole 
- Action effectuée pendant la prévention



## <a name="validate-ddos-detection"></a>Valider la détection d’une attaque DDoS

Microsoft travaille en collaboration avec [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) pour créer une interface à l’aide de laquelle vous pouvez générer du trafic sur les adresses IP publiques dotées de DDoS Protection à des fins de simulation. La simulation BreakPoint Cloud vous permet d’effectuer les tâches suivantes :

- Valider la façon dont Microsoft Azure DDoS Protection protège vos ressources Azure contre les attaques DDoS
- Optimiser votre processus de réponse aux incidents en cas d’attaque DDoS
- Documenter la conformité DDoS
- Former des équipes de sécurité réseau

## <a name="permissions"></a>Autorisations

Pour que vous puissiez travailler avec des plans de protection DDoS, il est nécessaire que votre compte ait le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans le tableau suivant :

| Action                                            | NOM                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Lire un plan de protection DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Créer ou mettre à jour un plan de protection DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Supprimer un plan de protection DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Joindre un plan de protection DDoS              |

Pour que vous puissiez activer la protection DDoS sur un réseau virtuel, il est nécessaire que votre compte dispose des [autorisations appropriées pour les réseaux virtuels](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Étapes suivantes

- Créer et appliquer une [stratégie Azure](policy-samples.md) pour des réseaux virtuels