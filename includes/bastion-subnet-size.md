---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 62df24c61cf50f57e5f59ebca8eb2d11b2d03b12
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640625"
---
* La plus petite taille de sous-réseau AzureBastionSubnet que vous pouvez créer est /27. Toutefois, nous vous recommandons de créer une taille /26 ou supérieure pour prendre en charge la mise à l’échelle de l’hôte. 
   * Pour plus d’informations sur la mise à l’échelle, consultez [Paramètres de configuration - Mise à l'échelle de l’hôte](../articles/bastion/configuration-settings.md#instance).
   * Pour plus d’informations sur les paramètres, consultez [Paramètres de configuration - AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance).
* Créez le sous-réseau **AzureBastionSubnet** sans tables de routage ou délégations. 
* Si vous utilisez des groupes de sécurité réseau sur le sous-réseau **AzureBastionSubnet**, reportez-vous à l’article [Utiliser des groupes de sécurité réseau](../articles/bastion/bastion-nsg.md).
