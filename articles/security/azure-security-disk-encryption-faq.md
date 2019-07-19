---
title: FAQ - Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article offre des réponses au forum aux questions sur Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 98acc7f6dd5ec7cf3702bbcbe60e2739732512e2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294911"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>FAQ Azure Disk Encryption pour machines virtuelles IaaS

Cet article offre des réponses au forum aux questions (FAQ) sur Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux. Pour plus d’informations sur ce service, consultez [Azure Disk Encryption pour machines virtuelles IaaS Windows et Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Où se trouve Azure Disk Encryption en disponibilité générale (GA) ?

Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux est en disponibilité générale dans toutes les régions publiques Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quelles expériences utilisateur sont disponibles avec Azure Disk Encryption ?

La disponibilité générale (GA) Azure Disk Encryption prend en charge les modèles Azure Resource Manager, Azure PowerShell et Azure CLI. Les différentes expériences utilisateur vous donnent de la souplesse. Vous disposez de trois options différentes pour activer le chiffrement des disques sur vos machines virtuelles IaaS. Pour plus d’informations sur l’expérience utilisateur et des instructions étape par étape disponibles dans Azure Disk Encryption, consultez [Activer Azure Disk Encryption pour Windows](azure-security-disk-encryption-windows.md) et [Activer Azure Disk Encryption pour Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Combien coûte Azure Disk Encryption ?

Il n’y a pas de frais associés au chiffrement des disques de machine virtuelle avec Azure Disk Encryption, mais l’utilisation d’Azure Key Vault entraîne des frais. Pour plus d’informations sur les coûts d’Azure Key Vault, consultez la page sur la [Tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Comment puis-je commencer à utiliser Azure Disk Encryption ?

Pour commencer, consultez la [Vue d’ensemble d’Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Puis-je chiffrer des volumes de démarrage et de données avec Azure Disk Encryption ?

Oui, vous pouvez chiffrer des volumes de démarrage et de données pour les machines virtuelles IaaS Windows et Linux. Pour les machines virtuelles Windows, vous ne pouvez pas chiffrer les données sans d’abord chiffrer le volume du système d’exploitation. Pour les machines virtuelles Linux, il est possible de chiffrer le volume de données sans devoir d’abord chiffrer le volume du système d’exploitation. Une fois que vous avez chiffré le volume du système d’exploitation sous Linux, la désactivation du chiffrement sur un volume du système d’exploitation pour les machines virtuelles IaaS Linux n’est pas prise en charge. Pour les machines virtuelles Linux dans un groupe identique, seul le volume de données peut être chiffré.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Puis-je chiffrer un volume démonté avec Azure Disk Encryption ?

Non, Azure Disk Encryption chiffre uniquement les volumes montés.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Comment effectuer la rotation des secrets ou des clés de chiffrement ?

Pour effectuer la rotation des secrets, appelez simplement la même commande que celle utilisée initialement pour activer le chiffrement de disque, en spécifiant un autre coffre de clés. Pour effectuer la rotation de la clé de chiffrement principale, appelez la même commande que celle utilisée initialement pour activer le chiffrement de disque, en spécifiant le nouveau chiffrement principal. 

>[!WARNING]
> - Si vous avez déjà utilisé [Azure Disk Encryption avec une application Azure AD](azure-security-disk-encryption-prerequisites-aad.md) avec des informations d’identification Azure AD pour chiffrer cette machine virtuelle, vous devrez continuer à utiliser cette option. Vous ne pouvez pas utiliser [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) sur cette machine virtuelle chiffrée car un tel scénario n’est pas pris en charge. Autrement dit, l’utilisation d’une autre application que l’application AAD pour cette machine virtuelle n’est pas encore prise en charge.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Comment ajouter ou supprimer une clé de chiffrement principale si je n’en utilisais pas à l’origine ?

Pour ajouter une clé de chiffrement principale, appelez la commande enable à nouveau en y passant le paramètre de clé de chiffrement principale. Pour supprimer une clé de chiffrement principale, appelez la commande enable à nouveau sans le paramètre de clé de chiffrement principale.

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
> L’extension Linux en préversion d’Azure Disk Encryption « Microsoft.OSTCExtension.AzureDiskEncryptionForLinux » est déconseillée. Cette extension a été publiée pour la préversion d’Azure Disk Encryption. Vous ne devriez pas utiliser la préversion de l’extension dans votre déploiement de test ou de production.

> Pour les scénarios de déploiement d’Azure Resource Manager (ARM) par exemple, où vous avez besoin de déployer l’extension Azure Disk Encryption pour que la machine virtuelle Linux puisse activer le chiffrement sur votre machine virtuelle IaaS Linux, vous devez utiliser l’extension Azure Disk Encryption prise en charge dans les environnements de production « Microsoft.Azure.Security.AzureDiskEncryptionForLinux ».

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Puis-je appliquer Azure Disk Encryption sur mon image Linux personnalisée ?

Vous ne pouvez pas appliquer Azure Disk Encryption sur votre image Linux personnalisée. Seules les images Linux de la galerie pour les distributions prises en charge indiquées précédemment sont prises en charge. Les images Linux personnalisées ne sont actuellement pas prises en charge.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Puis-je appliquer des mises à jour sur une machine virtuelle Red Hat Linux à partir d’une mise à jour Yum ?

Oui, vous pouvez effectuer une mise à jour Yum sur une machine virtuelle Red Hat Linux.  Pour plus d’informations, consultez [Gestion des packages Linux derrière un pare-feu](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

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

Sous Windows, ADE utilise la méthode de chiffrement BitLocker AES256 (AES256WithDiffuser sur les versions antérieures à Windows Server 2012). Sous Linux, ADE utilise la valeur de déchiffrement par défaut aes-xts-plain64 avec une clé principale de volume 256 bits.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Si j’utilise EncryptFormatAll et si je spécifie tous les types de volume, les données de tous les lecteurs de données déjà chiffrés seront-elles effacées ?
Non, les données ne sont pas effacées des lecteurs de données déjà chiffrés à l’aide d’Azure Disk Encryption. De même qu’EncryptFormatAll n’a pas chiffré à nouveau le lecteur du système d’exploitation, il ne chiffre pas à nouveau le lecteur de données déjà chiffré. Pour plus d’informations, consultez les [critères pour EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Le système de fichiers XFS est-il pris en charge ?
Les volumes XFS sont pris en charge pour le chiffrement de disque de données uniquement avec EncryptFormatAll. Ce paramètre reformate le volume, effaçant toutes les données qui s’y trouvaient précédemment. Pour plus d’informations, consultez les [critères pour EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Puis-je sauvegarder et restaurer une machine virtuelle chiffrée ? 

Sauvegarde Azure fournit un mécanisme permettant de sauvegarder et de restaurer les machines virtuelles chiffrées dans le même abonnement et la même région.  Pour obtenir les instructions correspondantes, consultez [Sauvegarder et restaurer des machines virtuelles chiffrées avec Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  La restauration d’une machine virtuelle chiffrée dans une autre région n’est pas prise en charge actuellement.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Où puis-je poser des questions ou envoyer des commentaires ?

Vous pouvez poser vos questions ou envoyer vos commentaires sur le [forum Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a fourni les réponses aux questions les plus courantes concernant Azure Disk Encryption. Pour plus d'informations sur ce service, consultez les articles suivants :

- [Vue d’ensemble d’Azure Disk Encryption](azure-security-disk-encryption-overview.md)
- [Appliquer le chiffrement de disque dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Chiffrement des données au repos Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
