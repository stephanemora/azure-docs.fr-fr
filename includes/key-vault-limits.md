---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 05/28/2021
ms.author: ambapat
ms.openlocfilehash: 3539e222407fee63a829e80db986f2b0eed02e24
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111761253"
---
Le service Azure Key Vault prend en charge deux types de ressources : les coffres et les HSM managés. Les deux sections suivantes décrivent les limites de service pour chacun d’entre eux.

### <a name="resource-type-vault"></a>Type de ressource : coffre

Cette section décrit les limites de service pour le type de ressource `vaults`.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transactions de clé (nombre maximal de transactions autorisées dans les 10 secondes, par coffre et par région<sup>1</sup>) :

|Type de clé|Clé HSM<br>Clé CREATE|Clé HSM<br>Toutes les autres transactions|Clé logicielle<br>Clé CREATE|Clé logicielle<br>Toutes les autres transactions|
|:---|---:|---:|---:|---:|
|RSA 2 048 bits|5|1 000|10|2 000|
|RSA 3 072 bits|5|250|10|500|
|RSA 4 096 bits|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|
||||||

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

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Secrets, clés de compte de stockage managé et transactions de coffre :

| Type de transaction | Nombre maximal de transactions autorisées dans les 10 secondes, par coffre et par région<sup>1</sup> |
| --- | --- |
| Toutes les transactions |2 000 |

Pour en savoir plus sur la façon de gérer la limitation en cas de dépassement de ces limites, voir [Aide sur la limitation de requêtes Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> La limite d’abonnement pour tous les types de transaction est fixée à 5 fois la limite des coffres de clés. Par exemple, le nombre de transactions autres que HSM par abonnement est limité à 5 000 en 10 secondes.

#### <a name="backup-keys-secrets-certificates"></a>Sauvegarder des clés, des secrets ou des certificats

Quand vous sauvegardez un objet d’un coffre de clés (secret, clé ou certificat), l’opération de sauvegarde télécharge l’objet sous la forme d’un objet blob chiffré. Cet objet blob ne peut pas être déchiffré en dehors d’Azure. Pour obtenir des données utilisables à partir de cet objet blob, vous devez restaurer l’objet blob dans un coffre de clés dans le même abonnement Azure et la même zone géographique Azure.

| Type de transaction | Nombre maximal de versions d’objet de coffre de clés autorisées |
| --- | --- |
| Sauvegarder une clé individuelle, un secret individuel ou un certificat individuel |500 |

> [!NOTE]
> Toute tentative de sauvegarde d’un objet clé, secret ou certificat avec un nombre de versions supérieur à la limite mentionnée ci-dessus génère une erreur. Il est impossible de supprimer les versions précédentes d’une clé, d’un secret ou d’un certificat. 

### <a name="limits-on-count-of-keys-secrets-and-certificates"></a>Limites du nombre de clés, de secrets et de certificats :

Key Vault ne limite pas le nombre de clés, de secrets ou de certificats qui peuvent être stockés dans un coffre. Les limites de transaction sur le coffre doivent être prises en compte afin de s’assurer que les opérations ne sont pas limitées.

Key Vault ne limite pas le nombre de versions sur un secret, une clé ou un certificat. Toutefois, le stockage d’un grand nombre de versions (+ de 500) peut avoir un impact sur les performances des opérations de sauvegarde. Consultez [Sauvegarde Azure Key Vault](../articles/key-vault/general/backup.md).

#### <a name="azure-private-link-integration"></a>Intégration d’Azure Private Link

> [!NOTE]
> Le nombre de coffres de clés avec des points de terminaison privés activés par abonnement est une limite modifiable. La limite indiquée ci-dessous est la limite par défaut. Si vous souhaitez augmenter la limite de votre service, créez une demande de support pour que celle-ci soit évaluée au cas par cas.

| Ressource | Limite |
| -------- | -----:|
| Points de terminaison privés par coffre de clés | 64 |
| Coffres de clés avec points de terminaison privés par abonnement | 400 |

### <a name="resource-type-managed-hsm"></a>Type de ressource : HSM managé

Cette section décrit les limites de service pour le type de ressource `managed HSM`.

#### <a name="object-limits"></a>Limites des objets

|Élément|Limites|
|----|------:|
Nombre d’instances HSM par région et par abonnement|1 
Nombre de clés par pool HSM|5 000
Nombre de versions par clé|100
Nombre de définitions de rôles personnalisées par HSM|50
Nombre d’attributions de rôles au niveau de l’étendue HSM|50
Nombre d’attributions de rôle à chaque étendue de clé individuelle|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limites de transactions pour les opérations d’administration (nombre d’opérations par seconde par instance HSM)
|Opération |Nombre d’opérations par seconde|
|----|------:|
Toutes les opérations RBAC<br/>(comprend toutes les opérations CRUD pour les définitions de rôles et les attributions de rôles)|5
Sauvegarde/restauration HSM complète<br/>(une seule opération de sauvegarde ou de restauration simultanée par instance HSM est prise en charge)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limites de transactions pour les opérations de chiffrement (nombre d’opérations par seconde par instance HSM)

- Chaque instance HSM managé constitue trois partitions HSM à charge équilibrée. Les limites de débit sont une fonction de la capacité matérielle sous-jacente allouée pour chaque partition. Les tableaux ci-dessous montrent le débit maximal avec au moins une partition disponible. Le débit réel peut être jusqu’à trois fois supérieur si les trois partitions sont disponibles.
- Les limites de débit indiquées partent du principe qu’une seule clé est utilisée pour obtenir le débit maximal. Par exemple, si une seule clé RSA-2048 est utilisée, le débit maximal sera de 1 100 opérations de signature. Si vous utilisez 1 100 clés différentes avec chacune une transaction par seconde, elles ne pourront pas atteindre le même débit.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Opérations de clé RSA (nombre d’opérations par seconde par instance HSM)

|Opération|2 048 bits|3 072 bits|4 096 bits|
|--|--:|--:|--:|
Créer une clé|1| 1| 1
Supprimer une clé (suppression réversible)|10|10|10 
Supprimer définitivement une clé|10|10|10 
Clé de sauvegarde|10|10|10 
Restaurer une clé|10|10|10 
Récupérer les informations d’une clé|1100|1100|1100
Encrypt (Chiffrer)|10000|10000|6000
Déchiffrer|1100|360|160
Encapsuler|10000|10000|6000
Désencapsuler|1100|360|160
Signer|1100|360|160
Vérifier|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Opérations de clé EC (nombre d’opérations par seconde par instance HSM)

Ce tableau décrit le nombre d’opérations par seconde pour chaque type de courbe.

|Opération|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Créer une clé| 1| 1| 1| 1
Supprimer une clé (suppression réversible)|10|10|10|10
Supprimer définitivement une clé|10|10|10|10
Clé de sauvegarde|10|10|10|10
Restaurer une clé|10|10|10|10
Récupérer les informations d’une clé|1100|1100|1100|1100
Signer|260|260|165|56
Vérifier|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Opérations de clé AES (nombre d’opérations par seconde par instance HSM)
- Les opérations de chiffrement et de déchiffrement partent du principe que la taille de paquet est de 4 Ko.
- Les limites de débit pour le chiffrement/déchiffrement s’appliquent aux algorithmes AES-CBC et AES-GCM.
- Les limites de débit pour Wrap/Unwrap s’appliquent à l’algorithme AES-KW.

|Opération|128 bits|192 bits|256 bits|
|--|--:|--:|--:|
Créer une clé|1| 1| 1
Supprimer une clé (suppression réversible)|10|10|10
Supprimer définitivement une clé|10|10|10
Clé de sauvegarde|10|10|10
Restaurer une clé|10|10|10
Récupérer les informations d’une clé|1100|1100|1100
Encrypt (Chiffrer)|8000|8000 |8000 
Déchiffrer|8000|8000|8000
Encapsuler|9000|9000|9000
Désencapsuler|9000|9000|9000

