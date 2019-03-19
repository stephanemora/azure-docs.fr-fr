---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0e55c372c6f5dc3484bd64cf4f328479d2d0b245
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553582"
---
## <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transactions de clé (transactions maximales autorisées dans les 10 secondes, par coffre par région<sup>1</sup>) :

|Type de clé|Clé HSM<br>CRÉER la clé|Clé HSM<br>Toutes les autres transactions|Clé logicielle<br>CRÉER la clé|Clé logicielle<br>Toutes les autres transactions|
|:---|---:|---:|---:|---:|
|RSA 2 048 bits|5.|1 000|10|2 000|
|RSA 3 072 bits|5.|250|10|500|
|RSA 4 096 bits|5.|125|10|250|
|ECC P-256|5.|1 000|10|2 000|
|ECC P-384|5.|1 000|10|2 000|
|ECC P-521|5.|1 000|10|2 000|
|ECC SECP256K1|5.|1 000|10|2 000|

> [!NOTE]
> Dans le tableau précédent, nous voyons que pour les clés de logiciels de RSA 2 048 bits, 2 000 transactions GET toutes les 10 secondes sont autorisées. Des clés-RSA 2 048 bits HSM, toutes les 10 secondes de 1 000 transactions GET sont autorisées.
>
> Les seuils de limitation sont pondérées et l’application se trouve sur leur somme. Par exemple, comme indiqué dans le tableau précédent, lorsque vous effectuez des opérations GET sur les clés RSA HSM, il est huit fois plus coûteux d’utiliser des clés de 4 096 bits par rapport aux clés de 2 048 bits. C’est parce que 1 000/125 = 8.
>
> Dans un intervalle de 10 secondes donné, un client Azure Key Vault faire *qu’une seule* des opérations suivantes avant qu’il rencontre un `429` la limitation de code d’état HTTP :
> - 2 000 transactions de GET-clé logicielle RSA 2 048 bits
> - 1 000 transactions de RSA 2 048 bits clé HSM GET
> - Transactions de 4 096 RSA bits clé HSM GET 125
> - Les transactions de 4 096 RSA bits de la clé HSM GET 124 et GET de clé HSM RSA 2 048 bits 8

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Secrets, les clés de compte de stockage géré et les transactions de coffre :
| Type de transaction | Nombre maximal de transactions autorisé dans les 10 secondes, par coffre par région<sup>1</sup> |
| --- | --- |
| Toutes les transactions |2 000 |

Pour plus d’informations sur la façon de gérer la limitation lorsque ces limites sont dépassées, consultez [Azure Key Vault aide sur la limitation](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> une limite à l’échelle de l’abonnement pour tous les types de transaction est cinq fois par limite de coffre de clés. Par exemple, HSM-autres transactions par abonnement sont limitées à 5 000 transactions dans 10 secondes par abonnement.
