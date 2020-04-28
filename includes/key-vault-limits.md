---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 8247b1cedc2c5ebc8577af6be485aed0fcd5d6af
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768754"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transactions de clé (nombre maximal de transactions autorisées dans les 10 secondes, par coffre et par région<sup>1</sup>) :

|Type de clé|Clé HSM<br>Clé CREATE|Clé HSM<br>Toutes les autres transactions|Clé logicielle<br>Clé CREATE|Clé logicielle<br>Toutes les autres transactions|
|:---|---:|---:|---:|---:|
|RSA 2 048 bits|5|1 000|10|2 000|
|RSA 3 072 bits|5|250|10|500|
|RSA 4 096 bits|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|

> [!NOTE]
> Dans la table ci-dessus, nous voyons que 2 000 transactions GET par tranche de 10 secondes sont autorisées pour des clés logicielles RSA 2 048 bits. Pour les clés HSM 2 048 bits RSA, 1 000 transactions GET par tranche de 10 secondes sont autorisées.
>
> Les seuils de limitation sont pondérés, et leur application correspond à leur somme. Par exemple, comme indiqué dans la table précédente, lorsque vous effectuez des opérations GET sur des clés HSM RSA, il est huit fois plus coûteux d’utiliser des clés de 4 096 bits par rapport aux clés de 2 048 bits. En effet, 1 000/125 = 8.
>
> Dans un intervalle de 10 secondes donné, un client Azure Key Vault peut exécuter *une seule* des opérations suivantes avant de rencontrer un code d’état HTTP de limitation `429` :
> - 2 000 transactions GET de clé logicielle RSA 2 048 bits
> - 1 000 transactions GET de clé HSM RSA 2 048 bits
> - 125 transactions GET de clé HSM RSA 4 096 bits
> - 124 transactions GET de clé HSM RSA 4 096 bits et 8 transactions GET de clé HSM RSA 2 048 bits

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Secrets, clés de compte de stockage managé et transactions de coffre :

| Type de transaction | Nombre maximal de transactions autorisées dans les 10 secondes, par coffre et par région<sup>1</sup> |
| --- | --- |
| Toutes les transactions |2 000 |

Pour en savoir plus sur la façon de gérer la limitation en cas de dépassement de ces limites, voir [Aide sur la limitation de requêtes Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> La limite d’abonnement pour tous les types de transaction est fixée à 5 fois la limite des coffres de clés. Par exemple, le nombre de transactions autres que HSM par abonnement est limité à 5 000 en 10 secondes.

### <a name="azure-private-link-integration"></a>Intégration d’Azure Private Link

| Ressource | Limite |
| -------- | ----- |
| Points de terminaison privés par coffre de clés | 64 |
| Coffres de clés avec points de terminaison privés par abonnement | 64 |
