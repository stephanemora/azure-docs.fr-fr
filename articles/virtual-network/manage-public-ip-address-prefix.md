---
title: Créer, changer ou supprimer un préfixe d’adresse IP publique Azure | Microsoft Docs
description: Découvrez comment créer, changer ou supprimer un préfixe d’adresse IP publique.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 71dee9f36a3e8e11cc23f966c9157f6409a3b5e0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405731"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Créer, changer ou supprimer un préfixe d’adresse IP publique

Découvrez les préfixes d’adresse IP publique et comment en créer, changer et supprimer un. Un préfixe d’adresse IP publique est une plage d’adresses contiguë basée sur le nombre d’adresses IP publiques spécifiées. Les adresses sont attribuées à votre abonnement. Quand vous créez une ressource d’adresse IP publique, vous pouvez attribuer une adresse IP publique statique à partir du préfixe et l’associer à des machines virtuelles, des équilibreurs de charge ou d’autres ressources afin d’activer la connectivité Internet. Si vous n’êtes pas familiarisé avec les préfixes d’adresse IP publique, consultez [Vue d’ensemble des préfixes d’adresse IP publique](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Avant de commencer

> [!IMPORTANT]
> Le préfixe d’adresse IP publique est en préversion publique dans un nombre limité de régions. Vous pouvez [découvrir ce que signifie l’état de préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Le préfixe d’adresse IP publique est actuellement disponible dans les régions : USA Centre-Ouest, USA Ouest, USA Ouest 2, USA Centre, Europe Nord, Europe Ouest et Asie Sud-Est. Pour obtenir la liste actualisée des régions, visitez [Mises à jour Azure](https://azure.microsoft.com/updates/?product=virtual-network).

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://aka.ms/publicipprefixportal, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce tutoriel nécessite la version 6.3.1 ou ultérieure du module PowerShell AzureRm.Network. Exécutez `Get-Module -ListAvailable AzureRM.Network` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/AzureRm.Network.6.3.1). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel nécessite Azure CLI 2.0.41 ou version ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

Le compte auquel vous vous connectez, ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans [Autorisations](#permissions).

Les préfixes d’adresse IP publique ont un coût. Pour en savoir plus, consultez les [tarifs](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Créer un préfixe d’adresse IP publique

1. Dans l’angle supérieur gauche du portail, sélectionnez **+ Créer une ressource**.
2. Entrez *préfixe d’adresse IP publique* dans la zone *Rechercher dans la Place de marché*. Quand **Préfixe d’adresse IP publique** s’affiche dans les résultats de recherche, sélectionnez-le.
3. Sous **Préfixe d’adresse IP publique**, sélectionnez **Créer**.
4. Entrez ou sélectionnez des valeurs pour les paramètres suivants et, sous **Créer un préfixe d’adresse IP publique**, sélectionnez **Créer** :

   |Paramètre|Requis ?|Détails|
   |---|---|---|
   |Abonnement|Oui|Doit exister dans le même [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |Groupe de ressources|Oui|Peut exister dans un [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) identique ou différent de celui de la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |NOM|OUI|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné.|
   |Région|Oui|Il doit exister dans la même [région](https://azure.microsoft.com/regions) que les adresses IP publiques auxquelles vous attribuez des adresses de la plage. Le préfixe est actuellement en préversion dans les régions USA Centre-Ouest, USA Ouest, USA Ouest 2, USA Centre, Europe Nord, Europe Ouest et Asie Sud-Est.|
   |Taille de préfixe|Oui| Taille du préfixe dont vous avez besoin. La valeur par défaut est une adresse IP A/28 ou 16. 

**Commandes**


|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-create)|
|PowerShell|[New-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/new-azurermpublicipaddressprefix)|

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Créer une adresse IP publique statique à partir d’un préfixe
Une fois que vous avez créé un préfixe, vous devez créer des adresses IP statiques à partir du préfixe. Pour ce faire, effectuez les étapes ci-dessous.

1. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, tapez *préfixe d’adresse ip publique*. Quand **Préfixes d’adresse IP publique** s’affiche dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez le préfixe à partir duquel créer les adresses IP publiques.
3. Quand il apparaît dans les résultats de recherche, sélectionnez-le et cliquez sur **+Ajouter une adresse IP** dans la section Vue d’ensemble. Si vous ne voyez pas cette option, vérifiez que vous utilisez le bon lien pour la préversion : https://aka.ms/publicipprefixportal
4. Entrez ou sélectionnez les valeurs des paramètres suivants sous **Créer une adresse IP publique**. Comme le préfixe est pour des adresses IP de type Standard, IPv4 et statiques, vous devez uniquement fournir les informations suivantes :

   |Paramètre|Requis ?|Détails|
    |---|---|---|
    |NOM|Oui|Le nom de l’adresse IP publique doit être unique au sein du groupe de ressources sélectionné.|
   |Délai d’inactivité (minutes)|Non |Durée (en minutes) de maintien d’une connexion TCP ou HTTP ouverte sans utiliser les clients pour envoyer des messages keep-alive. |
   |Étiquette du nom DNS|Non |Elle doit être unique dans la région Azure où vous créez le nom (pour tous les abonnements et tous les clients). Azure inscrit automatiquement le nom et l’adresse IP dans son DNS pour que vous puissiez vous connecter à une ressource avec le nom. Azure ajoute un sous-réseau par défaut de type *emplacement.cloudapp.azure.com* (où emplacement est l’emplacement que vous fournissez) pour créer le nom DNS complet. Pour plus d’informations, consultez [Utiliser Azure DNS avec une adresse IP publique Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

## <a name="view-or-delete-a-prefix"></a>Voir ou supprimer un préfixe

1. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, tapez *préfixe d’adresse ip publique*. Quand **Préfixes d’adresse IP publique** s’affiche dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez le nom du préfixe d’adresse IP publique que vous voulez voir, dont vous voulez changer les paramètres ou que vous voulez supprimer de la liste.
3. Choisissez l’une des options suivantes, selon que vous voulez voir le préfixe d’adresse IP publique, le supprimer ou le changer.
    - **Voir** : La section **Vue d’ensemble** montre les paramètres principaux du préfixe d’adresse IP publique, comme le préfixe.
    - **Supprimer** : Pour supprimer le préfixe d’adresse IP publique, sélectionnez **Supprimer** dans la section **Vue d’ensemble**. Si les adresses dans le préfixe sont associées à des ressources d’adresse IP publique, vous devez d’abord supprimer les ressources d’adresse IP publique. Consultez [Supprimer une adresse IP publique](virtual-network-public-ip-address.md#view-change-settings-for-or-delete-a-public-ip-address).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-list) pour lister les adresses IP publiques, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-show) pour montrer les paramètres, [az network public-ip prefix update](/cli/azure/network/public-ip-prefix#az-network-public-ip-prefix-update) pour mettre à jour, [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix/az-network-public-ip-prefix-delete) pour supprimer|
|PowerShell|[Get-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/get-azurermpublicipaddressprefix) pour récupérer un objet d’adresse IP publique et voir ses paramètres, [Set-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/set-azurermpublicipaddressprefix) pour mettre à jour les paramètres, [Remove-AzureRmPublicIpAddressPrefix](/powershell/module/azurerm.network/remove-azurermpublicipaddressprefix) pour supprimer|

## <a name="permissions"></a>Autorisations

Pour effectuer des tâches sur les préfixes d’adresse IP publique, votre compte doit avoir le rôle de [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) associé aux actions appropriées listées dans le tableau suivant :

| Action                                                                   | NOM                                                           |
| ---------                                                                | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Lire un préfixe d’adresse IP publique                                |
| Microsoft.Network/publicIPPrefixes/write                          | Créer ou mettre à jour un préfixe d’adresse IP publique                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Supprimer un préfixe d’adresse IP publique                              |
|Microsoft.Network/publicIPPrefixes/join/action | Créer une adresse IP publique à partir d’un préfixe |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les scénarios et les avantages avec un [préfixe d’adresse IP publique](public-ip-address-prefix.md)
