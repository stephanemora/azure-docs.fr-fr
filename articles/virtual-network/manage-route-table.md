---
title: Créer, modifier ou supprimer une table de routage Azure
titlesuffix: Azure Virtual Network
description: Découvrez comment créer, modifier ou supprimer une table de routage.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356651"
---
# <a name="create-change-or-delete-a-route-table"></a>Créer, modifier ou supprimer une table de routage

Azure achemine automatiquement le trafic entre les sous-réseaux, les réseaux virtuels et les réseaux locaux Azure. Si vous souhaitez modifier un routage par défaut d’Azure, vous pouvez le faire en créant une table de routage. Si vous ne maîtrisez pas encore le routage des réseaux virtuels, vous trouverez plus d’informations à ce sujet dans la [Vue d’ensemble du routage](virtual-networks-udr-overview.md) ou en suivant un [tutoriel](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

* Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).<br>
* Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.<br>
* Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 1.0.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.<br>
* Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel exige la version 2.0.31 ou une version ultérieure d’Azure CLI. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

Le compte auquel vous vous connectez ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, répertoriées dans [Autorisations](#permissions).

## <a name="create-a-route-table"></a>Créer une table de routage

Le nombre de tables de routage que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Dans l’angle supérieur gauche du portail, sélectionnez **+ Créer une ressource**.
1. Sélectionnez **Mise en réseau**, puis **Table de routage**.
1. Attribuez un **nom** à la table de routage, sélectionnez votre **abonnement**, créez un **groupe de ressources** ou sélectionnez un groupe de ressources existant, sélectionnez un **emplacement** et cliquez sur **Créer**. Si vous envisagez d’associer la table de routage à un sous-réseau d’un réseau virtuel connecté à votre réseau local via une passerelle VPN et que vous désactivez la **Propagation des itinéraires de passerelle de réseau virtuel**, vos itinéraires locaux ne sont pas propagés aux interfaces réseau du sous-réseau.

### <a name="create-route-table---commands"></a>Créer une table de routage (commandes)

* Azure CLI : [az network route-table create](/cli/azure/network/route-table/route)<br>
* PowerShell : [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Afficher les tables de routage

Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la. Les tables de routage présentes dans votre abonnement sont répertoriées.

### <a name="view-route-table---commands"></a>Afficher une table de routage (commandes)

* Azure CLI : [az network route-table list](/cli/azure/network/route-table/route)<br>
* PowerShell : [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Afficher les détails d’une table de routage

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Dans la liste, sélectionnez la table de routage dont vous souhaitez afficher les détails. Sous **PARAMÈTRES**, vous pouvez afficher les **Itinéraires** de la table de routage et les **Sous-réseaux** auxquels elle est associée.
1. Pour en savoir plus sur les paramètres Azure communs, consultez les informations suivantes :

    * [Journal d’activité](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Contrôle d’accès (IAM)](../role-based-access-control/overview.md)<br>
    * [Balises](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Verrous](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Script Automation](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Afficher les détails d’une table de routage (commandes)

* Azure CLI : [az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell : [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Modifier une table de routage

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Sélectionnez la table de routage à modifier. Les modifications les plus courantes sont l’[ajout](#create-a-route) ou la [suppression](#delete-a-route) d’itinéraires, et l’[association](#associate-a-route-table-to-a-subnet) de tables de routage à des sous-réseaux ou leur [dissociation](#dissociate-a-route-table-from-a-subnet) de ces derniers.

### <a name="change-a-route-table---commands"></a>Modifier une table de routage (commandes)

* Azure CLI : [az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell : [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Un sous-réseau peut avoir zéro ou une table de routage associée. Une table de routage peut être associée à plusieurs ou aucun sous-réseau. Étant donné que les tables de routage ne sont pas associées à des réseaux virtuels, vous devez associer une table de routage à chaque sous-réseau auquel vous souhaitez associer la table. Tout le trafic sortant du sous-réseau est basé sur les itinéraires que vous avez créés dans les tables de routage, les [itinéraires par défaut](virtual-networks-udr-overview.md#default) et les itinéraires propagés à partir d’un réseau local, si le réseau virtuel est connecté à une passerelle de réseau virtuel Azure (ExpressRoute ou VPN). Vous pouvez uniquement associer une table de routage à des sous-réseaux de réseaux virtuels qui se trouvent au même emplacement et dans le même abonnement Azure que la table de routage.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
1. Dans la liste, sélectionnez le réseau virtuel qui contient le sous-réseau auquel associer une table de routage.
1. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
1. Sélectionnez le sous-réseau auquel associer la table de routage.
1. Cliquez sur **Table de routage**, sélectionnez la table de routage à associer au sous-réseau, puis cliquez sur **Enregistrer**.

Si votre réseau virtuel est connecté à une passerelle VPN Azure, n’associez pas de table de routage au [sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) incluant un itinéraire avec une destination de 0.0.0.0/0. Cela peut empêcher la passerelle de fonctionner correctement. Pour plus d’informations sur l’utilisation de 0.0.0.0/0 dans un routage, consultez [Routage du trafic du réseau virtuel](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associer une table de routage (commandes)

* Azure CLI : [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell : [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissocier une table de routage d’un sous-réseau

Quand vous dissociez une table de routage d’un sous-réseau, Azure achemine le trafic en fonction de ses [itinéraires par défaut](virtual-networks-udr-overview.md#default).

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
1. Sélectionnez le réseau virtuel qui contient le sous-réseau duquel dissocier une table de routage.
1. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
1. Sélectionnez le sous-réseau duquel dissocier la table de routage.
1. Cliquez sur **Table de routage**, sélectionnez **Aucune**, puis cliquez sur **Enregistrer**.

### <a name="dissociate-a-route-table---commands"></a>Dissocier une table de routage (commandes)

* Azure CLI : [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell : [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Supprimer une table de routage

Une table de routage associée à un sous-réseau ne peut pas être supprimée. [Dissociez](#dissociate-a-route-table-from-a-subnet) la table de routage de tous les sous-réseaux avant de tenter de la supprimer.

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Sélectionnez **...** à droite de la table de routage à supprimer.
1. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

### <a name="delete-a-route-table---commands"></a>Supprimer une table de routage (commandes)

* Azure CLI : [az network route-table delete](/cli/azure/network/route-table/route)<br>
* PowerShell : [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Créer un itinéraire

Le nombre d’itinéraires par table de routage que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Dans la liste, sélectionnez la table de routage à laquelle vous souhaitez ajouter un itinéraire.
1. Sous **PARAMÈTRES**, sélectionnez **Itinéraires**.
1. Sélectionnez **Ajouter**.
1. Attribuez un **nom** unique à l’itinéraire dans la table de routage.
1. Entrez le **préfixe d’adresse** (dans la notation CIDR) vers lequel vous souhaitez acheminer le trafic. Le préfixe ne peut pas être dupliqué dans plusieurs itinéraires de la table de routage, bien qu’il puisse se trouver dans un autre préfixe. Par exemple, si vous avez défini 10.0.0.0/16 comme préfixe pour un itinéraire, vous pouvez toujours définir un autre itinéraire avec le préfixe d’adresse 10.0.0.0/24. Azure sélectionne un itinéraire pour le trafic en fonction de la correspondance de préfixe la plus longue. Pour en savoir plus sur la façon dont Azure sélectionne les itinéraires, consultez [vue d’ensemble du routage](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Sélectionnez un **type de tronçon suivant**. Pour obtenir une description détaillée de tous les types de tronçon suivant, consultez [vue d’ensemble du routage](virtual-networks-udr-overview.md).
1. Entrez une adresse IP pour l’**adresse de tronçon suivant**. Vous pouvez uniquement entrer une adresse si vous avez sélectionné *Appliance virtuelle* sous **Type de tronçon suivant**.
1. Sélectionnez **OK**.

### <a name="create-a-route---commands"></a>Créer un itinéraire (commandes)

* Azure CLI : [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell : [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Afficher des itinéraires

Une table de routage peut contenir plusieurs ou aucun itinéraire. Pour en savoir plus sur les informations affichées avec les itinéraires, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Dans la liste, sélectionnez la table de routage dont vous souhaitez afficher les itinéraires.
1. Sous **PARAMÈTRES**, sélectionnez **Itinéraires**.

### <a name="view-routes---commands"></a>Afficher les itinéraires (commandes)

* Azure CLI : [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell : [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Afficher les détails d’un itinéraire

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Sélectionnez la table de routage contenant l’itinéraire dont vous souhaitez afficher les détails.
1. Sélectionnez **Itinéraires**.
1. Sélectionnez l’itinéraire dont vous souhaitez afficher les détails.

### <a name="view-details-of-a-route---commands"></a>Afficher les détails d’un itinéraire (commandes)

* Azure CLI : [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell : [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Modifier un itinéraire

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Sélectionnez la table de routage dont vous souhaitez modifier un itinéraire.
1. Sélectionnez **Itinéraires**.
1. Sélectionnez l’itinéraire à modifier.
1. Modifiez les paramètres souhaités, puis cliquez sur **Enregistrer**.

### <a name="change-a-route---commands"></a>Modifier un itinéraire (commandes)

* Azure CLI : [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell : [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Supprimer un itinéraire

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de la recherche, sélectionnez-la.
1. Sélectionnez la table de routage de laquelle vous souhaitez supprimer un itinéraire.
1. Sélectionnez **Itinéraires**.
1. Dans la liste des itinéraires, sélectionnez **...** à droite de l’itinéraire à supprimer.
1. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

### <a name="delete-a-route---commands"></a>Supprimer un itinéraire (commandes)

* Azure CLI : [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell : [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Afficher les itinéraires effectifs

Les itinéraires effectifs de chaque interface réseau attachée à une machine virtuelle sont une combinaison des tables de routage que vous avez créées, des itinéraires par défaut d’Azure et des éventuels itinéraires propagés à partir de réseaux locaux suivant le protocole BGP à travers une passerelle de réseau virtuel Azure. Il est important de comprendre ce que représentent les itinéraires effectifs pour une interface réseau pour pouvoir résoudre les éventuels problèmes de routage. Vous pouvez afficher les itinéraires effectifs pour toute interface réseau attachée à une machine virtuelle en cours d’exécution.

1. Dans la zone de recherche située en haut du portail, entrez le nom d’une machine virtuelle dont vous souhaitez afficher les itinéraires effectifs. Si vous ne connaissez pas le nom de la machine virtuelle, entrez *machines virtuelles* dans la zone de recherche. Quand la mention **Machines virtuelles** apparaît dans les résultats de la recherche, sélectionnez-la et choisissez une machine virtuelle dans la liste.
1. Sous **PARAMÈTRES**, sélectionnez **Mise en réseau**.
1. Sélectionnez le nom d’une interface réseau.
1. Sous **SUPPORT + DÉPANNAGE**, cliquez sur **Routages effectifs**.
1. Passez en revue la liste des itinéraires effectifs pour déterminer si l’itinéraire correct existe pour l’emplacement vers lequel vous souhaitez acheminer le trafic. Pour en savoir plus sur les types de tronçon suivant qui apparaissent dans cette liste, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Afficher les itinéraires effectifs (commandes)

* Azure CLI : [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell : [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Valider le routage entre deux points de terminaison

Vous pouvez déterminer le type de tronçon suivant entre une machine virtuelle et l’adresse IP d’une autre ressource Azure, une ressource locale ou une ressource sur Internet. La détermination du routage d’Azure est utile pour pouvoir résoudre les éventuels problèmes de routage. Pour exécuter cette tâche, vous devez disposer d’un Network Watcher existant. Si ce n’est pas le cas, créez-en un en procédant comme indiqué sous [Créer une instance de Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Dans la zone de recherche située en haut du portail, entrez *network watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
1. Sous **OUTILS DE DIAGNOSTIC RÉSEAU**, sélectionnez **Tronçon suivant**.
1. Sélectionnez votre **abonnement** et le **groupe de ressources** de la machine virtuelle source à partir de laquelle vous souhaitez valider le routage.
1. Sélectionnez la **machine virtuelle**, l’**interface réseau** attachée à la machine virtuelle, et l’**adresse IP source** affectée à l’interface réseau à partir de laquelle vous souhaitez valider le routage.
1. Entrez l’**adresse IP de destination** vers laquelle vous souhaitez valider le routage.
1. Sélectionnez **Tronçon suivant**.
1. Après un court délai d’attente, les informations retournées vous indiquent le type de tronçon suivant et l’ID de l’itinéraire qui a acheminé le trafic. Pour en savoir plus sur les types de tronçon suivant qui apparaissent dans les informations retournées, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Valider le routage entre deux points de terminaison (commandes)

* Azure CLI : [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell : [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Autorisations

Pour que vous puissiez exécuter des tâches sur des tables de routage et des itinéraires, il est nécessaire que votre compte ait le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans le tableau suivant :

| Action                                                          |   Nom                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Lire une table de routage                                    |
| Microsoft.Network/routeTables/write                             |   Créer ou mettre à jour une table de routage                        |
| Microsoft.Network/routeTables/delete                            |   Supprimer une table de routage                                  |
| Microsoft.Network/routeTables/join/action                       |   Associer une table de routage à un sous-réseau                   |
| Microsoft.Network/routeTables/routes/read                       |   Lire un itinéraire                                          |
| Microsoft.Network/routeTables/routes/write                      |   Créer ou mettre à jour un itinéraire                              |
| Microsoft.Network/routeTables/routes/delete                     |   Supprimer un itinéraire                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obtenir la table de routage effective d’une interface réseau |
| Microsoft.Network/networkWatchers/nextHop/action                |   Obtenir le tronçon suivant à partir d’une machine virtuelle                           |

## <a name="next-steps"></a>Étapes suivantes

* Créer une table de routage avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou avec les [modèles Azure Resource Manager](template-samples.md)<br>
* Créer et appliquer une [stratégie Azure](policy-samples.md) pour des réseaux virtuels
