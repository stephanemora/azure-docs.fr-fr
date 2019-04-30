---
title: FAQ - Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article offre des réponses au forum aux questions sur Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/16/2019
ms.custom: seodec18
ms.openlocfilehash: 2a5fb822a84792cec059aa6c78b66b3a628f25af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611287"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>FAQ Azure Disk Encryption pour machines virtuelles IaaS

Cet article offre des réponses au forum aux questions (FAQ) sur Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux. Pour plus d’informations sur ce service, consultez [Azure Disk Encryption pour machines virtuelles IaaS Windows et Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Où se trouve Azure Disk Encryption en disponibilité générale (GA) ?

Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux est en disponibilité générale dans toutes les régions publiques Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quelles expériences utilisateur sont disponibles avec Azure Disk Encryption ?

La disponibilité générale (GA) Azure Disk Encryption prend en charge les modèles Azure Resource Manager, Azure PowerShell et Azure CLI. Les différentes expériences utilisateur vous donnent de la souplesse. Vous disposez de trois options différentes pour activer le chiffrement des disques sur vos machines virtuelles IaaS. Pour plus d’informations sur l’expérience utilisateur et des instructions étape par étape disponibles dans Azure Disk Encryption, consultez [Activer Azure Disk Encryption pour Windows](azure-security-disk-encryption-windows.md) et [Activer Azure Disk Encryption pour Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Combien coûte Azure Disk Encryption ?

Il n’y a pas de frais associés au chiffrement des disques de machine virtuelle avec Azure Disk Encryption, mais l’utilisation d’Azure Key Vault entraîne des frais. Pour plus d’informations sur les coûts d’Azure Key Vault, consultez la page sur la [Tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Quels niveaux de machines virtuelles prennent en charge Azure Disk Encryption ?

Azure Disk Encryption est disponible sur les machines virtuelles du niveau standard, y compris les machines virtuelles IaaS de série [A, D, DS, G, GS et F](https://azure.microsoft.com/pricing/details/virtual-machines/). Il est également disponible pour les machines virtuelles avec stockage premium. Il n’est pas disponible sur les machines virtuelles de niveau de base.

## <a name="bkmk_LinuxOSSupport"></a> Quelles sont les distributions Linux prises en charge par Azure Disk Encryption ?

Azure Disk Encryption est pris en charge sur un sous-ensemble de la [distributions Linux approuvées par Azure](../virtual-machines/linux/endorsed-distros.md), qui est lui-même un sous-ensemble de toutes les distributions de Linux server possible.

 ![Diagramme de Venn de serveur distributions Linux qui prennent en charge Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Les distributions de serveur Linux qui ne sont pas approuvées par Azure ne prennent pas en charge Azure Disk Encryption et, de celles qui sont approuvées, uniquement les versions et les distributions suivantes prennent en charge Azure Disk Encryption :

| Distribution Linux | Version | Type de volume pris en charge pour le chiffrement|
| --- | --- |--- |
| Ubuntu | 18,04| Disque de système d’exploitation et de données |
| Ubuntu | 16.04| Disque de système d’exploitation et de données |
| Ubuntu | 14.04.5</br>[avec le noyau Azure mis à jour vers la version 4.15 ou ultérieure](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Disque de système d’exploitation et de données |
| RHEL | 7.6 | Disque de système d’exploitation et de données* |
| RHEL | 7.5 | Disque de système d’exploitation et de données* |
| RHEL | 7.4 | Disque de système d’exploitation et de données* |
| RHEL | 7.3 | Disque de système d’exploitation et de données* |
| RHEL | 7,2 | Disque de système d’exploitation et de données* |
| RHEL | 6.8 | Disque de données* |
| RHEL | 6.7 | Disque de données* |
| CentOS | 7.5 | Disque de système d’exploitation et de données |
| CentOS | 7.4 | Disque de système d’exploitation et de données |
| CentOS | 7.3 | Disque de système d’exploitation et de données |
| CentOS | 7.2n | Disque de système d’exploitation et de données |
| CentOS | 6.8 | Disque de système d’exploitation et de données |
| CentOS | 7.1 | Disque de données |
| CentOS | 7.0 | Disque de données |
| CentOS | 6.7 | Disque de données |
| CentOS | 6.6 | Disque de données |
| CentOS | 6.5 | Disque de données |
| openSUSE | 42.3 | Disque de données |
| SLES | 12-SP4 | Disque de données |
| SLES | 12-SP3 | Disque de données |

> [!NOTE]
> Nouvelle implémentation ADE est pris en charge pour RHEL du système d’exploitation et le disque de données pour les images de paiement à l’utilisation de RHEL7. ADE n’est actuellement pas pris en charge pour les images BYOS de RHEL. Reportez-vous à la [Azure Disk Encryption pour Linux](azure-security-disk-encryption-linux.md) article pour plus d’informations. __

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Comment puis-je commencer à utiliser Azure Disk Encryption ?

Pour commencer, consultez la [Vue d’ensemble d’Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Puis-je chiffrer des volumes de démarrage et de données avec Azure Disk Encryption ?

Oui, vous pouvez chiffrer des volumes de démarrage et de données pour les machines virtuelles IaaS Windows et Linux. Pour les machines virtuelles Windows, vous ne pouvez pas chiffrer les données sans d’abord chiffrer le volume du système d’exploitation. Pour les machines virtuelles Linux, il est possible de chiffrer le volume de données sans devoir d’abord chiffrer le volume du système d’exploitation. Une fois que vous avez chiffré le volume du système d’exploitation sous Linux, la désactivation du chiffrement sur un volume du système d’exploitation pour les machines virtuelles IaaS Linux n’est pas prise en charge. Pour les machines virtuelles Linux dans un groupe identique, seul le volume de données peut être chiffré.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Puis-je chiffrer un volume démonté avec Azure Disk Encryption ?

Non, Azure Disk Encryption chiffre uniquement les volumes montés.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>La rotation des secrets ou des clés de chiffrement ?

Pour faire pivoter des secrets, appelez simplement la même commande que vous avez utilisé initialement pour activer le chiffrement de disque, en spécifiant un autre coffre de clé. Pour faire pivoter la clé de chiffrement à clé, appelez la même commande que vous avez utilisé initialement pour activer le chiffrement de disque, en spécifiant la nouvelle clé de chiffrement. 

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Comment ajouter ou supprimer une clé de chiffrement à clé si je n’en utilisez pas à l’origine ?

Pour ajouter une clé de chiffrement à clé, appelez la commande de l’activer à nouveau en transmettant le paramètre de clé de chiffrement à clé. Pour supprimer une clé de chiffrement à clé, appelez la commande de l’activer à nouveau sans le paramètre de clé de chiffrement à clé.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Azure Disk Encryption vous permet-il d’apporter votre propre clé « BYOK » ?

Oui, vous pouvez fournir vos propres clés de chiffrement principales. Ces clés sont sauvegardées dans Azure Key Vault, le magasin de clés d’Azure Disk Encryption. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement principales, consultez [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Puis-je utiliser une clé de chiffrement principale créée par Azure ?

Oui, vous pouvez utiliser Azure Key Vault pour générer la clé de chiffrement principale pour une utilisation du chiffrement de disques Azure. Ces clés sont sauvegardées dans Azure Key Vault, le magasin de clés d’Azure Disk Encryption. Pour plus d’informations sur la clé de chiffrement principale, consultez [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Puis-je utiliser un service de gestion de clés local ou HSM pour sauvegarder les clés de chiffrement ?

Vous ne pouvez pas utiliser le service de gestion de clés local ou HSM pour protéger les clés de chiffrement avec Azure Disk Encryption. Vous pouvez uniquement utiliser le service Azure Key Vault pour sauvegarder les clés de chiffrement. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement principales, consultez [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quels sont les composants requis pour configurer Azure Disk Encryption ?

Azure Disk Encryption a des prérequis. Consultez l’article [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) pour créer un nouveau coffre de clés ou en configurer un existant pour l’accès au chiffrement de disque, et pour activer le chiffrement et protéger des secrets et des clés. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement principales, consultez [Vue d’ensemble d’Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quels sont les prérequis pour configurer Azure Disk Encryption avec une application Azure AD (version précédente) ?

Azure Disk Encryption a des prérequis. Consultez l’article [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) pour créer une application Azure Disk Encryption, créer un nouveau coffre de clés ou en configurer un existant pour l’accès au chiffrement de disque, et pour activer le chiffrement et protéger des secrets et des clés. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement principales, consultez [Vue d’ensemble d’Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>La technologie Azure Disk Encryption utilisant une application Azure AD (version précédente) est-elle toujours pris en charge ?
Oui. Le chiffrement de disque à l’aide d’une application Azure AD est toujours pris en charge. Cependant, lors du chiffrement de nouvelles machines virtuelles, il est recommandé d’utiliser la nouvelle méthode plutôt que le chiffrement avec une application Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Puis-je migrer des machines virtuelles qui ont été chiffrées avec une application Azure AD pour les chiffrer sans application Azure AD ?
  Actuellement, il n’existe pas de chemin d’accès de migration directe pour des machines chiffrées avec une application Azure AD qui permette le chiffrement sans application Azure AD. Il n’y a pas non plus de chemin d'accès direct du chiffrement sans application Azure AD au chiffrement avec une application AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Quelle version d’Azure PowerShell est prise en charge par Azure Disk Encryption ?

Utilisez la dernière version du Kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version d’[Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption n’est *pas* pris en charge par le Kit SDK Azure version 1.1.0.

> [!NOTE]
> Il est déconseillé d’utiliser l’extension de la version préliminaire d’Azure Disk Encryption pour Linux. Pour plus d’informations, consultez [Dépréciation de l’extension d’aperçu du chiffrement de disque Azure pour les machines virtuelles IaaS Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Puis-je appliquer Azure Disk Encryption sur mon image Linux personnalisée ?

Vous ne pouvez pas appliquer Azure Disk Encryption sur votre image Linux personnalisée. Seules les images Linux de la galerie pour les distributions prises en charge indiquées précédemment sont prises en charge. Les images Linux personnalisées ne sont actuellement pas prises en charge.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Puis-je appliquer des mises à jour sur une machine virtuelle Red Hat Linux à partir d’une mise à jour Yum ?

Oui, vous pouvez effectuer une mise à jour yum sur une machine virtuelle Red Hat Linux.  Pour plus d’informations, consultez [gestion des packages Linux derrière un pare-feu](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Quel est le workflow de chiffrement de disque Azure recommandé pour Linux ?

Le workflow suivant est recommandé pour obtenir les meilleurs résultats sur Linux :
* Démarrer à partir de l’image de la galerie de stock non modifié correspondant à la distribution et à la version du système d’exploitation requises
* Sauvegarder tous les lecteurs montés qui seront chiffrés.  Cette sauvegarde permet la récupération en cas d’échec, par exemple, si la machine virtuelle est redémarrée avant la fin du chiffrement.
* Chiffrer (opération qui peut prendre plusieurs heures voire même plusieurs jours selon les caractéristiques de machine virtuelle et la taille de tous les disques de données attachés)
* Personnaliser et ajouter des logiciels à l’image selon les besoins.

Si ce flux de travail n’est pas possible, s’appuyer sur le [Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) au niveau de la couche du compte de stockage de la plateforme peut être une alternative au chiffrement de disque complet avec dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>À quoi correspond le disque « volume Bek » ou « /mnt/azure_bek_disk » ?

« volume Bek » pour Windows ou « /mnt/azure_bek_disk» pour Linux est un volume de données local qui stocke de façon sécurisée les clés de chiffrement pour les machines virtuelles Azure IaaS chiffrées.
> [!NOTE]
> Vous ne devez pas supprimer ni modifier le contenu de ce disque. Vous ne devez pas non plus démonter ce disque, car les clés de chiffrement qui y sont stockées sont nécessaires pour effectuer les opérations de chiffrement sur les machines virtuelles IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Quelle méthode de chiffrement Azure Disk Encryption utilise-t-il ?

Sous Windows, ADE utilise la méthode de chiffrement BitLocker AES256 (AES256WithDiffuser sur les versions antérieures à Windows Server 2012). Sur Linux, ADE utilise la valeur par défaut aes-xts-plain64 déchiffrer avec une clé principale de volume de 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Si j’utilise EncryptFormatAll et si je spécifie tous les types de volume, les données de tous les lecteurs de données déjà chiffrés seront-elles effacées ?
Non, les données ne sont pas effacées des lecteurs de données déjà chiffrés à l’aide d’Azure Disk Encryption. De même qu’EncryptFormatAll n’a pas chiffré à nouveau le lecteur du système d’exploitation, il ne chiffre pas à nouveau le lecteur de données déjà chiffré. Pour plus d’informations, consultez les [critères pour EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Système de fichiers XFS est prise en charge ?
Volumes XFS sont pris en charge pour le chiffrement de disque de données uniquement avec le EncryptFormalAll. Cela remet en forme le volume, l’effacement de toutes les données précédemment il. Pour plus d’informations, consultez les [critères pour EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Puis-je sauvegarder et restaurer une machine virtuelle chiffrée ? 

Sauvegarde Azure fournit un mécanisme permettant de sauvegarder et restaurer chiffrée de la machine virtuelle dans le même abonnement et région.  Pour obtenir des instructions, consultez [sauvegarder et restaurer des machines virtuelles chiffrées avec sauvegarde Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).  Restauration d’une machine virtuelle chiffrée dans une autre région n’est pas pris en charge actuellement.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Où puis-je poser des questions ou envoyer des commentaires ?

Vous pouvez poser vos questions ou envoyer vos commentaires sur le [forum Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a fourni les réponses aux questions les plus courantes concernant Azure Disk Encryption. Pour plus d'informations sur ce service, consultez les articles suivants :

- [Vue d’ensemble d’Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Appliquer le chiffrement de disque dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Chiffrement des données au repos Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
