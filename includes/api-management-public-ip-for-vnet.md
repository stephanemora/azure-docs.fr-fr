---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 413328496d35346ef6b7219bbed40755160ce9eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287473"
---
* **[Adresse IPv4 publique](../articles/virtual-network/ip-services/public-ip-addresses.md#standard)de la référence SKU Standard**. La ressource d’adresse IP publique est requise lors de la configuration du réseau virtuel pour un accès externe ou interne. Avec un réseau virtuel interne, l’adresse IP publique est utilisée uniquement pour les opérations de gestion. En savoir plus sur les [adresses IP de Gestion des API](../articles/api-management/api-management-howto-ip-addresses.md).

  * L’adresse IP doit être dans la même région et le même abonnement que l’instance Gestion des API et le réseau virtuel.

  * La valeur de l’adresse IP est affectée en tant qu’adresse IPv4 publique virtuelle de l’instance Gestion des API dans cette région. 

  * Lorsque vous passez d’un réseau virtuel externe à un réseau virtuel interne (ou vice versa), que vous modifiez des sous-réseaux dans le réseau ou que vous mettez à jour des zones de disponibilité pour l’instance Gestion des API, vous devez configurer une autre adresse IP publique.