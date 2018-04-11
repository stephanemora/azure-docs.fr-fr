---
title: Surveiller les connexions réseau avec Azure Network Watcher - Portail Azure | Microsoft Docs
description: Apprenez à surveiller la connectivité réseau avec Azure Network Watcher à l’aide du portail Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: 0d550d3bda119cfcb9ecc6f852006d5e325fdfa3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Surveiller les connexions réseau avec Azure Network Watcher à l’aide du portail Azure

Découvrez comment utiliser le contrôle de la connexion pour surveiller la connectivité réseau entre une machine virtuelle Azure et une adresse IP. Le contrôle de la connexion assure la surveillance entre les ports et les adresses IP source et de destination. Le contrôle de la connexion permet des scénarios tels que l’analyse de la connectivité d’une machine virtuelle d’un réseau virtuel à une machine virtuelle exécutant SQL Server dans un réseau virtuel identique ou différent, sur le port 1433. Le contrôle de la connexion fournit la latence de la connexion en tant que métrique Azure Monitor, enregistrée toutes les 60 secondes. Il fournit également une topologie tronçon par tronçon et identifie les problèmes de configuration ayant un impact sur votre connexion.

## <a name="prerequisites"></a>Prérequis


Vous devez respecter les prérequis suivants avant d’effectuer les étapes décrites dans cet article :

* Une instance de Network Watcher dans la région pour laquelle vous voulez surveiller une connexion. Si vous n’en avez pas déjà une, vous pouvez en créer une en suivant les étapes indiquées dans [Créer une instance Azure Network Watcher](network-watcher-create.md).
* Une machine virtuelle à partir de laquelle procéder au contrôle. Pour découvrir comment créer une machine virtuelle, consultez les articles relatifs à la création d’une machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
* Avoir installé l’extension `AzureNetworkWatcherExtension` dans la machine virtuelle dont vous souhaitez effectuer la surveillance d’une connexion. Pour installer l’extension dans une machine virtuelle Windows, consultez [Extension de machine virtuelle Agent Azure Network Watcher pour Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) et pour l’installer dans une machine virtuelle Linux, consultez [Extension de machine virtuelle Agent Azure Network Watcher pour Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au [Portail Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Créer un moniteur de connexion

Les étapes suivantes permettent de contrôler la connexion à une machine virtuelle de destination sur les ports 80 et 1433 :

1. Dans la partie gauche du portail, sélectionnez **Tous les services**.
2. Entrez *network watcher* dans la zone **Filtre**. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
3. Sous **SURVEILLANCE**, sélectionnez **Contrôle de la conversion**.
4. Sélectionnez **Ajouter**.
5. Entrez ou sélectionnez les informations appropriées à la connexion à contrôler, puis sélectionnez **Ajouter**. Dans l’exemple illustré dans l’image suivante, la connexion contrôlée est établie entre la machine virtuelle *MultiTierApp0* et la machine virtuelle *Database0* via le port 80 :

    ![Ajouter un contrôle de la connexion](./media/connection-monitor/add-connection-monitor.png)

    La surveillance commence. Le moniteur de connexion procède à un sondage toutes les 60 secondes.
6. Effectuez de nouveau l’étape 5 en spécifiant les mêmes machines virtuelles source et de destination, ainsi que les valeurs suivantes :
    
    |Paramètre  |Valeur          |
    |---------|---------      |
    |NOM     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>Afficher le contrôle de la connexion

1. Effectuez les étapes 1 à 3 dans [Créer un contrôle de la connexion](#create-a-connection-monitor) pour afficher le contrôle de la connexion.
2. L’image suivante affiche les détails de la connexion *AppToDB(80)*. L’**État** est accessible. L’**Affichage graphique** montre le **temps d’aller-retour moyen** et le **% d’échecs des sondes**. Le graphique fournit des informations tronçon par tronçon et indique qu’aucun problème n’impacte l’accessibilité de la destination.

    ![Affichage du graphique](./media/connection-monitor/view-graph.png)

3. L’affichage de la connexion *AppToDB(1433)*, illustrée dans l’image suivante, indique que pour les mêmes machines virtuelles source et de destination, l’état est inaccessible sur le port 1433. La **Vue de grille** dans ce scénario fournit les informations tronçon par tronçon et le problème ayant un impact sur l’accessibilité. Dans ce cas, une règle du Groupe de sécurité réseau bloque tout le trafic sur le port 1433 sur le second tronçon.

    ![Affichage de grille](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en [créant une capture de paquets déclenchée par alerte](network-watcher-alert-triggered-packet-capture.md).
- Déterminez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en [vérifiant le flux IP](network-watcher-check-ip-flow-verify-portal.md).