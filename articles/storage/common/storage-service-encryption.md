---
title: Chiffrement du stockage Azure pour les données au repos | Microsoft Docs
description: Stockage Azure protège vos données en chiffrant automatiquement avant du rendre persistantes dans le cloud. Toutes les données dans un stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du chiffrement AES 256 bits et n’est conformes aux normes FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e95adbd1a564fb34d3f0506ac1cc25bc5a63c62
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790049"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Chiffrement du stockage Azure pour les données au repos

Stockage Azure chiffre automatiquement vos données lorsqu’il rend persistante dans le cloud. Chiffrement protège vos données et pour vous aider à répondre à vos engagements en matière de sécurité et de conformité d’organisation. Données dans le stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide de 256 bits [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), celui du bloc plus fort chiffrements et n’est conformes aux normes FIPS 140-2. Chiffrement du stockage Azure est similaire pour le chiffrement BitLocker sur Windows.

Chiffrement du stockage Azure est activé pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou les applications pour tirer parti du chiffrement de stockage Azure. 

Comptes de stockage sont chiffrées, quel que soit leur niveau de performances (standard ou premium) ou un modèle de déploiement (Azure Resource Manager ou classique). Toutes les options de redondance de stockage Azure prend en charge le chiffrement, et toutes les copies d’un compte de stockage sont chiffrées. Toutes les ressources de stockage Azure sont chiffrées, y compris les objets BLOB, les disques, les fichiers, les files d’attente et les tables. Toutes les métadonnées d’objet sont également chiffrées.

Le chiffrement n’affecte pas les performances de stockage Azure. Il n’existe aucun coût supplémentaire pour le chiffrement de stockage Azure.

Pour plus d’informations sur les modules cryptographiques sous-jacentes de chiffrement de stockage Azure, consultez [Cryptography API : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Gestion des clés

Vous pouvez compter sur les clés gérées par Microsoft pour le chiffrement de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés, ainsi que d’Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Clés gérées par Microsoft

Par défaut, votre compte de stockage utilise des clés de chiffrement gérées par Microsoft. Vous pouvez voir les paramètres de chiffrement de votre compte de stockage dans le **chiffrement** section de la [Azure portal](https://portal.azure.com), comme illustré dans l’image suivante.

![Afficher le compte chiffré avec clés gérées par Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Clés gérées par le client

Vous pouvez gérer le chiffrement du stockage Azure avec des clés gérées par le client. Clés gérées par le client vous donnent plus de souplesse pour créer, faire pivoter, désactiver et révoquer des contrôles d’accès. Vous pouvez également auditer les clés de chiffrement utilisés pour protéger vos données. 

Utilisez Azure Key Vault pour gérer vos clés et d’audit de votre utilisation de la clé. Vous pouvez soit créer vos propres clés et les stocker dans un coffre de clés, ou vous pouvez utiliser les API Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [qu’est Azure Key Vault ?](../../key-vault/key-vault-overview.md).

Pour révoquer l’accès aux clés de gérée par le client, consultez [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) et [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Révocation de l’accès efficacement de bloque l’accès à toutes les données du compte de stockage, comme la clé de chiffrement n’est pas accessible par le stockage Azure.

Pour savoir comment utiliser des clés gérées par le client avec le stockage Azure, consultez ces articles :

- [Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir du portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés de gérée par le client pour le chiffrement de stockage Azure à partir de PowerShell](storage-encryption-keys-powershell.md)
- [Utiliser des clés de gérée par le client avec chiffrement de stockage Azure à partir d’Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Clés gérées par le client s’appuient sur des identités gérées pour les ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Lors du transfert d’un abonnement à partir d’un répertoire Azure AD aux identités d’un autre, gérées ne sont pas mis à jour et les clés de gérée par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez **transfert d’un abonnement entre annuaires Azure AD** dans [FAQ et problèmes connus avec gérés des identités pour les ressources Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Clés gérées par le client ne sont pas pris en charge pour [disques gérés Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Chiffrement du stockage Azure et le chiffrement de disque

Avec le chiffrement de stockage Azure, les tous les comptes de stockage Azure et les ressources qu’ils contiennent sont chiffrées, y compris les objets BLOB de pages qui stockent les disques de machine virtuelle Azure. En outre, les disques de machine virtuelle Azure peuvent être chiffrés avec [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption utilise le standard de l’industrie [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) sur Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) sur Linux pour fournir des solutions de chiffrement basé sur le système d’exploitation qui sont intégrées à Azure Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md)
