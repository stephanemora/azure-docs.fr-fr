---
title: Créer, modifier ou supprimer une homologation de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment créer, modifier ou supprimer une homologation de réseau virtuel.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial;anavin
ms.openlocfilehash: 11726b274d72f263ff3defeb7eb7b80594681e15
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077375"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Créer, modifier ou supprimer une homologation de réseau virtuel

Découvrez comment créer, modifier ou supprimer une homologation de réseau virtuel. L’appairage de réseaux virtuels vous permet de connecter des réseaux virtuels via le réseau principal Azure. Une fois appairés, les réseaux virtuels continuent d’être gérés comme des ressources distinctes. Si vous découvrez l’appairage de réseaux virtuels, vous trouverez plus d’informations à ce sujet dans l’article [Vue d’ensemble de l’appairage de réseaux virtuels](virtual-network-peering-overview.md) ou en suivant un [tutoriel](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com et connectez-vous avec un compte qui possède les [autorisations nécessaires](#permissions) pour utiliser les homologations.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce tutoriel requiert le module Azure PowerShell version 5.7.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell localement, vous devez également exécuter `Connect-AzureRmAccount` avec un compte qui possède les [autorisations nécessaires](#permissions) pour utiliser les homologations, afin de vous connecter à Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel requiert Azure CLI version 2.0.31 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` avec un compte qui possède les [autorisations nécessaires](#permissions) pour utiliser les homologations, afin de vous connecter à Azure.

Le compte auquel vous vous connectez, ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou avoir un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) assigné aux actions appropriées répertoriées dans [Autorisations](#permissions).

## <a name="create-a-peering"></a>Créer une homologation

Avant de créer une homologation, familiarisez-vous avec les [exigences et contraintes](#requirements-and-contstraints) ainsi qu’avec les [autorisations nécessaires](#permissions).

1. Dans la zone de recherche située en haut du Portail Azure, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel pour lequel vous souhaitez créer une homologation.
3. Dans la liste des réseaux virtuels, sélectionnez le réseau virtuel pour lequel vous souhaitez créer une homologation.
4. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
5. Sélectionnez **Ajouter**. 
6. <a name="add-peering"></a> :
    - **Nom :** le nom doit être unique au sein du réseau virtuel.
    - **Modèle de déploiement de réseau virtuel :** sélectionnez le modèle de déploiement via lequel le réseau virtuel avec lequel vous souhaitez opérer l’homologation a été déployé.
    - **Je connais mon ID de ressource :** si vous avez accès en lecture au réseau virtuel avec lequel vous souhaitez opérer l’homologation, laissez cette case à cocher désactivée. Si vous n’avez pas accès en lecture au réseau virtuel ou à l’abonnement avec lequel vous souhaitez opérer l’homologation, activez cette case à cocher. Dans la zone **ID de ressource** qui s’affiche lorsque vous cochez la case, entrez l’ID de ressource complet du réseau virtuel avec lequel vous souhaitez opérer l’homologation. L’ID de ressource que vous entrez doit être celui d’un réseau virtuel existant dans la même [région](https://azure.microsoft.com/regions) Azure que ce réseau virtuel ou dans une [autre région Azure prise en charge](#requirements-and-constraints). L’ID de ressource complet ressemble à /subscriptions/<Id>/resourceGroups/<nom-du-groupe-de-ressources>/providers/Microsoft.Network/virtualNetworks/<nom-du-réseau-virtuel>. Vous pouvez obtenir l’ID de ressource d’un réseau virtuel en affichant les propriétés de celui-ci. Pour savoir comment afficher les propriétés d’un réseau virtuel, consultez [Gérer des réseaux virtuels](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Abonnement :** sélectionnez l’[abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) du réseau virtuel avec lequel vous souhaitez opérer l’homologation. Un ou plusieurs abonnements sont répertoriés, selon le nombre d’abonnements auxquels votre compte a accès en lecture. Si vous avez activé la case à cocher **ID de ressource**, ce paramètre n’est pas disponible.
    - **Réseau virtuel :** sélectionnez le réseau virtuel avec lequel vous voulez opérer l’homologation. Vous pouvez sélectionner un réseau virtuel créé à l’aide d’un modèle de déploiement d’Azure. Si vous souhaitez sélectionner un réseau virtuel dans une autre région, vous devez le choisir dans une [région prise en charge](#cross-region). Pour que le réseau virtuel apparaisse dans la liste, vous devez disposer de l’accès en lecture à celui-ci. Si un réseau virtuel est répertorié, mais grisé, ce peut être parce que l’espace d’adressage du réseau virtuel chevauche l’espace d’adressage de ce réseau. Si les espaces d’adressage de réseaux virtuels se chevauchent, il n’est pas possible d’homologuer ces réseaux. Si vous avez activé la case à cocher **ID de ressource**, ce paramètre n’est pas disponible.
    - **Autoriser l’accès au réseau virtuel :** sélectionnez **Activé** (par défaut) si vous souhaitez activer la communication entre les deux réseaux virtuels. L’activation de la communication entre les réseaux virtuels permet aux ressources connectées à ceux-ci de communiquer entre elles avec les mêmes bande passante et latence que si elles étaient connectées au même réseau virtuel. Toute communication entre les ressources des deux réseaux virtuels passe par le réseau privé Azure. La balise par défaut **VirtualNetwork** pour les groupes de sécurité réseau englobe le réseau virtuel et le réseau virtuel homologué. Pour en savoir plus sur les groupes de sécurité réseau, lisez l’article présentant les [Groupes de sécurité réseau](virtual-networks-nsg.md#default-tags).  Si vous ne souhaitez pas que le trafic soit dirigé vers le réseau virtuel homologué, sélectionnez **Désactivé**. Vous pouvez sélectionner **Désactivé** si vous avez homologué un réseau virtuel avec un autre réseau virtuel, mais souhaitez occasionnellement désactiver le flux de trafic entre les deux réseaux virtuels. Il se peut que vous trouviez plus pratique d’activer ou de désactiver les homologations que de les supprimer et recréer. Lorsque ce paramètre est désactivé, le trafic ne passe plus entre les réseaux virtuels homologues.
    - **Autoriser le trafic transféré :** Cochez cette case pour autoriser l’acheminement du trafic *transféré* par une appliance virtuelle réseau dans un réseau virtuel (ne provenant pas du réseau virtuel) vers ce réseau virtuel par le biais d’un appairage. Par exemple, considérez trois réseaux virtuels nommés Spoke1, Spoke2 et Hub. Une homologation existe entre chaque réseau virtuel spoke et le réseau virtuel Hub, mais les homologations n’existent pas entre les réseaux virtuels spoke. Une appliance virtuelle réseau est déployée dans le réseau virtuel Hub et des itinéraires définis par l’utilisateur sont appliqués à chaque réseau virtuel spoke acheminant le trafic entre les sous-réseaux via l’appliance virtuelle réseau. Si cette case à cocher n’est pas cochée pour l’homologation entre chaque réseau virtuel spoke et le réseau virtuel Hub, le trafic ne passe pas entre les réseaux virtuels spoke, car le Hub transfère le trafic entre les réseaux virtuels. Si l’activation de cette fonctionnalité autorise le transfert du trafic via l’homologation, elle n’a pas pour effet de créer des itinéraires définis par l’utilisateur ou des appliances virtuelles. Les itinéraires définis par l’utilisateur et les appliances virtuelles sont créés séparément. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Il est inutile de cocher ce paramètre si le trafic est transféré entre les réseaux virtuels par le biais d’une passerelle VPN Azure.
    -   **:** cochez cette case si vous avez une passerelle de réseau virtuel attachée à ce réseau virtuel, et souhaitez autoriser le trafic en provenance du réseau virtuel homologué via la passerelle. Par exemple, ce réseau virtuel peut être attaché à un réseau local via une passerelle de réseau virtuel. La passerelle peut être une passerelle ExpressRoute ou VPN. L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance du réseau virtuel homologué passe par la passerelle attachée à ce réseau virtuel vers le réseau local. Si vous cochez cette case, le réseau virtuel homologué ne peut pas avoir de passerelle configurée. La case à cocher **Utiliser des passerelles distantes** doit être activée pour le réseau virtuel homologué lors de la configuration de l’homologation de l’autre réseau virtuel à ce réseau virtuel. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel homologué continue d’être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel. Si l’appairage est entre un réseau virtuel (Resource Manager) et un réseau virtuel (classique), la passerelle doit se trouver dans le réseau virtuel (Resource Manager). Vous ne pouvez pas activer cette option si vous homologuez des réseaux virtuels dans des régions différentes.
    
        En plus de transférer le trafic vers un réseau local, une passerelle VPN peut transférer le trafic réseau entre les réseaux virtuels qui sont homologués avec le réseau virtuel dans lequel la passerelle se trouve, sans que les réseaux virtuels n’aient besoin d’être homologués entre eux. L’utilisation d’une passerelle VPN pour le transfert du trafic est utile lorsque vous souhaitez utiliser une passerelle VPN dans un réseau virtuel Hub (consultez l’exemple de hub-and-spoke décrit pour **Autoriser le trafic transféré**) afin d’acheminer le trafic entre des réseaux virtuels spoke qui ne sont pas appairés. Pour en savoir plus sur l’autorisation d’utilisation d’une passerelle pour le transit, consultez [Configurer une passerelle VPN pour le transit dans un appairage de réseaux virtuels](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ce scénario nécessite l’implémentation d’itinéraires définis par l’utilisateur qui spécifient la passerelle de réseau virtuel en tant que type de tronçon suivant. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Vous ne pouvez spécifier une passerelle VPN en tant que type de tronçon suivant uniquement dans un itinéraire défini par l’utilisateur, vous ne pouvez pas spécifier une passerelle ExpressRoute en tant que type de tronçon suivant dans un itinéraire défini par l’utilisateur. Vous ne pouvez pas activer cette option si vous homologuez des réseaux virtuels dans des régions différentes.

    -   **:** cochez cette case pour autoriser que le trafic en provenance de ce réseau virtuel passe par une passerelle de réseau virtuel attachée au réseau virtuel avec lequel vous opérez l’homologation. Par exemple, le réseau virtuel avec lequel vous opérez l’homologation a une passerelle VPN attachée qui permet la communication avec un réseau local.  L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance de ce réseau virtuel passe par la passerelle VPN attachée au réseau virtuel homologué. Si vous cochez cette case, le réseau virtuel homologué doit avoir une passerelle de réseau virtuel attachée, et la case à cocher **Autoriser le transit par passerelle** doit être activée pour ce réseau. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel homologué peut toujours être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel. 
    Une seule homologation pour ce réseau virtuel peut avoir ce paramètre activé.

        Vous ne pouvez pas utiliser de passerelles distantes si vous avez déjà configuré une passerelle dans votre réseau virtuel. Vous ne pouvez pas activer cette option si vous homologuez des réseaux virtuels dans des régions différentes. Pour en savoir plus sur l’utilisation d’une passerelle pour le transit, consultez [Configurer une passerelle VPN pour le transit dans un appairage de réseaux virtuels](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

7. Pour ajouter l’homologation au réseau virtuel que vous avez sélectionné, cliquez sur **OK**.

Pour obtenir des instructions pas à pas sur l’implémentation de l’homologation entre des réseaux virtuels dans différents abonnements et modèles de déploiement, consultez la section [Étapes suivantes](#next-steps). 


### <a name="commands"></a>Commandes

- **Azure CLI** : [az network vnet peering create](/cli/azure/network/vnet/peering#create)
- **PowerShell** : [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Afficher ou modifier les paramètres d’homologation

Avant de modifier une homologation, familiarisez-vous avec les [exigences et contraintes](#requirements-and-contstraints) ainsi qu’avec les [autorisations nécessaires](#permissions).

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel dont vous souhaitez modifier les paramètres d’homologation.
3. Dans la liste des réseaux virtuels, sélectionnez le réseau dont vous souhaitez modifier les paramètres d’homologation.
4. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
5. Sélectionnez l’homologation que vous souhaitez afficher ou dont vous voulez modifier les paramètres.
6. Modifiez le paramètre approprié. Lisez les options de chaque paramètres de l’[étape 6](#add-peering) de Créer une homologation. 
7. Sélectionnez **Enregistrer**.

**Commandes**

- **Azure CLI** : [az network vnet peering list](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) pour afficher la liste des homologations d’un réseau virtuel, [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) pour afficher les paramètres d’une homologation spécifique et [az network vnet peering update](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) pour modifier les paramètres de l’homologation.|
- **PowerShell** : [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) pour afficher les paramètres de l’homologation et [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) pour en modifier les paramètres.

## <a name="delete-a-peering"></a>Supprimer une homologation

Avant de supprimer une homologation, vérifiez que votre compte possède les [autorisations nécessaires](#permissions).

En cas de suppression d’une homologation, le trafic en provenance d’un réseau virtuel n’est plus acheminé vers le réseau virtuel homologué. Lorsque des réseaux virtuels déployés via le Gestionnaire de ressources sont homologués, chaque réseau virtuel a une homologation à l’autre réseau virtuel. Si la suppression de l’homologation d’un réseau virtuel désactive la communication entre les réseaux virtuels, elle ne supprime pas l’homologation de l’autre réseau virtuel. L’état d’homologation pour l’homologation existant dans l’autre réseau virtuel est **Déconnectée**. Vous ne pouvez pas recréer l’homologation tant que vous n’avez pas recréé l’homologation dans le premier réseau virtuel et que l’état d’homologation des deux réseaux virtuels n’est pas *Connectée*. 

Si vous souhaitez que les réseaux virtuels communiquent occasionnellement, au lieu de supprimer une homologation, vous pouvez définir le paramètre **Autoriser l’accès au réseau virtuel** sur **Désactivé**. Pour savoir comment procéder, voir l’étape 6 de la section [Créer une homologation](#create-peering) dans cet article. Il se peut que vous trouviez plus facile de désactiver et activer l’accès réseau que de supprimer et recréer des homologations.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel pour lequel vous souhaitez supprimer une homologation.
3. Dans la liste des réseaux virtuels, sélectionnez le réseau virtuel pour lequel vous souhaitez supprimer une homologation.
4. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
5. Du côté droit de l’homologation que vous souhaitez supprimer, sélectionnez **...**, **Supprimer**, puis **Oui** pour supprimer l’homologation du premier réseau virtuel.
6. Suivez les étapes précédentes pour supprimer l’homologation de l’autre réseau virtuel dans l’homologation.

**Commandes**

- **Azure CLI** : [az network vnet peering delete](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- **PowerShell** : [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Exigences et contraintes 

- Les réseaux virtuels que vous homologuez doivent avoir des espaces d’adressage IP qui ne se chevauchent pas.
- Il n’est pas possible d’ajouter ou de supprimer des plages d’adresses dans l’espace d’adressage d’un réseau virtuel après que celui-ci a été homologué avec un autre réseau virtuel. Pour ajouter ou supprimer des plages d’adresses, supprimez l’homologation, ajoutez ou supprimez les plages d’adresses, puis recréez l’homologation. Pour ajouter ou supprimer des plages d’adresses dans des réseaux virtuels, voir [Gérer les réseaux virtuels](manage-virtual-network.md).
- Vous pouvez homologuer deux réseaux virtuels déployés via le Gestionnaire de ressources, ou homologuer un réseau virtuel déployé via le Gestionnaire de ressources avec un réseau virtuel déployé via le modèle de déploiement classique. Vous ne pouvez pas homologuer deux réseaux virtuels créés via le modèle de déploiement classique. Si vous n’êtes pas familiarisé avec les modèles de déploiement Azure, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour connecter deux réseaux virtuels créés via le modèle de déploiement classique.
- Lors de l’homologation de deux réseaux virtuels créés via le Gestionnaire de ressources, une homologation doit être configurée pour chaque réseau virtuel dans l’homologation. L’un des états suivants s’affiche pour l’homologation : 
    - *Lancé* : lorsque vous créez l’appairage au deuxième réseau virtuel à partir du premier réseau virtuel, l’état d’appairage est *Lancé*. 
    - *Connecté* : lorsque vous créez l’appairage à partir du deuxième réseau virtuel au premier réseau virtuel, l’état d’appairage est *Connecté*. Si vous affichez l’état d’homologation pour le premier réseau virtuel, vous voyez que son état est passé de *Initiée* à *Connectée*. L’homologation n’est pas correctement établie tant que l’état d’homologation pour les deux homologations de réseau virtuel est *Connectée*.
- Lors de l’homologation d’un réseau virtuel créé via le Gestionnaire de ressources avec un réseau virtuel créé via le modèle de déploiement classique, vous configurez une homologation uniquement pour le réseau virtuel est déployé via le Gestionnaire de ressources. Vous ne pouvez pas configurer d’homologation pour un réseau virtuel (classique), ou entre deux réseaux virtuels déployés via le modèle de déploiement classique. Lorsque vous créez l’homologation à partir du réseau virtuel (Gestionnaire des ressources) au réseau virtuel (classique), l’état d’homologation est *Mise à jour*, puis passe rapidement à *Connectée*.
- Une homologation est établie entre deux réseaux virtuels. Les homologations ne sont pas transitives. Imaginons que vous créez des homologations entre :
    - VirtualNetwork1 et VirtualNetwork2
    - VirtualNetwork2 et VirtualNetwork3

  Il n’existe aucune homologation entre VirtualNetwork1 et VirtualNetwork3 via VirtualNetwork2. Si vous souhaitez créer une homologation de réseaux virtuels entre VirtualNetwork1 et VirtualNetwork3, vous devez créer une homologation entre VirtualNetwork1 et VirtualNetwork3.
- Vous ne pouvez pas résoudre des noms dans des réseaux virtuels homologués en utilisant une résolution de noms Azure par défaut. Pour résoudre des noms dans d’autres réseaux virtuels, vous devez utiliser [Azure DNS pour les domaines privés](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou un serveur DNS personnalisé. Pour savoir comment configurer votre propre serveur DNS, consultez [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Les ressources des réseaux virtuels homologués dans la même région peuvent communiquer entre elles avec les mêmes bande passante et latence que si elles étaient dans le même réseau virtuel. Toutefois, chaque taille de machine virtuelle a sa propre bande passante réseau maximale. Pour en savoir plus sur la bande passante réseau maximale pour différentes tailles de machine virtuelle, consultez les tailles de machine virtuelle pour [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Les réseaux virtuels peuvent être dans des abonnements identiques ou différents. Quand les réseaux virtuels sont dans des abonnements différents, les deux abonnements doivent être associés au même locataire Azure Active Directory. Si vous n’avez pas encore de client Active Directory, vous pouvez rapidement en [créer un](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour vous connecter à deux réseaux virtuels situés dans des abonnements différents qui sont associés à des locataires Active Directory différents.
- Un réseau virtuel peut être homologué ainsi qu’être connecté à un autre réseau virtuel avec une passerelle de réseau virtuel Azure. Lorsque des réseaux virtuels sont connectés via une homologation et une passerelle, le trafic entre les réseaux virtuels passe par l’homologation plutôt que par la passerelle.
- Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une homologation de réseau virtuel. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/virtual-network).
* <a name="cross-region"></a>Vous pouvez appairer des réseaux virtuels dans la même région ou dans différentes régions. Les contraintes suivantes ne s’appliquent pas quand les deux réseaux virtuels sont dans la *même* région, mais elles s’appliquent quand les réseaux virtuels sont appairés à l’échelle mondiale : 
    - Les réseaux virtuels peuvent se trouver dans n’importe quelle région de clouds publics Azure, mais pas dans les clouds nationaux Azure.
    - Les ressources situées dans un réseau virtuel ne peuvent pas communiquer avec l’adresse IP d’un équilibreur de charge interne Azure dans le réseau virtuel homologué. L’équilibreur de charge et les ressources qui communiquent avec lui doivent être dans le même réseau virtuel.
    - Vous ne pouvez pas utiliser de passerelles distantes ni autoriser de transit via une passerelle. Pour utiliser des passerelles distantes ou autoriser un transit via une passerelle, les deux réseaux virtuels homologués doivent appartenir à la même région. 

## <a name="permissions"></a>Autorisations

Les comptes pouvant être utilisés avec l’appairage de réseaux virtuels doivent avoir les rôles suivants :

- [Contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) : pour un réseau virtuel déployé par le biais de Resource Manager.
- [Contributeur de réseaux classiques](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor) : pour un réseau virtuel déployé par le biais du modèle de déploiement classique.

Si votre compte n’a pas l’un des rôles ci-dessus, il doit avoir un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) auquel sont assignées les actions appropriées répertoriées dans le tableau suivant :

| Action | NOM |
|---|---|
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Action requise pour créer un appairage entre un réseau virtuel A et un réseau virtuel B. Le réseau virtuel A doit être un réseau virtuel (Resource Manager)                            |
| Microsoft.Network/virtualNetworks/peer/action                   | Action requise pour créer un appairage entre un réseau virtuel B (Resource Manager) et un réseau virtuel A                                                                                |
| Microsoft.ClassicNetwork/virtualNetworks/peer                   | Action requise pour créer un appairage entre un réseau virtuel B (classique) et un réseau virtuel A                                                                                    |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Action requise pour lire un appairage de réseaux virtuels   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Action requise pour supprimer un appairage de réseaux virtuels |

## <a name="next-steps"></a>Étapes suivantes

* Une homologation est générée entre des réseaux virtuels créés via des modèles de déploiement identiques ou différents qui existent dans des abonnements identiques ou différents. Suivez un didacticiel pour l’un des scénarios suivants :

    |Modèle de déploiement Azure             | Abonnement  |
    |---------                          |---------|
    |Les deux modèles Resource Manager              |[Identique](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Différent](create-peering-different-subscriptions.md)|
    |Un modèle Resource Manager, un modèle classique  |[Identique](create-peering-different-deployment-models.md)|
    |                                   |[Différent](create-peering-different-deployment-models-subscriptions.md)|

* Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Créez un appairage de réseaux virtuels avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou à l’aide des [modèles Azure Resource Manager](template-samples.md)
* Créez et appliquez une [stratégie Azure](policy-samples.md) pour des réseaux virtuels
