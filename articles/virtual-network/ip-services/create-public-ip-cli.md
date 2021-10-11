---
title: 'Démarrage rapide : Créer une IP publique – Azure CLI'
titleSuffix: Azure Virtual Network
description: Apprendre à créer une IP publique à l’aide de l’interface Azure CLI
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 8d8b1525c910592cb9cc35ae1e08da7e9e24e30b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369050"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-cli"></a>Démarrage rapide : Créer une adresse IP publique à l’aide de l’interface Azure CLI

Dans ce guide de démarrage rapide, vous allez apprendre à créer une adresse IP publique Azure. Les adresses IP publiques dans Azure sont utilisées pour les connexions publiques aux ressources Azure. Les adresses IP publiques sont disponibles dans deux SKU : de base et standard. Deux niveaux d’adresses IP publiques sont disponibles : régional et mondial. La préférence de routage d’une adresse IP publique est définie lors de sa création. Le routage Internet et le routage Réseau Microsoft sont les choix disponibles.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

À l’aide de la commande [az group create](/cli/azure/group#az_group_create), créez un groupe de ressources nommé **QuickStartCreateIP-rg** dans l’emplacement **eastus2**.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIP-rg \
    --location eastus2
```

# <a name="standard-sku"></a>[**Référence Standard**](#tab/create-public-ip-standard)

>[!NOTE]
>L’adresse IP publique de SKU standard est recommandée pour les charges de travail de production.  Pour plus d’informations sur les SKU, consultez **[Adresses IP publiques](public-ip-addresses.md)** .
>
>La commande suivante est disponible pour l’API version **2020-08-01** ou **ultérieure**.  Pour plus d’informations sur la version de l’API actuellement utilisée, consultez [Fournisseurs et types de ressources](../../azure-resource-manager/management/resource-providers-and-types.md).

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une adresse IPv4 publique redondante interzone standard nommée **myStandardPublicIP** dans **QuickStartCreateIP-rg**.  

Pour créer une adresse IPv6, changez la valeur du paramètre **`--version`** pour **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> Avec des versions de l’API antérieures à 2020-08-01, exécutez la commande sans spécifier de paramètre **`--zone`** pour créer une adresse IP redondante interzone. 
>

# <a name="basic-sku"></a>[**Référence De base**](#tab/create-public-ip-basic)

Dans cette section, vous allez créer une IP de base. Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité.

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une adresse IPv4 publique statique de base, nommée **myBasicPublicIP** dans **QuickStartCreateIP-rg**.

Pour créer une adresse IPv6, changez la valeur du paramètre **`--version`** pour **IPv6**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
S’il est acceptable que l’adresse IP change au fil du temps, l’affectation **dynamique** d’adresses IP peut être sélectionnée en changeant la valeur de **`--allocation-method`** pour **Dynamique**. 

>[!NOTE]
> Une adresse IPv6 de base doit toujours être « dynamique ».

---

## <a name="create-a-zonal-or-no-zone-ip-address"></a>Créer une adresse IP zonale ou non zonale

Dans cette section, vous allez apprendre à créer une adresse IP publique zonale ou dépourvue de zone.

# <a name="zonal"></a>[**Zonal**](#tab/create-public-ip-zonal)

Pour créer une adresse IPv4 publique zonale standard à l’intérieur de la zone 2, nommée **myStandardPublicIP** dans **QuickStartCreateIP-rg**, exécutez la commande suivante.

Pour créer une adresse IPv6, changez la valeur du paramètre **`--version`** pour **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rgLB \
    --name myStandardPublicIP-zonal \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal"></a>[**Non zonale**](#tab/create-public-ip-non-zonal)

Dans cette section, vous allez créer une adresse IP non zonale.  

>[!NOTE]
>La commande suivante est disponible pour l’API version 2020-08-01 ou ultérieure.  Pour plus d’informations sur la version de l’API actuellement utilisée, consultez [Fournisseurs et types de ressources](../../azure-resource-manager/management/resource-providers-and-types.md).

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une adresse IPv4 publique standard comme ressource non zonale, nommée **myStandardPublicIP-nozone** dans **QuickStartCreateIP-rg**. 

Pour créer une adresse IPv6, changez la valeur du paramètre **`--version`** pour **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-nozone \
    --version IPv4 \
    --sku Standard
```
La suppression du paramètre **`--zone`** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **`--zone`** constitue la sélection par défaut pour les adresses IP publiques standard dans les régions sans [Zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="routing-preference-and-tier"></a>Préférence de routage et niveau

Les adresses IPv4 publiques statiques de référence SKU standard prennent en charge la préférence de routage ou la fonctionnalité Niveau global.

# <a name="routing-preference"></a>[**Préférence de routage**](#tab/routing-preference)

Par défaut, la préférence de routage pour les adresses IP publiques est définie sur le « Réseau Microsoft », qui remet le trafic à l’utilisateur via le réseau étendu mondial de Microsoft.  

La sélection de **Internet** réduit les déplacements sur le réseau de Microsoft en utilisant à la place le réseau du fournisseur de services Internet pour acheminer le trafic à un coût optimisé.  

Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](routing-preference-overview.md).

La commande crée une nouvelle adresse IPv4 publique redondante interzone standard avec une préférence de routage de type **Internet** :

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

# <a name="tier"></a>[**Niveau**](#tab/tier)

Les adresses IP publiques sont associées à une seule région. Le niveau **Global** s’étend sur une adresse IP dans plusieurs régions. Le niveau **Global** est exigé pour les front-ends des équilibreurs de charge interrégionaux.  

Pour plus d’informations, consultez [Équilibreur de charge inter-région](../../load-balancer/cross-region-overview.md).

La commande suivante crée une adresse IPv4 globale. Cette adresse peut être associée au serveur front-end d’un équilibreur de charge inter-région.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>Les adresses du niveau Global ne prennent pas en charge les zones de disponibilité.

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez l’adresse IP publique en suivant ces étapes :

1. Dans la zone de recherche située en haut du portail, entrez **Groupe de ressources**.

2. Dans les résultats de la recherche, sélectionnez **Groupes de ressources**.

3. Sélectionnez **QuickStartCreateIP-rg**.

4. Sélectionnez **Supprimer le groupe de ressources**.

5. Indiquez **QuickStartCreateIP-rg** dans **ENTREZ LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un préfixe d’adresse IP publique :
> [!div class="nextstepaction"]
> [Créer un préfixe d’adresse IP publique à l’aide de l’interface Azure CLI](create-public-ip-prefix-cli.md)
