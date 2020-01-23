---
title: Chiffrement du Stockage Azure pour les données au repos
description: Le Stockage Azure protège vos données en les chiffrant automatiquement avant de les rendre persistantes dans le cloud. Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés.
services: storage
author: tamram
ms.service: storage
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abb9325510b52672027338314e02466f2d28e701
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942205"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Chiffrement du Stockage Azure pour les données au repos

Le Stockage Azure chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement de Stockage Azure protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

## <a name="about-azure-storage-encryption"></a>À propos du chiffrement de Stockage Azure

Les données dans Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Le chiffrement du Stockage Azure est similaire au chiffrement BitLocker sur Windows.

Le chiffrement de Stockage Azure est activé pour tous les nouveaux comptes de stockage, y compris les comptes de stockage classiques et Resource Manager. Le chiffrement de Stockage Azure ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement du Stockage Azure.

Les comptes de stockage sont chiffrés quel que soit leur niveau de performances (standard ou premium) ou modèle de déploiement (Azure Resource Manager ou Classic). Toutes les options de redondance de Stockage Azure prennent en charge le chiffrement, et toutes les copies d’un compte de stockage sont chiffrées. Toutes les ressources de Stockage Azure sont chiffrées, y compris les objets blob, disques, fichiers, files d’attente et tables. Toutes les métadonnées d’objet sont également chiffrées.

Le chiffrement n’affecte pas les performances de Stockage Azure. Le chiffrement de Stockage Azure n’implique aucun coût supplémentaire.

Chaque objet blob de blocs, objet blob d’ajout ou objet blob de pages qui a été écrit dans le Stockage Azure après le 20 octobre 2017 est chiffré. Les objets blob créés avant cette date continuent à être chiffrés à l’aide d’un processus en arrière-plan. Pour forcer le chiffrement d’un objet blob qui a été créé avant le 20 octobre 2017, vous pouvez réécrire l’objet. Pour savoir comment vérifier l’état de chiffrement d’un objet blob, consultez [Vérifier l’état de chiffrement d’un objet blob](../blobs/storage-blob-encryption-status.md).

Pour plus d’informations sur les modules cryptographiques de chiffrement de Stockage Azure, consultez [API de chiffrement : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, deux options s’offrent à vous :

- Vous pouvez spécifier une *clé gérée par le client* avec Azure Key Vault à utiliser pour le chiffrement et le déchiffrement des données dans Stockage Blob et dans Azure Files.<sup>1,2</sup>
- Vous pouvez spécifier une *clé fournie par le client* sur les opérations de stockage Blob. Un client qui effectue une requête de lecture ou d’écriture sur le stockage Blob peut inclure une clé de chiffrement dans la requête afin de contrôler la précision avec laquelle les données blob sont chiffrées et déchiffrées.

Le tableau suivant compare les options de gestion de clés pour le chiffrement de Stockage Azure.

|                                        |    Clés managées par Microsoft                             |    Clés managées par le client                                                                                                                        |    Clés fournies par le client                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Opérations de chiffrement/déchiffrement    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Services de stockage Azure pris en charge    |    Tous                                                |    Stockage Blob, Azure Files<sup>1,2</sup>                                                                                                               |    Stockage d'objets blob                                                                  |
|    Stockage des clés                         |    Magasin de clés Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault ou tout autre magasin de clés                                                                 |
|    Responsabilité de la permutation des clés         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    Utilisation de la clé                           |    Microsoft                                          |    Portail Azure, API REST de fournisseur de ressources de stockage, bibliothèques de gestion de Stockage Azure, PowerShell, CLI        |    API REST de Stockage Azure (stockage d’objets blob), bibliothèques de client Stockage Azure    |
|    Accès aux clés                          |    Microsoft uniquement                                     |    Microsoft, client                                                                                                                    |    Client uniquement                                                                 |

<sup>1</sup> Pour plus d’informations sur la création d’un compte qui prend en charge l’utilisation de clés gérées par le client avec Stockage File d’attente, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les files d’attente](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Pour plus d’informations sur la création d’un compte qui prend en charge l’utilisation de clés gérées par le client avec Stockage Table, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les tables](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

Les sections suivantes décrivent en détail chacune des options de gestion des clés.

## <a name="microsoft-managed-keys"></a>Clés managées par Microsoft

Par défaut, votre compte de stockage utilise des clés de chiffrement managées par Microsoft. Vous pouvez voir les paramètres de chiffrement de votre compte de stockage dans la section **Chiffrement** du [Portail Azure](https://portal.azure.com), comme illustré dans l’image suivante.

![Afficher le compte chiffré avec des clés managées par Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Clés gérées par le client avec Azure Key Vault

Vous pouvez gérer le chiffrement de Stockage Azure au niveau du compte de stockage avec vos propres clés. Quand vous spécifiez une clé gérée par le client au niveau du compte de stockage, cette clé est utilisée pour protéger la clé de chiffrement racine du compte de stockage et contrôler son accès. Cette dernière est à son tour utilisée pour chiffrer et déchiffrer toutes les données d’objet blob et de fichier. Les clés managées par le client offrent plus de flexibilité pour créer, permuter, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données.

Vous devez utiliser Azure Key Vault pour stocker vos clés managées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région et dans le même locataire Azure Active Directory, mais ils peuvent être dans des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md).

Ce diagramme montre comment Stockage Azure utilise Azure Active Directory et Azure Key Vault pour effectuer des requêtes à l’aide de la clé managée par le client :

![Diagramme montrant le fonctionnement des clés managées par le client dans Stockage Azure](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

La liste suivante décrit les étapes numérotées dans le diagramme :

1. Un administrateur Azure Key Vault accorde des autorisations d’accès à des clés de chiffrement à l’identité managée associée au compte de stockage.
2. Un administrateur Stockage Azure configure le chiffrement avec une clé managée par le client pour le compte de stockage.
3. Stockage Azure utilise l’identité managée associée au compte de stockage pour authentifier l’accès à Azure Key Vault par le biais d’Azure Active Directory.
4. Stockage Azure encapsule la clé de chiffrement du compte avec la clé du client dans Azure Key Vault.
5. Pour les opérations de lecture/écriture, Stockage Azure envoie des requêtes à Azure Key Vault pour encapsuler et désencapsuler la clé de chiffrement du compte afin d’effectuer des opérations de chiffrement et de déchiffrement.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Activer des clés gérées par le client pour un compte de stockage

Quand vous activez le chiffrement avec des clés gérées par le client pour un compte de stockage, le Stockage Azure wrappe la clé de chiffrement du compte avec la clé gérée par le client dans le coffre de clés associé. L’activation des clés gérées par le client n’impacte pas les performances et le compte est immédiatement chiffré avec la nouvelle clé.

Un nouveau compte de stockage est toujours chiffré à l’aide de clés managées par Microsoft. Il est impossible d’activer des clés gérées par le client au moment de la création du compte. Les clés gérées par le client sont stockées dans Azure Key Vault, et le coffre de clés doit être provisionné avec des stratégies d’accès qui accordent des autorisations de clé à l’identité managée associée au compte de stockage. L’identité managée est disponible uniquement après la création du compte de stockage.

Quand vous modifiez la clé utilisée pour le chiffrement du Storage Azure en activant ou en désactivant des clés gérées par le client, en mettant à jour la version de la clé ou en spécifiant une clé différente, le chiffrement de la clé racine change, mais les données de votre compte de Stockage Azure n’ont pas besoin d’être rechiffrées.

Pour savoir comment utiliser des clés gérées par le client avec Azure Key Vault pour le chiffrement de Stockage Azure, consultez un de ces articles :

- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis le portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Les clés gérées par le client s’appuient sur des identités managées pour les ressources Azure, une fonctionnalité d’Azure AD. Les identités managées ne prennent actuellement pas en charge les scénarios entre annuaires. Quand vous configurez des clés managées par le client dans le portail Azure, une identité managée est affectée automatiquement à votre compte de stockage dans les coulisses. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le compte de stockage d’un annuaire Azure AD vers un autre, l’identité managée associée au compte de stockage n’est pas transférée vers le nouveau locataire ; les clés managées par le client risquent donc de ne plus fonctionner. Pour plus d’informations, consultez **Transfert d’un abonnement entre des répertoires Azure AD** dans [FAQ et problèmes connus en lien avec les identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Stocker les clés gérées par le client dans Azure Key Vault

Pour activer les clés gérées par le client sur un compte de stockage, vous devez utiliser un coffre de clés Azure pour stocker vos clés. Vous devez activer les propriétés **Suppression réversible** et **Ne pas vider** sur le coffre de clés.

Seules les clés RSA de taille 2048 sont prises en charge avec le chiffrement du Stockage Azure. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Permuter des clés gérées par le client

Vous pouvez permuter une clé gérée par le client dans Azure Key Vault en fonction de vos stratégies de conformité. Une fois la clé permutée, vous devez mettre à jour le compte de stockage pour utiliser le nouvel URI de clé. Pour savoir comment mettre à jour le compte de stockage afin d’utiliser une nouvelle version de la clé dans le portail Azure, consultez la section intitulée **Mettre à jour la version de la clé** dans [Configurer les clés gérées par le client pour Stockage Azure à l’aide du portail Azure](storage-encryption-keys-portal.md).

La permutation de la clé ne déclenche pas le rechiffrement des données dans le compte de stockage. Aucune autre action n’est requise de la part de l’utilisateur.

### <a name="revoke-access-to-customer-managed-keys"></a>Révoquer l’accès aux clés gérées par le client

Pour révoquer l’accès aux clés gérées par le client, utilisez PowerShell ou Azure CLI. Pour plus d’informations, consultez [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) ou [Interface de ligne de commande Azure Key Vault](/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les données dans le compte de stockage, car la clé de chiffrement n’est pas accessible au Stockage Azure.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Clés gérées par le client pour des disques managés Azure (préversion)

Les clés gérées par le client sont également disponibles pour la gestion du chiffrement des disques managés Azure (préversion). Les clés gérées par le client se comportent différemment pour les disques managés que pour les ressources de stockage Azure. Pour plus d’informations, consultez [Chiffrement côté serveur de disques managés Azure](../../virtual-machines/windows/disk-encryption.md) pour Windows ou [Chiffrement côté serveur de disques managés Azure](../../virtual-machines/linux/disk-encryption.md) pour Linux.

## <a name="customer-provided-keys-preview"></a>Clés fournies par le client (préversion)

Les clients effectuant des requêtes auprès du stockage d’objets blob Azure ont la possibilité de fournir une clé de chiffrement sur une requête individuelle. L’inclusion de la clé de chiffrement dans la requête offre un contrôle précis des paramètres de chiffrement pour les opérations de stockage d’objets blob. Les clés fournies par le client (préversion) peuvent être stockées dans Azure Key Vault ou dans un autre magasin de clés.

Pour obtenir un exemple qui montre comment spécifier une clé fournie par le client sur une demande au stockage Blob, consultez [Spécifier une clé fournie par le client sur une demande au stockage Blob avec .NET](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Chiffrement des opérations de lecture et d’écriture

Quand une application cliente fournit une clé de chiffrement dans la requête, Stockage Azure effectue le chiffrement et le déchiffrement de manière transparente lors de la lecture et de l’écriture des données blob. Stockage Azure écrit un hachage SHA-256 de la clé de chiffrement avec le contenu de l’objet blob. Le hachage est utilisé pour vérifier que toutes les opérations suivantes sur l’objet blob utilisent la même clé de chiffrement. 

Stockage Azure ne stocke pas et ne gère pas la clé de chiffrement que le client envoie avec la requête. La clé est supprimée de façon sécurisée dès que le processus de chiffrement ou de déchiffrement est terminé.

Quand un client crée ou met à jour un objet blob à l’aide d’une clé fournie par le client, les requêtes de lecture et d’écriture suivantes pour cet objet blob doivent également fournir la clé. Si la clé n’est pas fournie sur une requête d’objet blob qui a déjà été chiffrée avec une clé fournie par le client, la requête échoue avec le code d’erreur 409 (conflit).

Si l’application cliente envoie une clé de chiffrement dans la requête et que le compte de stockage est également chiffré à l’aide d’une clé managée par Microsoft ou d’une clé managée par le client, Stockage Azure utilise la clé fournie dans la requête pour le chiffrement et le déchiffrement.

Pour envoyer la clé de chiffrement dans le cadre de la requête, un client doit établir une connexion sécurisée à Stockage Azure à l’aide du protocole HTTPS.

Chaque instantané d’objet blob peut avoir sa propre clé de chiffrement.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>En-têtes de requêtes pour spécifier les clés fournies par le client

Pour les appels REST, les clients peuvent utiliser les en-têtes suivants pour transmettre en toute sécurité des informations sur les clés de chiffrement sur une requête au stockage d’objets blob :

|En-tête de requête | Description |
|---------------|-------------|
|`x-ms-encryption-key` |Nécessaire pour les requêtes d’écriture et de lecture. Valeur de clé de chiffrement AES-256 encodée en base64. |
|`x-ms-encryption-key-sha256`| Nécessaire pour les requêtes d’écriture et de lecture. Valeur SHA256 encodée en base64 de la clé de chiffrement. |
|`x-ms-encryption-algorithm` | Nécessaire pour les requêtes d’écriture, facultatif pour les requêtes de lecture. Spécifie l’algorithme à utiliser lors du chiffrement des données à l’aide de la clé donnée. Doit être AES256. |

La spécification de clés de chiffrement dans la requête est facultative. Toutefois, si vous spécifiez l’un des en-têtes listés ci-dessus pour une opération d’écriture, vous devez les spécifier tous.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Opérations de stockage d’objets blob prenant en charge les clés fournies par le client

Les opérations de stockage d’objets blob suivantes prennent en charge l’envoi de clés de chiffrement fournies par le client sur une requête :

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block from URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Placer la page à partir de l’URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Permuter des clés fournies par le client

Pour permuter une clé de chiffrement transmise dans la requête, téléchargez l’objet blob et rechargez-le avec la nouvelle clé de chiffrement.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser le portail Azure pour lire ou écrire dans un conteneur ou un objet blob chiffré avec une clé fournie dans la requête.
>
> Veillez à protéger la clé de chiffrement que vous fournissez dans une requête au stockage d’objets blob dans un magasin de clés sécurisé comme Azure Key Vault. Si vous tentez d’effectuer une opération d’écriture sur un conteneur ou un objet blob sans clé de chiffrement, l’opération échoue et vous perdez l’accès à l’objet.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Chiffrement du Stockage Azure et chiffrement de disque

Le chiffrement du stockage Azure chiffre les objets blob de pages qui soutiennent les disques de machines virtuelles Azure. Tous les disques de machine virtuelle Azure, y compris les disques temporaires locaux, peuvent également être chiffrés (facultatif) avec [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption utilise les fonctionnalités standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) sur Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) sur Linux pour fournir des solutions de chiffrement basées sur le système d’exploitation qui sont intégrées à Azure Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis le Portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Configurer des clés gérées par le client pour le chiffrement du stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)
