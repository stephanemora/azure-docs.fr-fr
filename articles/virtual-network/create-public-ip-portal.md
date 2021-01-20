---
title: Créer une adresse IP publique - Portail Azure
description: Découvrez comment créer une adresse IP publique à l’aide du portail Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 02a6e934b517cdd118b6175d9cfef73bee4c996d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223004"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Démarrage rapide : Créer une adresse IP publique à l’aide du portail Azure

Cet article explique comment créer une ressource d’adresse IP publique à l’aide du portail Azure. Pour savoir quelles ressources peuvent être associées, connaître la différence entre les références SKU De base et Standard et obtenir d’autres informations connexes, consultez [Adresses IP publiques](./public-ip-addresses.md).  Cet exemple traite seulement des adresses IPv4. Pour plus d’informations sur les adresses IPv6, consultez [IPv6 pour les réseaux virtuels Azure](./ipv6-overview.md).

# <a name="standard-sku---using-zones"></a>[**Référence SKU Standard - Avec des zones**](#tab/option-create-public-ip-standard-zones)

Effectuez les étapes suivantes pour créer une adresse IP publique redondante interzone standard nommée **myStandardZRPublicIP**.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**. 
3. Dans la zone de recherche, tapez *Adresse IP publique*.
4. Dans les résultats de recherche, sélectionnez **Adresse IP publique**. Ensuite, dans la page **Adresse IP publique**, sélectionnez **Créer**.
5. Dans la page **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4                 |    
    | SKU                     | sélectionnez **Standard**.         |
    | Niveau (si affiché*)                  | Sélectionnez **Régional**         |
    | Nom                    | Entrez *myStandardZRPublicIP*          |
    | Affectation d’adresses IP   | Notez que cette valeur est verrouillée sur « Statique »                                        |
    | Délai d’inactivité (minutes)  | Conservez la valeur 4        |
    | Étiquette du nom DNS          | Conservez la valeur vide    |
    | Abonnement            | Sélectionnez votre abonnement.   |
    | Resource group          | Sélectionnez **Créer**, entrez myResourceGroup et sélectionnez **OK** |
    | Emplacement                | Sélectionnez **USA Est 2**      |
    | Zone de disponibilité       | Sélectionnez **Redondant interzone** ou choisissez une zone spécifique (voir la remarque ci-dessous) |

Notez que ces sélections sont valides uniquement dans des régions avec des [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  (Vous pouvez également sélectionner une zone spécifique dans ces régions, mais elle ne sera pas résiliente aux échecs de zone.)

\* = Le niveau se rapporte à la fonctionnalité [Équilibreur de charge interrégion](../load-balancer/cross-region-overview.md), actuellement en préversion.

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-create-public-ip-basic)

Effectuez les étapes suivantes pour créer une adresse IP publique statique de base nommée **myBasicPublicIP**.  Les adresses IP publiques de base n’intègrent pas le concept de zones de disponibilité.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource**. 
3. Dans la zone de recherche, tapez *Adresse IP publique*.
4. Dans les résultats de recherche, sélectionnez **Adresse IP publique**. Ensuite, dans la page **Adresse IP publique**, sélectionnez **Créer**.
5. Dans la page **Créer une adresse IP publique**, entrez ou sélectionnez les informations suivantes : 

    | Paramètre                 | Valeur                       |
    | ---                     | ---                         |
    | Version de l’adresse IP              | Sélectionnez IPv4                 |    
    | SKU                     | sélectionnez **Standard**.         |
    | Nom                    | Entrez *myBasicPublicIP*          |
    | Affectation d’adresses IP   | Choisissez **Statique** (voir la remarque ci-dessous)                                     |
    | Délai d’inactivité (minutes)  | Conservez la valeur 4        |
    | Étiquette du nom DNS          | Conservez la valeur vide    |
    | Abonnement            | Sélectionnez votre abonnement.   |
    | Resource group          | Sélectionnez **Créer**, entrez myResourceGroup et sélectionnez **OK** |
    | Emplacement                | Sélectionnez **USA Est 2**      |

S’il est acceptable que l’adresse IP change au fil du temps, l’attribution **Dynamique** d’adresse IP peut être sélectionnée.

---

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur chacun des champs listés ci-dessus, consultez [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-portal)
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).