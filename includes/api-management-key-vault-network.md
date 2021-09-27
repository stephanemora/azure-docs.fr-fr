---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: 86b5a62d7aaf16009e59637ebe9a967fce1e0358
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569588"
---
#### <a name="requirements-for-key-vault-firewall"></a>Exigences pour le pare-feu Key Vault

Si le [pare-feu Key Vault](../articles/key-vault/general/network-security.md) est activé sur votre coffre de clés, les conditions suivantes sont requises :

* Vous devez utiliser l’identité managée **attribuée par le système** à l’instance Gestion des API pour accéder au coffre de clés.
* Dans le pare-feu Key Vault, activez l’option **Autoriser les services Microsoft approuvés à contourner ce pare-feu**.

#### <a name="virtual-network-requirements"></a>Conditions requises pour le réseau virtuel

Si l’instance Gestion des API est déployée dans un réseau virtuel, configurez également les paramètres réseau suivants :

* Activez un [point de terminaison de service](../articles/key-vault/general/overview-vnet-service-endpoints.md) pour Azure Key Vault sur le sous-réseau Gestion des API.
* Configurez une règle de groupe de sécurité réseau (NSG) pour autoriser le trafic sortant vers les [balises de service](../articles/virtual-network/service-tags-overview.md) AzureKeyVault et AzureActiveDirectory. 

Pour plus d’informations, consultez les détails de la configuration réseau dans la section [Se connecter à un réseau virtuel](../articles/api-management/api-management-using-with-vnet.md#network-configuration-issues).