---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491007"
---
#### <a name="requirements-for-key-vault-firewall"></a>Exigences pour le pare-feu Key Vault

Si le [pare-feu Key Vault](../articles/key-vault/general/network-security.md) est activé sur votre coffre de clés, les conditions suivantes sont requises :

* Vous devez utiliser l’identité managée **attribuée par le système** à l’instance Gestion des API pour accéder au coffre de clés.
* Dans le pare-feu Key Vault, activez l’option **Autoriser les services Microsoft approuvés à contourner ce pare-feu**.

#### <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel

Si l’instance Gestion des API est déployée dans un réseau virtuel, configurez également les paramètres réseau suivants :

* Activez un [point de terminaison de service](../articles/key-vault/general/overview-vnet-service-endpoints.md) pour Azure Key Vault sur le sous-réseau Gestion des API.
* Configurez une règle de groupe de sécurité réseau (NSG) pour autoriser le trafic sortant vers les [balises de service](../articles/virtual-network/service-tags-overview.md) AzureKeyVault et AzureActiveDirectory. 

Pour plus d’informations, consultez les détails de la configuration réseau dans la section [Se connecter à un réseau virtuel](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).