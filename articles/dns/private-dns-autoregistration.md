---
title: Qu’est-ce que la fonctionnalité d’inscription automatique dans les zones privées Azure DNS ?
description: Vue d’ensemble de l’inscription automatique dans les zones privées Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: 4357d3bc347d000ebcdd7c2a9a01069eb64a9343
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003796"
---
# <a name="what-is-the-auto-registration-feature-in-azure-dns-private-zones"></a>Qu’est-ce que la fonctionnalité d’inscription automatique dans les zones privées Azure DNS ?

La fonctionnalité d’inscription automatique des zones privées Azure DNS gère les enregistrements DNS pour les machines virtuelles déployées dans un réseau virtuel. Lorsque vous [liez un réseau virtuel](./private-dns-virtual-network-links.md) à une zone DNS privée avec ce paramètre activé, un enregistrement DNS est créé pour chaque machine virtuelle déployée dans le réseau virtuel. 

Pour chaque machine virtuelle, un enregistrement A et un enregistrement PTR sont créés. Les enregistrements DNS pour les machines virtuelles nouvellement déployées sont également créés automatiquement dans la zone DNS privée liée. Quand une machine virtuelle est supprimée, tous les enregistrements DNS associés sont également supprimés de la zone DNS privée.

Pour activer l’inscription automatique, activez la case à cocher Activer l’inscription automatique lors de la création de la liaison de réseau virtuel.

:::image type="content" source="./media/privatedns-concepts/enable-autoregistration.png" alt-text="Capture d’écran de la page Activer l’inscription automatique lors de l’ajout d’une liaison de réseau virtuel.":::

## <a name="restrictions"></a>Restrictions

* L’inscription automatique est possible uniquement pour les machines virtuelles. Pour toutes les autres ressources, comme les équilibreurs de charge internes, vous pouvez créer manuellement des enregistrements DNS dans la zone DNS privée qui est liée au réseau virtuel.
* Les enregistrements DNS sont créés automatiquement uniquement pour la carte réseau principale des machines virtuelles. Si vos machines virtuelles ont plusieurs cartes réseau, vous pouvez créer manuellement les enregistrements DNS pour les autres interfaces réseau.
* Les enregistrements DNS sont créés automatiquement uniquement si la carte réseau principale des machines virtuelles utilise le protocole DHCP. Si vous utilisez des adresses IP statiques, comme une configuration avec [plusieurs adresses IP dans Azure](../virtual-network/virtual-network-multiple-ip-addresses-portal.md#os-config), l’inscription automatique ne crée pas d’enregistrements pour cette machine virtuelle.
* L’inscription automatique pour IPv6 (enregistrements AAAA) n’est pas prise en charge.
* Un réseau virtuel spécifique ne peut être lié qu’à une seule zone DNS privée lorsque l’inscription automatique au DNS de la machine virtuelle est activée. Vous pouvez toutefois lier plusieurs réseaux virtuels à une seule zone DNS.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une zone privée dans Azure DNS à l’aide d’[Azure PowerShell](./private-dns-getstarted-powershell.md) ou d’[Azure CLI](./private-dns-getstarted-cli.md).

* Passez en revue certains [scénarios de zones privées](./private-dns-scenarios.md) courants qui peuvent être réalisés avec des zones privées dans Azure DNS.

* Pour trouver des réponses aux questions fréquemment posées concernant les zones privées dans Azure DNS, notamment le comportement spécifique auquel vous attendre pour certains types d’opérations, voir le [FAQ sur les DNS privés](./dns-faq-private.yml).
