---
title: Prérequis d’Azure Disk Encryption avec Azure AD App (version précédente)
description: Cet article décrit les prérequis pour l’utilisation de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b0e3e683b2c103bc7f9b6812115e2e7a5d871034
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829994"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption avec Azure AD (version précédente)

**La nouvelle version d’Azure Disk Encryption vous évite de fournir un paramètre d’application Azure AD pour activer le chiffrement de disque de machine virtuelle. Avec la nouvelle version, vous n’êtes plus obligé de fournir les informations d’identification Azure AD lors de l’étape d’activation du chiffrement. Avec la nouvelle version, toutes les nouvelles machines virtuelles doivent être chiffrées sans les paramètres d’application Azure AD. Pour obtenir des instructions sur l’activation du chiffrement de disque de machine virtuelle avec la nouvelle version, consultez [Azure Disk Encryption pour les machines virtuelles Linux](disk-encryption-overview.md). Les machines virtuelles déjà chiffrées avec les paramètres d’application Azure AD sont toujours prises en charge et doivent continuer à être gérées avec la syntaxe AAD.**

Cet article fournit des compléments à l’article [Azure Disk Encryption pour les machines virtuelles Linux](disk-encryption-overview.md), avec des exigences et des prérequis supplémentaires pour Azure Disk Encryption avec Azure AD (version précédente).

Les informations contenues dans ces sections restent les mêmes :

- [Machines virtuelles et systèmes d’exploitation pris en charge](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Configuration requise supplémentaire pour les machines virtuelles](disk-encryption-overview.md#additional-vm-requirements)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="networking-and-group-policy"></a>Mise en réseau et stratégie de groupe

**Pour l’activation de la fonctionnalité Azure Disk Encryption utilisant l’ancienne syntaxe des paramètres AAD, les machines virtuelles IaaS doivent répondre aux exigences de configuration de point de terminaison de réseau suivantes :** 
  - Pour obtenir un jeton afin de se connecter à votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Azure Active Directory \[Login.microsoftonline.com\].
  - Pour écrire les clés de chiffrement dans votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Key Vault.
  - La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extensions Azure et au compte de stockage Azure qui héberge les fichiers de disque dur virtuel.
  -  Si votre stratégie de sécurité limite l’accès à Internet à partir des machines virtuelles Azure, vous pouvez résoudre l’URI ci-dessus et configurer une règle spécifique pour autoriser les connexions sortantes vers les adresses IP. Pour plus d’informations, consultez l’article [Azure Key Vault derrière un pare-feu](../../key-vault/key-vault-access-behind-firewall.md).
  - Sur Windows, si le protocole TLS 1.0 a été explicitement désactivé et que la version .NET n’a pas été mise à jour vers la version 4.6 ou une version supérieure, la modification suivante du Registre active ADE pour sélectionner la version la plus récente du protocole TLS :
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Stratégie de groupe :**
 - La solution Azure Disk Encryption utilise le protecteur de clé externe BitLocker pour les machines virtuelles IaaS Windows. Pour les machines virtuelles jointes à un domaine, n’envoyez (push) pas de stratégies de groupe qui appliquent des protecteurs de Module de plateforme sécurisée (TPM). Pour en savoir plus sur la stratégie de groupe pour « Autoriser BitLocker sans module de plateforme sécurisée compatible », consultez la rubrique [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1) (Référence de stratégie de groupe BitLocker).

-  La stratégie BitLocker sur les machines virtuelles jointes à un domaine avec stratégie de groupe personnalisée doit inclure le paramètre suivant : [Configurer le stockage par les utilisateurs des informations de récupération BitLocker -> Autoriser une clé de récupération de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). En cas d'incompatibilité des paramètres de la stratégie de groupe personnalisée de BitLocker, Azure Disk Encryption échouera. Sur les machines dont le paramètre de stratégie était incorrect, il peut être nécessaire d’appliquer la nouvelle stratégie, de forcer la mise à jour de cette dernière (gpupdate.exe /force), puis de procéder à un redémarrage.  

## <a name="encryption-key-storage-requirements"></a>Conditions requises pour le stockage des clés de chiffrement  

Azure Disk Encryption exige Azure Key Vault pour contrôler et gérer les clés et les secrets de chiffrement de disque. Votre coffre de clés et vos machines virtuelles doivent se trouver dans la même région et le même abonnement Azure.

Pour plus d’informations, consultez [Création et configuration d’un coffre de clés pour Azure Disk Encryption avec Azure AD (version précédente)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Étapes suivantes

- [Création et configuration d’un coffre de clés pour Azure Disk Encryption avec Azure AD (version précédente)](disk-encryption-key-vault-aad.md).
- [Activer Azure Disk Encryption avec Azure AD App sur des machines virtuelles Linux (version précédente)](disk-encryption-linux-aad.md)
- [Script d’interface CLI des prérequis Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script PowerShell des prérequis Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)