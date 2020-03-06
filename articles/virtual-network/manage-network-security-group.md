---
title: Créer, modifier ou supprimer un groupe de sécurité réseau Azure
titlesuffix: Azure Virtual Network
description: Découvrez comment créer, changer ou supprimer un groupe de sécurité réseau.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: fa933b820d8677e4d080b54ce5e6a5d506ea38fc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360516"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Créer, changer ou supprimer un groupe de sécurité réseau

Les règles de sécurité dans les groupes de sécurité réseau permettent de filtrer le type de trafic réseau qui peut circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel. Si vous n’êtes pas familiarisé avec les groupes de sécurité réseau, consultez [Présentation de la sécurité réseau Azure](security-overview.md), puis effectuez le tutoriel [Filtrer le trafic réseau](tutorial-filter-network-traffic.md) pour avoir une expérience pratique de ces groupes.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 1.0.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel requiert Azure CLI version 2.0.28 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

Le compte auquel vous vous connectez ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou avoir un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des actions appropriées répertoriées dans [Autorisations ](#permissions).

## <a name="work-with-network-security-groups"></a>Utiliser des groupes de sécurité réseau

Vous pouvez effectuer différentes opérations concernant les groupes de sécurité réseau : en créer, [les voir tous](#view-all-network-security-groups), [afficher les détails d’un des groupes](#view-details-of-a-network-security-group), [les changer](#change-a-network-security-group) et [en supprimer](#delete-a-network-security-group). Vous pouvez également [associer ou dissocier](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) un groupe de sécurité réseau à ou d’une interface réseau ou sous-réseau.

### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Le nombre de groupes de sécurité réseau que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
2. Sélectionnez **Mise en réseau**, puis **Groupe de sécurité réseau**.
3. Attribuez un **nom** au groupe de sécurité réseau, sélectionnez votre **abonnement**, créez un **groupe de ressources** ou sélectionnez un groupe de ressources existant, sélectionnez un **emplacement**, puis cliquez sur **Créer**.

**Commandes**

- Azure CLI : [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell : [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Voir tous les groupes de sécurité réseau

Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la. Les groupes de sécurité réseau présents dans votre abonnement sont répertoriés.

**Commandes**

- Azure CLI : [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell : [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Voir les détails d’un groupe de sécurité réseau

1. Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez le groupe de sécurité réseau dont vous souhaitez voir les détails. Sous **PARAMÈTRES** vous pouvez voir les **Règles de sécurité de trafic entrant**, les **Règles de sécurité de trafic sortant**, les **Interfaces réseau** et les **Sous-réseaux** auxquels le groupe de sécurité réseau est associé. Vous pouvez également activer ou désactiver les **Journaux de diagnostic** et afficher les **Règles de sécurité effectives**. Pour plus d’informations, consultez [Journaux de diagnostic](virtual-network-nsg-manage-log.md) et [Afficher les règles de sécurité effectives](diagnose-network-traffic-filter-problem.md).
3. Pour en savoir plus sur les paramètres Azure courants répertoriés, consultez les articles suivants :
    *   [Journal d’activité](../azure-monitor/platform/platform-logs-overview.md)
    *   [Contrôle d’accès (IAM)](../role-based-access-control/overview.md)
    *   [Balises](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Verrous](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script Automation](../azure-resource-manager/templates/export-template-portal.md)

**Commandes**

- Azure CLI : [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell : [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Changer un groupe de sécurité réseau

1. Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez le groupe de sécurité réseau que vous souhaitez changer. Les modifications les plus courantes sont [ajouter](#create-a-security-rule) ou [supprimer](#delete-a-security-rule) des règles de sécurité et [associer un groupe de sécurité réseau à un sous-réseau ou à une interface réseau ou l’en dissocier](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Commandes**

- Azure CLI : [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell : [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Associer un groupe de sécurité réseau à un sous-réseau ou à une interface réseau ou l’en dissocier

Pour associer un groupe de sécurité réseau à une interface réseau ou l’en dissocier, consultez [Associer un groupe de sécurité réseau à une interface réseau ou l’en dissocier](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Pour associer un groupe de sécurité réseau à un sous-réseau ou l’en dissocier, consultez [Modifier les paramètres de sous-réseau](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Supprimer un groupe de sécurité réseau

Si un groupe de sécurité réseau est associé à des sous-réseaux ou à des interfaces réseau, il ne peut pas être supprimé. Dissociez un groupe de sécurité réseau de tous les sous-réseaux et interfaces réseau avant de tenter de le supprimer.

1. Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez le groupe de sécurité réseau que vous souhaitez supprimer.
3. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

**Commandes**

- Azure CLI : [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell : [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Utiliser des règles de sécurité

Un groupe de sécurité réseau contient zéro règle de sécurité, ou plus. Vous pouvez effectuer différentes opérations concernant les règles de sécurité : en créer, [les voir toutes](#view-all-security-rules), [afficher les détails d’une des règles](#view-details-of-a-security-rule), [les changer](#change-a-security-rule) et [en supprimer](#delete-a-security-rule).

### <a name="create-a-security-rule"></a>Créer une règle de sécurité

Le nombre de règles par groupe de sécurité réseau que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez le groupe de sécurité réseau auquel vous souhaitez ajouter une règle de sécurité.
3. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic entrant**. Plusieurs règles existantes sont répertoriées, dont certaines que vous n’avez peut-être pas ajoutées. Quand un groupe de sécurité réseau est créé, plusieurs règles de sécurité par défaut y sont créées. Pour plus d’informations, consultez [Règles de sécurité par défaut](security-overview.md#default-security-rules).  Vous ne pouvez pas supprimer les règles de sécurité par défaut, mais vous pouvez les remplacer par des règles qui ont une priorité plus élevée.
4. <a name = "security-rule-settings"></a>Sélectionnez **+ Ajouter**.  Sélectionnez ou ajoutez des valeurs pour les paramètres suivants, puis sélectionnez **OK** :
    
    |Paramètre  |Valeur  |Détails  |
    |---------|---------|---------|
    |Source     | Sélectionnez **N’importe lequel**, **Groupe de sécurité d’application**, **Adresses IP**, ou **Balise de service** pour les règles de sécurité du trafic entrant. Si vous créez une règle de sécurité du trafic sortant, les options sont les mêmes que celles répertoriées pour **Destination**.       | Si vous choisissez **Groupe de sécurité d’application**, sélectionnez un ou plusieurs groupes de sécurité d’application existants se trouvant dans la même région que celle de l’interface réseau. Découvrez comment [créer un groupe de sécurité d’application](#create-an-application-security-group). Si vous sélectionnez **Groupe de sécurité d’application** en tant que **source** et **destination**, les interfaces réseau dans les deux groupes de sécurité d’application doivent se trouver dans le même réseau virtuel. Si vous sélectionnez **Adresses IP**, spécifiez les **Plages d’adresses IP/CIDR sources**. Vous pouvez spécifier une valeur unique ou une liste de valeurs séparées par des virgules. Un exemple de plusieurs valeurs est 10.0.0.0/16, 192.188.1.1. Le nombre de valeurs que vous pouvez spécifier est limité. Pour plus d’informations, consultez [Limites de mise en réseau](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Si vous sélectionnez **Balise de service**, sélectionnez une balise de service. Une balise de service est un identificateur prédéfini pour une catégorie d’adresses IP. Pour en savoir plus sur les balises de service disponibles et ce que représente chaque balise, consultez [Balises de service](security-overview.md#service-tags). Si l’adresse IP que vous spécifiez est affectée à une machine virtuelle Azure, veillez à spécifier l’adresse IP privée, et non l’adresse IP publique, affectée à la machine virtuelle. Les règles de sécurité sont traitées une fois qu’Azure a converti l’adresse IP publique en adresse IP privée pour les règles de sécurité liées au trafic entrant, et avant qu’Azure ne convertisse une adresse IP privée en une adresse IP publique pour les règles de trafic sortant. Pour plus d’informations sur les adresses IP privées et publiques dans Azure, consultez [Types d’adresses IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Source port ranges     | Spécifiez un port unique, tel que 80, une plage de ports, telle que 1024-65535, ou une liste séparée par des virgules de ports uniques et/ou de plages de ports, telle que 80, 1024-65535. Entrez un astérisque pour autoriser le trafic sur n’importe quel port. | Les ports et les plages spécifient les ports sur lesquels la règle autorise ou refuse le trafic. Le nombre de ports que vous pouvez spécifier est limité. Pour plus d’informations, consultez [Limites de mise en réseau](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).  |
    |Destination     | Sélectionnez **N’importe lequel**, **Groupe de sécurité d’application**, **Adresses IP**, ou **Réseau virtuel** pour les règles de sécurité du trafic sortant. Si vous créez une règle de sécurité du trafic entrant, les options sont les mêmes que celles répertoriées pour **Source**.        | Si vous choisissez **Groupe de sécurité d’application**, vous devez sélectionner un ou plusieurs groupes de sécurité d’application existants se trouvant dans la même région que celle de l’interface réseau. Découvrez comment [créer un groupe de sécurité d’application](#create-an-application-security-group). Si vous choisissez **Groupe de sécurité d’application**, sélectionnez un groupe de sécurité d’application existant se trouvant dans la même région que celle de l’interface réseau. Si vous sélectionnez **Adresses IP**, spécifiez les **Plages d’adresses IP/CIDR de destination**. À l’image de **Source** et de **Plages d’adresses IP/CIDR sources**, vous pouvez spécifier une seule adresse ou plage, ou plusieurs dans une certaine limite. Si vous sélectionnez **Réseau virtuel**, qui est une balise de service, le trafic est autorisé sur toutes les adresses IP dans l’espace d’adressage du réseau virtuel. Si l’adresse IP que vous spécifiez est affectée à une machine virtuelle Azure, veillez à spécifier l’adresse IP privée, et non l’adresse IP publique, affectée à la machine virtuelle. Les règles de sécurité sont traitées une fois qu’Azure a converti l’adresse IP publique en adresse IP privée pour les règles de sécurité liées au trafic entrant, et avant qu’Azure ne convertisse une adresse IP privée en une adresse IP publique pour les règles de trafic sortant. Pour plus d’informations sur les adresses IP privées et publiques dans Azure, consultez [Types d’adresses IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Plages de ports de destination     | Spécifiez une valeur unique ou une liste de valeurs séparées par des virgules. | À l’image de **Plages de ports sources**, vous pouvez spécifier un seul port et une seule plage, ou plusieurs dans une certaine limite. |
    |Protocol     | Sélectionnez **Any (Tout)** , **TCP**, **UDP** ou **ICMP**.        |         |
    |Action     | Sélectionnez **Autoriser** ou **Refuser**.        |         |
    |Priority     | Entrez une valeur comprise entre 100 et 4096 qui est unique pour toutes les règles de sécurité au sein du groupe de sécurité réseau. |Les règles sont traitées par ordre de priorité. Plus le numéro est faible, plus la priorité est élevée. Nous vous recommandons de laisser un écart entre les numéros de priorité quand vous créez des règles, par exemple, 100, 200, 300. Cela permet par la suite d’intercaler de nouvelles règles.         |
    |Nom     | Nom unique de la règle au sein du groupe de sécurité réseau.        |  Le nom peut comprendre jusqu’à 80 caractères. Il doit commencer par une lettre ou un chiffre, se terminer par une lettre, un chiffre ou un trait de soulignement, et ne peut contenir que des lettres, des chiffres, des traits de soulignement, des points ou des tirets.       |
    |Description     | Description facultative.        |         |

**Commandes**

- Azure CLI : [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell : [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Voir toutes les règles de sécurité

Un groupe de sécurité réseau contient zéro ou plusieurs règles. Pour en savoir plus sur les informations affichées avec les règles, consultez [Vue d’ensemble des groupes de sécurité réseau](security-overview.md).

1. Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez le groupe de sécurité réseau dont vous souhaitez voir les règles.
3. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic entrant** ou **Règles de sécurité de trafic sortant**.

La liste contient toutes les règles que vous avez créées et le groupe de sécurité réseau [Règles de sécurité par défaut](security-overview.md#default-security-rules).

**Commandes**

- Azure CLI : [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell : [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Voir les détails d’une règle de sécurité

1. Dans la zone de recherche située en haut du portail, entrez *groupes de sécurité réseau*. Quand la mention **groupes de sécurité réseau** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez le groupe de sécurité réseau dont vous souhaitez voir les détails d’une règle de sécurité.
3. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic entrant** ou **Règles de sécurité de trafic sortant**.
4. Sélectionnez la règle dont vous souhaitez voir les détails. Pour obtenir une explication détaillée de tous les paramètres, consultez les [paramètres de règle de sécurité](#security-rule-settings).

**Commandes**

- Azure CLI : [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell : [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Changer une règle de sécurité

1. Effectuez les étapes indiquées dans [Voir les détails d’une règle de sécurité](#view-details-of-a-security-rule).
2. Changez les paramètres comme vous le souhaitez, puis sélectionnez **Enregistrer**. Pour obtenir une explication détaillée de tous les paramètres, consultez les [paramètres de règle de sécurité](#security-rule-settings).

**Commandes**

- Azure CLI : [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell : [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Supprimer une règle de sécurité

1. Effectuez les étapes indiquées dans [Voir les détails d’une règle de sécurité](#view-details-of-a-security-rule).
2. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

**Commandes**

- Azure CLI : [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell : [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Utiliser des groupes de sécurité d’application

Un groupe de sécurité d’application contient zéro interface réseau, ou plus. Pour en savoir plus, consultez [Groupes de sécurité d’application](security-overview.md#application-security-groups). Toutes les interfaces réseau dans un groupe de sécurité d’application doivent exister dans le même réseau virtuel. Pour savoir comment ajouter une interface réseau à un groupe de sécurité d’application, consultez [Ajouter une interface réseau à un groupe de sécurité d’application](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Créer un groupe de sécurité d’application

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Dans le champ **Rechercher dans la Place de marché**, entrez *Groupe de sécurité d’application*. Lorsque **Groupe de sécurité d’application** apparaît dans les résultats de recherche, sélectionnez cette entrée. Cliquez à nouveau sur **Groupe de sécurité d’application** sous **Tout**, puis sélectionnez **Créer**.
3. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Créer** :

    | Paramètre        | Valeur                                                   |
    | ---            | ---                                                     |
    | Nom           | Le nom doit être unique au sein d’un groupe de ressources.        |
    | Abonnement   | Sélectionnez votre abonnement.                               |
    | Resource group | Sélectionnez un groupe de ressources existant ou créez-en un. |
    | Emplacement       | Sélectionner un emplacement                                       |

**Commandes**

- Azure CLI : [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell : [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Voir tous les groupes de sécurité d’application

1. En haut à gauche du portail Azure, sélectionnez **Tous les services**.
2. Entrez *Groupes de sécurité d’application* dans la zone **Filtre Tous les services**, puis sélectionnez **Groupes de sécurité d’application** quand il apparaît dans les résultats de recherche.

**Commandes**

- Azure CLI : [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell : [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Voir les détails d’un groupe de sécurité d’application spécifique

1. En haut à gauche du portail Azure, sélectionnez **Tous les services**.
2. Entrez *Groupes de sécurité d’application* dans la zone **Filtre Tous les services**, puis sélectionnez **Groupes de sécurité d’application** quand il apparaît dans les résultats de recherche.
3. Sélectionnez le groupe de sécurité d’application dont vous souhaitez afficher les détails.

**Commandes**

- Azure CLI : [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell : [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Changer un groupe de sécurité d’application

1. En haut à gauche du portail Azure, sélectionnez **Tous les services**.
2. Entrez *Groupes de sécurité d’application* dans la zone **Filtre Tous les services**, puis sélectionnez **Groupes de sécurité d’application** quand il apparaît dans les résultats de recherche.
3. Sélectionnez le groupe de sécurité d’application dont vous souhaitez modifier les paramètres. Vous pouvez ajouter ou supprimer des balises, ou affecter ou supprimer des autorisations pour le groupe de sécurité d’application.

- Azure CLI : [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell : aucune applet de commande PowerShell.

### <a name="delete-an-application-security-group"></a>Supprimer un groupe de sécurité d’application

Vous ne pouvez pas supprimer un groupe de sécurité d’application s’il contient des interfaces réseau. Supprimez toutes les interfaces réseau du groupe de sécurité d’application en changeant les paramètres d’interface réseau ou en supprimant les interfaces réseau. Pour plus d’informations, consultez [Ajouter une interface réseau à des groupes de sécurité d’application ou en supprimer de ces derniers](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) ou [Supprimer une interface réseau](virtual-network-network-interface.md#delete-a-network-interface).

1. En haut à gauche du portail Azure, sélectionnez **Tous les services**.
2. Entrez *Groupes de sécurité d’application* dans la zone **Filtre Tous les services**, puis sélectionnez **Groupes de sécurité d’application** quand il apparaît dans les résultats de recherche.
3. Sélectionnez le groupe de sécurité d’application que vous souhaitez supprimer.
4. Sélectionnez **Supprimer**, puis sélectionnez **Oui** pour supprimer le groupe de sécurité d’application.

**Commandes**

- Azure CLI : [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell : [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Autorisations

Pour exécuter des tâches sur des groupes de sécurité réseau, des règles de sécurité et des groupes de sécurité d’application, votre compte doit posséder le rôle de [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées qui sont répertoriées dans les tableaux suivants :

### <a name="network-security-group"></a>Groupe de sécurité réseau

| Action                                                        |   Nom                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Obtenir un groupe de sécurité réseau                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Créer ou mettre à jour un groupe de sécurité réseau                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Supprimer un groupe de sécurité réseau                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Associez un groupe de sécurité réseau à un sous-réseau ou à une interface réseau 


### <a name="network-security-group-rule"></a>Règle de groupe de sécurité réseau

| Action                                                        |   Nom                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Obtenir une règle                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Créer ou mettre à jour une règle                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Supprimer une règle                                                         |

### <a name="application-security-group"></a>Groupe de sécurité d’application

| Action                                                                     | Nom                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Joindre une configuration IP aux groupes de sécurité d’application|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Joindre une règle de sécurité aux groupes de sécurité d’application    |
| Microsoft.Network/applicationSecurityGroups/read                           | Obtenir un groupe de sécurité d’application                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Créer ou mettre à jour un groupe de sécurité d’application           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Supprimer un groupe de sécurité d’application                     |

## <a name="next-steps"></a>Étapes suivantes

- Créez un réseau ou un groupe de sécurité d’application avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou à l’aide des[modèles Azure Resource Manager](template-samples.md)
- Créer et appliquer une [stratégie Azure](policy-samples.md) pour des réseaux virtuels
