---
title: Chiffrement du Stockage Azure pour les données au repos | Microsoft Docs
description: Le Stockage Azure protège vos données en les chiffrant automatiquement avant de les rendre persistantes dans le cloud. Toutes les données dans un Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du chiffrement AES 256 bits et sont conformes à la norme FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e95adbd1a564fb34d3f0506ac1cc25bc5a63c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790049"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Chiffrement du Stockage Azure pour les données au repos

Le Stockage Azure chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Les données dans Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Le chiffrement du Stockage Azure est similaire au chiffrement BitLocker sur Windows.

Le chiffrement du Stockage Azure est activé pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement du Stockage Azure. 

Les comptes de stockage sont chiffrés quel que soit leur niveau de performances (standard ou premium) ou modèle de déploiement (Azure Resource Manager ou Classic). Toutes les options de redondance de Stockage Azure prennent en charge le chiffrement, et toutes les copies d’un compte de stockage sont chiffrées. Toutes les ressources de Stockage Azure sont chiffrées, y compris les objets blob, disques, fichiers, files d’attente et tables. Toutes les métadonnées d’objet sont également chiffrées.

Le chiffrement n’affecte pas les performances de Stockage Azure. Le chiffrement de Stockage Azure n’implique aucun coût supplémentaire.

Pour plus d’informations sur les modules cryptographiques de chiffrement de Stockage Azure, consultez [API de chiffrement : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Gestion des clés

Vous pouvez compter sur les clés managées par Microsoft pour le chiffrement de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés, ainsi qu’avec Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Clés managées par Microsoft

Par défaut, votre compte de stockage utilise des clés de chiffrement managées par Microsoft. Vous pouvez voir les paramètres de chiffrement de votre compte de stockage dans la section **Chiffrement** du [Portail Azure](https://portal.azure.com), comme illustré dans l’image suivante.

![Afficher le compte chiffré avec des clés managées par Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Clés managées par le client

Vous pouvez gérer le chiffrement du Stockage Azure avec des clés managées par le client. Les clés managées par le client vous offrent plus de flexibilité pour créer, faire pivoter, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données. 

Utilisez Azure Key Vault pour gérer vos clés et effectuez un audit d’utilisation de votre clé. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md).

Pour révoquer l’accès aux clés managées par le client, consultez [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) et [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les données dans le compte de stockage, car la clé de chiffrement n’est pas accessible au Stockage Azure.

Pour savoir comment utiliser des clés managées par le client avec le Stockage Azure, consultez un de ces articles :

- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis le Portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Utiliser les clés managées par le client avec le chiffrement du Stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Lorsque vous transférez un abonnement d’un répertoire Azure AD vers un autre, les identités managées ne sont pas mises à jour et les clés managées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez **Transfert d’un abonnement entre des répertoires Azure AD** dans [FAQ et problèmes connus en lien avec les identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Les clés managées par le client ne sont pas prises en charge pour des [disques managés Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Chiffrement du Stockage Azure et chiffrement de disque

Avec le chiffrement du Stockage Azure, tous les comptes de Stockage Azure et les ressources qu’ils contiennent sont chiffrés, y compris les objets blob de pages qui stockent des disques de machine virtuelle Azure. Les disques de machine virtuelle Azure peuvent également être chiffrés avec [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption utilise les fonctionnalités standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) sur Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) sur Linux pour fournir des solutions de chiffrement basées sur le système d’exploitation qui sont intégrées à Azure Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md)
