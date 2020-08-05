---
title: Utiliser des clés gérées par le client avec Azure Key Vault pour gérer le chiffrement du compte
titleSuffix: Azure Storage
description: Vous pouvez utiliser votre propre clé de chiffrement pour protéger les données de votre compte de stockage. Quand vous spécifiez une clé gérée par le client, cette clé est utilisée pour protéger et contrôler l’accès à la clé qui chiffre vos données. Les clés gérées par le client offrent davantage de flexibilité pour gérer les contrôles d’accès.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: af70b1746b2ac847d964975aaf1b2186aa89be01
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292729"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Utiliser des clés gérées par le client avec Azure Key Vault pour gérer le chiffrement du Stockage Azure

Vous pouvez utiliser votre propre clé de chiffrement pour protéger les données de votre compte de stockage. Quand vous spécifiez une clé gérée par le client, cette clé est utilisée pour protéger et contrôler l’accès à la clé qui chiffre vos données. Les clés gérées par le client offrent davantage de flexibilité pour gérer les contrôles d’accès.

Vous devez utiliser Azure Key Vault pour stocker vos clés managées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région et dans le même locataire Azure Active Directory, mais ils peuvent être dans des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>À propos des clés gérées par le client

Le diagramme suivant montre comment Stockage Azure utilise Azure Active Directory et Azure Key Vault pour effectuer des requêtes à l’aide de la clé gérée par le client :

![Diagramme montrant le fonctionnement des clés managées par le client dans Stockage Azure](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

La liste suivante décrit les étapes numérotées dans le diagramme :

1. Un administrateur Azure Key Vault accorde des autorisations d’accès à des clés de chiffrement à l’identité managée associée au compte de stockage.
2. Un administrateur Stockage Azure configure le chiffrement avec une clé managée par le client pour le compte de stockage.
3. Stockage Azure utilise l’identité managée associée au compte de stockage pour authentifier l’accès à Azure Key Vault par le biais d’Azure Active Directory.
4. Stockage Azure encapsule la clé de chiffrement du compte avec la clé du client dans Azure Key Vault.
5. Pour les opérations de lecture/écriture, Stockage Azure envoie des requêtes à Azure Key Vault pour désencapsuler la clé de chiffrement du compte afin d’effectuer des opérations de chiffrement et de déchiffrement.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Créer un compte qui prend en charge les clés gérées par le client pour les files d’attente et les tables

Les données stockées dans les services File d’attente et Table ne sont pas automatiquement protégées par une clé gérée par le client quand les clés gérées par le client sont activées pour le compte de stockage. Vous pouvez éventuellement configurer ces services au moment où vous créez le compte de stockage à inclure dans cette protection.

Pour plus d’informations sur la façon de créer un compte de stockage qui prend en charge les clés gérées par le client pour les files d’attente et les tables, consultez [Créer un compte qui prend en charge les clés gérées par le client pour les tables et les files d’attente](account-encryption-key-create.md).

Les données des services Objets blob et Fichiers sont toujours protégées par des clés gérées par le client quand les clés gérées par le client sont configurées pour le compte de stockage.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Activer des clés gérées par le client pour un compte de stockage

Quand vous configurez une clé gérée par le client, Stockage Azure encapsule la clé de chiffrement des données racine pour le compte avec la clé gérée par le client dans le coffre de clés associé. L’activation des clés gérées par le client n’a aucun impact sur les performances et prend effet immédiatement.

Quand vous activez ou désactivez les clés gérées par le client, ou quand vous modifiez la clé ou la version de clé, la protection de la clé de chiffrement racine change, mais les données de votre compte Stockage Azure n’ont pas besoin d’être rechiffrées.

Les clés gérées par le client ne peuvent être activées que sur des comptes de stockage existants. Le coffre de clés doit être configuré avec des stratégies d’accès qui accordent des autorisations à l’identité managée associée au compte de stockage. L’identité managée est disponible uniquement après la création du compte de stockage.

Vous pouvez basculer entre les clés gérées par le client et les clés managées par Microsoft à tout moment. Pour plus d’informations sur les clés managées par Microsoft, consultez [À propos de la gestion des clés de chiffrement](storage-service-encryption.md#about-encryption-key-management).

Pour savoir comment utiliser des clés gérées par le client avec Azure Key Vault pour le chiffrement de Stockage Azure, consultez un de ces articles :

- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis le portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Les clés gérées par le client s’appuient sur des identités managées pour les ressources Azure, une fonctionnalité d’Azure AD. Les identités managées ne prennent actuellement pas en charge les scénarios entre répertoires. Quand vous configurez des clés managées par le client dans le portail Azure, une identité managée est affectée automatiquement à votre compte de stockage dans les coulisses. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le compte de stockage d’un annuaire Azure AD vers un autre, l’identité managée associée au compte de stockage n’est pas transférée vers le nouveau locataire ; les clés managées par le client risquent donc de ne plus fonctionner. Pour plus d’informations, consultez **Transfert d’un abonnement entre des répertoires Azure AD** dans [FAQ et problèmes connus en lien avec les identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Stocker les clés gérées par le client dans Azure Key Vault

Pour activer les clés gérées par le client sur un compte de stockage, vous devez utiliser un coffre de clés Azure pour stocker vos clés. Vous devez activer les propriétés **Suppression réversible** et **Ne pas vider** sur le coffre de clés.

Le chiffrement de stockage Azure prend en charge les clés RSA et RSA-HSM de tailles 2048, 3072 et 4096. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

L’utilisation d’Azure Key Vault génère des coûts. Pour plus d’informations, consultez [Tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="rotate-customer-managed-keys"></a>Permuter des clés gérées par le client

Vous pouvez permuter une clé gérée par le client dans Azure Key Vault en fonction de vos stratégies de conformité. Vous avez deux options pour permuter une clé gérée par le client :

- **Permutation automatique :** pour configurer la permutation automatique des clés gérées par le client, omettez la version de la clé lorsque vous activez le chiffrement avec les clés gérées par le client pour le compte de stockage. Si la version de la clé est omise, Stockage Azure vérifie Azure Key Vault quotidiennement pour voir s’il existe une nouvelle version d’une clé gérée par le client. Si une nouvelle version de la clé est disponible, Stockage Azure utilise automatiquement la dernière version de la clé.
- **Permutation manuelle :** pour utiliser une version de clé particulière pour le chiffrement de Stockage Azure, spécifiez la version de clé lorsque vous activez le chiffrement avec les clés gérées par le client pour le compte de stockage. Si vous spécifiez la version de la clé, Stockage Azure utilise cette version pour le chiffrement jusqu’à ce que vous mettiez manuellement à jour la version de la clé.

    Une fois la clé permutée manuellement, vous devez mettre à jour le compte de stockage pour utiliser le nouvel URI de version de clé. Pour découvrir comment mettre à jour le compte de stockage afin d’utiliser une nouvelle version de la clé dans le portail Azure, consultez [Mettre à jour la version de la clé manuellement](storage-encryption-keys-portal.md#manually-update-the-key-version).

La permutation d’une clé gérée par le client ne déclenche pas le rechiffrement des données dans le compte de stockage. Aucune autre action n’est requise de la part de l’utilisateur.

## <a name="revoke-access-to-customer-managed-keys"></a>Révoquer l’accès aux clés gérées par le client

Vous pouvez révoquer l’accès du compte de stockage à la clé gérée par le client à tout moment. Après la révocation de l’accès aux clés gérées par le client, ou après la désactivation ou la suppression de la clé, les clients ne peuvent pas appeler les opérations qui lisent ou écrivent dans un objet blob ou ses métadonnées. Toute tentative d’appel de l’une des opérations suivantes échouent avec le code d’erreur 403 (interdit) pour tous les utilisateurs :

- [List Blobs](/rest/api/storageservices/list-blobs), en cas d’appel avec le paramètre `include=metadata` sur l’URI de requête
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob), en cas d’appel avec l’en-tête de requête `x-ms-meta-name`
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block From URL](/rest/api/storageservices/put-block-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Append Block From URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page From URL](/rest/api/storageservices/put-page-from-url)
- [Copie incrémentielle BLOB](/rest/api/storageservices/incremental-copy-blob)

Pour rappeler ces opérations, restaurez l’accès à la clé gérée par le client.

Toutes les opérations de données qui ne sont pas mentionnées dans cette section peuvent se poursuivre après la révocation des clés gérées par le client ou après la suppression ou la désactivation d’une clé.

Pour révoquer l’accès aux clés gérées par le client, utilisez [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) ou [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Clés gérées par le client pour les disques managés Azure

Les clés gérées par le client sont également disponibles pour la gestion du chiffrement des disques managés Azure. Les clés gérées par le client se comportent différemment pour les disques managés que pour les ressources de stockage Azure. Pour plus d’informations, consultez [Chiffrement côté serveur de disques managés Azure](../../virtual-machines/windows/disk-encryption.md) pour Windows ou [Chiffrement côté serveur de disques managés Azure](../../virtual-machines/linux/disk-encryption.md) pour Linux.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis le portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement de Stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)
- [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md)
