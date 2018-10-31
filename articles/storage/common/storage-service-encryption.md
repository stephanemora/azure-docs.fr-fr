---
title: Azure Storage Service Encryption pour les données au repos | Microsoft Docs
description: La fonctionnalité Azure Storage Service Encryption permet de chiffrer Azure Managed Disks, Stockage Blob Azure, Azure Files, Stockage File d’attente Azure et Stockage Table Azure du côté du service lors du stockage des données, et de les déchiffrer quand vous récupérez les données.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: c6001b0c95186dff53f33c73833b4cea30688db2
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094890"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption pour les données au repos
Azure Storage Service Encryption pour les données au repos vous permet de protéger vos données pour garantir le respect des engagements de votre organisation en matière de sécurité et de conformité. Avec cette fonctionnalité, la plateforme de stockage Azure chiffre automatiquement vos données avant de les rendre persistantes dans Azure Managed Disks, Azure Storage Blob, File d’attente Azure, Stockage Table ou Azure Files, et déchiffre ces données avant l’extraction. La gestion du chiffrement, le chiffrement au repos, le déchiffrement et la gestion des clés dans Storage Service Encryption se font de façon transparente pour les utilisateurs. Toutes les données écrites dans la plateforme de stockage Azure sont chiffrées à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, l’un des plus puissants chiffrements par blocs disponibles.

Storage Service Encryption est activé pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti de Storage Service Encryption.

La fonctionnalité chiffre automatiquement les données dans :

- Services de stockage Azure :
    - Azure Managed Disks
    - Stockage d'objets blob Azure
    - Azure Files
    - Stockage File d’attente Azure
    - Stockage Table Azure  
- Les deux niveaux de performances (Standard et Premium).
- Deux modèles de déploiement : Azure Resource Manager et classique.

Storage Service Encryption n’affecte pas les performances des services de stockage Azure.

Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec Storage Service Encryption ou utiliser vos propres clés de chiffrement. Pour en savoir plus sur l’utilisation de vos propres clés, consultez [Storage Service Encryption avec des clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

## <a name="view-encryption-settings-in-the-azure-portal"></a>Afficher des paramètres de chiffrement dans le portail Azure
Pour afficher les paramètres de Storage Service Encryption, connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez un compte de stockage. Dans le volet **PARAMÈTRES**, sélectionnez le paramètre **Chiffrement**.

![Capture d’écran du portail affichant l’option de chiffrement](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>FAQ relatif à Storage Service Encryption
**Comment faire pour chiffrer les données dans un compte de stockage Resource Manager ?**  
Storage Service Encryption est activé pour tous les comptes de stockage (comptes classiques et Resource Manager). Tous les fichiers existants dans le compte de stockage créés avant l’activation du chiffrement sont chiffrés rétroactivement par un processus de chiffrement d’arrière-plan.

**Storage Service Encryption est-il activé par défaut lorsque je crée un compte de stockage ?**  
Oui, Storage Service Encryption est activé pour tous les comptes de stockage et pour tous les services de stockage Azure.

**Je dispose d’un compte de stockage Resource Manager. Puis-je y activer Storage Service Encryption ?**  
Storage Service Encryption est activé par défaut sur tous les comptes de stockage Resource Manager existants. Il est pris en charge pour Stockage Blob Azure, Azure Files, Stockage File d’attente Azure et Stockage Table Azure. 

**Puis-je activer le chiffrement sur mon compte de stockage ?**  
Le chiffrement est activé par défaut, et aucune disposition ne prévoit de désactiver le chiffrement de votre compte de stockage. 

**Quel est le coût supplémentaire du stockage Azure si Storage Service Encryption est activé ?**  
Aucun coût supplémentaire n’est facturé.

**Puis-je utiliser mes propres clés de chiffrement ?**  
Pour Stockage Blob Azure et Azure Files, oui, vous pouvez utiliser vos propres clés de chiffrement. Les clés gérées par le client ne sont actuellement pas prises en charge par Azure Managed Disks. Pour plus d’informations, consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

**Est-il possible de révoquer l’accès aux clés de chiffrement ?**  
Oui, si vous [utilisez vos propres clés de chiffrement](storage-service-encryption-customer-managed-keys.md) dans Azure Key Vault.

**En quoi Storage Service Encryption est-il différent d’Azure Disk Encryption ?**  
Azure Disk Encryption fournit une intégration entre les solutions basées sur le système d’exploitation, telles que BitLocker et DM-Crypt, et Azure Key Vault. Storage Service Encryption assure un chiffrement en mode natif dans la couche de plateforme de stockage Azure, sous la machine virtuelle.

**Je dispose d’un compte de stockage classique. Puis-je y activer Storage Service Encryption ?**  
Storage Service Encryption est activé pour tous les comptes de stockage (classiques et Resource Manager).

**Comment chiffrer les données de mon compte de stockage classique ?**  
Avec le chiffrement activé par défaut, toutes les données stockées dans les services de stockage Azure sont automatiquement chiffrées. 

**Puis-je créer des comptes de stockage dans lesquels Storage Service Encryption est activé à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure ?**  
Storage Service Encryption est activé par défaut lors de la création d’un compte de stockage (classique ou Resource Manager). Vous pouvez vérifier les propriétés du compte à l’aide d’Azure PowerShell et de l’interface de ligne de commande Azure.

**Mon compte de stockage est configuré pour être répliqué de manière géoredondante. Avec Storage Service Encryption, ma copie redondante sera-t-elle également chiffrée ?**  
Oui, toutes les copies du compte de stockage sont chiffrées. Toutes les options de redondance sont prises en charge : stockage localement redondant, stockage redondant interzone, stockage géoredondant et stockage géoredondant avec accès en lecture.

**Storage Service Encryption est-il autorisé uniquement dans certaines régions ?**  
Storage Service Encryption est disponible dans toutes les régions.

**Storage Service Encryption est-il conforme à la norme FIPS 140-2 ?**  
Oui, Storage Service Encryption est conforme à la norme FIPS 140-2.

**Comment faire pour obtenir de l’aide si je rencontre des problèmes ou que je souhaite envoyer des commentaires ?**  
Contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour faire part de vos problèmes ou commentaires liés à Storage Service Encryption.

## <a name="next-steps"></a>Étapes suivantes
Le stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, ensemble, aident les développeurs à créer des applications sécurisées. Pour plus d’informations, consultez le [guide de sécurité Stockage Azure](../storage-security-guide.md).
