---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531641"
---
* **Une [adresse IPv4 publique](../articles/virtual-network/public-ip-addresses.md#standard) de référence SKU standard**, si votre client utilise la version d’API 2021-01-01-preview ou une version ultérieure. La ressource d’adresse IP publique est requise lors de la configuration du réseau virtuel pour un accès externe ou interne. Avec un réseau virtuel interne, l’adresse IP publique est utilisée uniquement pour les opérations de gestion. En savoir plus sur les [adresses IP de Gestion des API](../articles/api-management/api-management-howto-ip-addresses.md).

  * L’adresse IP doit être dans la même région et le même abonnement que l’instance Gestion des API et le réseau virtuel.

  * La valeur de l’adresse IP est affectée en tant qu’adresse IPv4 publique virtuelle de l’instance Gestion des API dans cette région. 

  * Lorsque vous passez d’un réseau virtuel externe à un réseau virtuel interne (ou vice versa), que vous modifiez des sous-réseaux dans le réseau ou que vous mettez à jour des zones de disponibilité pour l’instance Gestion des API, vous devez configurer une autre adresse IP publique. 

  > [!IMPORTANT]
  > Actuellement, le portail Azure utilise la version d’API 2021-01-01-preview lors de la création ou de la mise à jour d’une instance Gestion des API. Vous pouvez spécifier cette version de l’API à l’aide d’un modèle Azure Resource Manager ou de l’API REST Gestion des API. Azure CLI et Azure PowerShell prennent actuellement en charge l’API version 2020-12-01.
