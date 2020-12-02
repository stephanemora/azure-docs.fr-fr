---
title: Consulter et configurer la télémétrie de la protection DDoS
description: Découvrez comment afficher et configurer la télémétrie de la protection DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989392"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Afficher et configurer la télémétrie de la protection DDoS

La norme Azure DDoS Protection fournit une visualisation et des insights détaillés sur les attaques avec l’analyse des attaques DDoS. Les clients qui protègent leurs réseaux virtuels contre les attaques DDoS ont une visibilité détaillée du trafic des attaques et des mesures prises pour leur prévention par le biais de rapports de prévention des attaques et de journaux de flux de prévention des attaques. La télémétrie enrichie est exposée via Azure Monitor et inclut des métriques détaillées pendant la durée d’une attaque DDoS. La génération d’alertes peut être configurée pour l’une des métriques Azure Monitor exposées par DDoS Protection. La journalisation peut également être intégrée à [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics et Stockage Azure pour l’analyse avancée par le biais de l’interface de diagnostic d’Azure Monitor.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer des alertes pour les métriques du service de protection DDoS
> * Utiliser les données de télémétrie du service de protection DDoS
> * Voir les stratégies d’atténuation des risques liés à DDoS
> * Afficher les alertes de protection DDoS dans Azure Security Center

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Avant de pouvoir exécuter la procédure de ce tutoriel, vous devez créer un [plan de protection Azure DDoS Standard](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurer des alertes pour les métriques du service de protection DDoS

À l’aide de la configuration des alertes Azure Monitor, vous pouvez sélectionner les métriques du service de protection DDoS disponibles de votre choix pour être averti quand il existe une atténuation active pendant une attaque. Quand les conditions sont remplies, vous recevez un e-mail d’alerte à l’adresse spécifiée :

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sélectionnez **Métriques** sous **SERVICES PARTAGÉS**.
4. Entrez ou sélectionnez vos propres valeurs, ou entrez les exemples de valeur suivants, acceptez les valeurs par défaut restantes, puis sélectionnez **OK** :

    |Paramètre                  |Valeur                                                                                               |
    |---------                |---------                                                                                           |
    |Nom                     | Entrez _MyDdosAlert_.                                                                                |
    |Abonnement             | Sélectionnez l’abonnement qui contient l’adresse IP publique pour laquelle vous souhaitez recevoir des alertes.        |
    |Resource group           | Sélectionnez le groupe de ressources qui contient l’adresse IP publique pour laquelle vous souhaitez recevoir des alertes.      |
    |Ressource                 | Sélectionnez la ressource qui contient l’adresse IP publique pour laquelle vous souhaitez recevoir des alertes. Le service DDoS surveille les adresses IP publiques affectées aux ressources dans un réseau virtuel. Si aucune ressource dans le réseau virtuel ne possède une adresse IP publique, vous devez d’abord créer une ressource dotée d’une adresse IP publique. Vous pouvez surveiller l’adresse IP publique de toutes les ressources déployées par le biais de Resource Manager (non classique) qui sont répertoriées dans [Réseau virtuel pour services Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), à l’exception des environnements Azure App Service et de la passerelle VPN Azure. Pour poursuivre ce tutoriel, vous pouvez créer rapidement une machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Métrique                   | Sélectionnez **Sous attaque DDoS ou non**.                                                                |
    |Seuil                | **1** signifie que vous faites l’objet d’une attaque. **0** signifie que vous ne faites pas l’objet d’une attaque.                         |
    |Période                   | Sélectionnez la valeur de votre choix.                                                                   |
    |Notifier via e-mail         | Cochez la case.                                                                                 |
    |Administrateur supplémentaire | Entrez votre adresse e-mail, si vous n’êtes pas propriétaire d’e-mail, collaborateur ou lecteur pour l’abonnement. |

    Dans les minutes qui suivent la détection d’une attaque, vous recevez un e-mail en provenance des métriques Azure Monitor qui ressemble à l’image suivante :

    ![Alerte en cas d’attaque](./media/manage-ddos-protection/ddos-alert.png)


Pour simuler une attaque DDoS afin de valider votre alerte, consultez [Valider la détection d’une attaque DDoS](test-through-simulations.md).

Vous pouvez également découvrir plus en détail la [configuration de webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et les [applications logiques](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour créer des alertes.

## <a name="use-ddos-protection-telemetry"></a>Utiliser les données de télémétrie du service de protection DDoS

Les données de télémétrie pour une attaque sont fournies par le biais d’Azure Monitor en temps réel. Les données de télémétrie ne sont disponibles que pendant l’atténuation dont fait l’objet une adresse IP publique. Vous ne les voyez pas avant ou après l’atténuation d’une attaque.

1. Sélectionnez **Tous les services** dans la partie supérieure gauche du portail.
2. Entrez *Surveiller* dans la zone **Filtre**. Quand **Surveiller** apparaît dans les résultats, sélectionnez cette option.
3. Sélectionnez **Métriques** sous **SERVICES PARTAGÉS**.
4. Sélectionnez **l’Abonnement** et le **Groupe de ressources** qui contiennent l’adresse IP publique dont vous souhaitez les données de télémétrie.
5. Sélectionnez **Adresse IP publique** pour **Type de ressource**, puis sélectionnez l’adresse IP publique dont vous souhaitez les données de télémétrie.
6. Une série de **métriques disponibles** s’affiche sur le côté gauche de l’écran. Quand elles sont sélectionnées, ces métriques sont représentées dans le **graphique des métriques Azure Monitor** sur l’écran de vue d’ensemble.
7. Définissez le type **d’agrégation** sur **Max**

Les noms des métriques présentent différents types de paquets et les octets/paquets, et les noms de balise sur chaque métrique sont essentiellement construits comme suit :

- **Nom de balise des paquets supprimés** (par exemple, **Paquets entrants ignorés DDoS) :** nombre de paquets supprimés/nettoyés par le système de protection DDoS.
- **Nom de balise des paquets transférés** (par exemple, **Paquets entrants transférés DDoS) :** nombre de paquets transférés par le système DDoS vers l’adresse IP virtuelle de destination (trafic qui n’a pas été filtré).
- **Nom de balise du nombre de paquets** (par exemple, **Paquets entrants DDoS**) : nombre total de paquets ayant atteint le système de nettoyage, représentant la somme des paquets supprimés et transférés.

Cette [règle d’alerte Azure Monitor](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) exécute une requête simple pour détecter si une atténuation DDoS active se produit. Pour simuler une attaque DDoS afin de valider les données de télémétrie, consultez [Valider la détection d’une attaque DDoS](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Voir les stratégies d’atténuation des risques liés à DDoS

DDoS Protection Standard applique trois stratégies de prévention réglées automatiquement (TCP SYN, TCP et UDP) pour chaque adresse IP publique de la ressource protégée, dans le réseau virtuel sur lequel la protection DDoS est activée. Vous pouvez voir les seuils de stratégie en sélectionnant les métriques **Paquets TCP entrants pour déclencher l’atténuation des risques liés à DDoS** et **Paquets UDP entrants pour déclencher l’atténuation des risques liés à DDoS** avec le type **d’agrégation** défini sur « Max », comme indiqué dans l’image suivante :

![Voir les stratégies d’atténuation des risques](./media/manage-ddos-protection/view-mitigation-policies.png)

Les seuils de stratégie sont configurés automatiquement par le biais du système de profilage du trafic réseau basé sur l’apprentissage automatique Azure. L’atténuation des risques liés à DDoS pour l’adresse IP n’a lieu que si le seuil de stratégie est franchi.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Afficher les alertes de protection DDoS dans Azure Security Center

Azure Security Center fournit une liste des [alertes de sécurité](../security-center/security-center-managing-and-responding-alerts.md), accompagnée d’informations pour examiner et corriger les problèmes. Avec cette fonctionnalité, vous obtenez une vue unifiée des alertes, y compris les alertes en rapport avec les attaques DDoS et les actions mises en place pour atténuer l’attaque en temps quasi-réel.
Il existe deux alertes spécifiques qui seront affichées pour toutes les détections et atténuation d’attaque DDoS :

- **DDoS Attack detected for Public IP** (Attaque DDoS détectée pour l’adresse IP publique) : Cette alerte est générée lorsque le service de protection DDoS détecte que l’une de vos adresses IP publiques est la cible d’une attaque DDoS.
- **DDoS Attack mitigated for Public IP** (Attaque DDoS atténuée pour l’adresse IP publique) : Cette alerte est générée lorsqu’une attaque sur l’adresse IP publique a été atténuée.
Pour afficher les alertes, ouvrez **Security Center** dans le portail Azure. Sous **Protection contre les menaces**, sélectionnez **Alertes de sécurité**. La capture d’écran suivante présente un exemple des alertes d’attaque DDoS.

![Alerte DDoS dans Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Les alertes incluent des informations générales sur l’adresse IP publique faisant l’objet de l’attaque, des informations géographiques et sur l’intelligence des menaces, ainsi que des étapes de correction.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

- Configurer des alertes pour les métriques du service de protection DDoS
- Utiliser les données de télémétrie du service de protection DDoS
- Voir les stratégies d’atténuation des risques liés à DDoS
- Afficher les alertes de protection DDoS dans Azure Security Center

Pour savoir comment configurer les rapports de prévention des attaques et les journaux de flux, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Configurer les rapports de prévention des attaques DDoS et les journaux de flux](reports-and-flow-logs.md)