---
title: Présentation d’Azure Disk Encryption
description: Cet article fournit une vue d’ensemble d’Azure Disk Encryption.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/17/2019
ms.custom: seodec18
ms.openlocfilehash: a67f19f0823827dad74e7aba15a92d696fbf580b
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304264"
---
# <a name="azure-disk-encryption-overview"></a>Vue d’ensemble d’Azure Disk Encryption

Azure Disk Encryption vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Il utilise la fonctionnalité [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume des disques de système d’exploitation et des disques de données des machines virtuelles Azure. Elle est également intégrée à [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) pour vous aider à contrôler et à gérer les secrets et les clés de chiffrement de disque, et garantit que toutes les données des disques des machines virtuelles sont chiffrées au repos pendant qu’elles résident dans le Stockage Azure. Azure Disk Encryption pour les machines virtuelles Windows et Linux est en disponibilité générale dans toutes les régions publiques Azure et régions Azure Government pour les machines virtuelles standard et les machines virtuelles avec Stockage Premium Azure. 

Si vous utilisez Azure Security Center, vous recevez une alerte dès lors que certaines de vos machines virtuelles ne sont pas chiffrées. Les alertes indiquent un niveau de gravité élevé et recommandent de chiffrer ces machines virtuelles.

![Alerte de chiffrement de disque Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Certaines recommandations peuvent impliquer une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul et déboucher sur des coûts de licence ou d’abonnement supplémentaires.


## <a name="encryption-scenarios"></a>Scénarios de chiffrement

Avec Azure Disk Encryption, vous pouvez répondre aux exigences de sécurité et de conformité de l’organisation en sécurisant vos machines virtuelles Azure au repos à l’aide d’une technologie de chiffrement standard. Vous pouvez également configurer des machines virtuelles pour qu’elles démarrent sous des stratégies et des clés contrôlées par le client (BYOK), et auditer l’utilisation de ces clés dans votre coffre de clés.

Azure Disk Encryption prend en charge les scénarios client suivants :

* Activation et désactivation du chiffrement sur les nouvelles machines virtuelles créées à partir des images Azure Gallery prises en charge
* Activation et désactivation du chiffrement sur des machines virtuelles existantes qui s’exécutent dans Azure
* Activation et désactivation du chiffrement sur les nouvelles machines virtuelles Windows créées à partir de disques durs virtuels préchiffrés et de clés de chiffrement
* Activation et désactivation du chiffrement sur des groupes de machines virtuelles identiques Windows
* Activation et désactivation du chiffrement sur les lecteurs de données pour des groupes de machines virtuelles identiques Linux
* Activation et désactivation du chiffrement des machines virtuelles avec disque managé
* Mise à jour des paramètres de chiffrement d’une machine virtuelle de Stockage (Premium ou non) chiffrée existante
* Sauvegarde et restauration de machines virtuelles chiffrées
* Scénarios BYOE (Bring Your Own Encryption) et BYOK (Bring Your Own Key), dans lesquels les clients utilisent leurs propres clés de chiffrement et les stockent dans un coffre de clés Azure.

Il prend également en charge les scénarios de machines virtuelles suivants quand ils sont activés dans Microsoft Azure :

* Intégration avec Azure Key Vault.
* [Machines virtuelles de niveau standard](https://azure.microsoft.com/pricing/details/virtual-machines/) qui remplissent les [conditions de mémoire minimale requises](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes) 
* Activation du chiffrement sur les machines virtuelles Windows et Linux, le disque managé et les machines virtuelles des groupes identiques à partir des images Azure Gallery prises en charge
* Désactivation du chiffrement sur les lecteurs de système d’exploitation et de données pour les machines virtuelles Windows, les machines virtuelles des groupes identiques et les machines virtuelles avec disque managé
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles Linux, les machines virtuelles des groupes identiques et les machines virtuelles avec disque managé
* Activation du chiffrement sur les machines virtuelles qui exécutent le système d’exploitation client Windows
* Activation du chiffrement sur les volumes avec chemins de montage
* Activation du chiffrement sur les machines virtuelles Linux configurées avec entrelacement de disques (RAID) à l’aide de mdadm
* Activation du chiffrement sur les machines virtuelles Linux qui utilisent LVM pour les disques de données
* Activation du chiffrement sur les disques de système d’exploitation et de données des machines virtuelles Linux

   > [!NOTE]
   > Le chiffrement de lecteur de système d’exploitation n’est pas pris en charge pour toutes les distributions Linux. Pour plus d’informations, consultez [Systèmes d’exploitation pris en charge par Azure Disk Encryption : Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Activation du chiffrement sur les machines virtuelles Windows configurées avec des espaces de stockage Windows à compter de Windows Server 2016 Les espaces de stockage direct (S2D) ne sont pas encore pris en charge.
* Sauvegarde et restauration de machines virtuelles chiffrées pour les scénarios KEK (Key Encryption Key) et autres

Azure Disk Encryption ne fonctionne pas pour les scénarios, fonctionnalités et technologies suivants :

* Chiffrement de machines virtuelles de niveau de base ou de machines virtuelles créées par le biais de la méthode de création de machine virtuelle classique
* Désactivation du chiffrement sur un lecteur de système d’exploitation ou de données d’une machine virtuelle Linux quand le lecteur de système d’exploitation est chiffré
* Chiffrement du lecteur de système d’exploitation pour des groupes de machines virtuelles identiques
* Chiffrement de machines virtuelles Windows configurées avec des systèmes RAID logiciels
* Chiffrement d’images personnalisées sur des machines virtuelles Linux
* Intégration à un système de gestion de clés local
* Azure Files (système de fichiers partagés).
* NFS (système de gestion de fichiers en réseau).
* Volumes dynamiques.

## <a name="encryption-features"></a>Fonctionnalités de chiffrement

Quand vous activez et déployez Azure Disk Encryption pour des machines virtuelles Azure, vous pouvez configurer les fonctionnalités suivantes afin qu’elles soient activées :

* Chiffrement du volume de système d’exploitation pour protéger le volume de démarrage au repos dans votre stockage
* Chiffrement des volumes de données pour protéger les volumes de données au repos dans votre stockage
* Désactivation du chiffrement sur les lecteurs de système d’exploitation et de données pour les machines virtuelles Windows
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles Linux (à condition que le lecteur de système d’exploitation ne soit pas chiffré)
* Protection des secrets et clés de chiffrement dans votre abonnement Azure Key Vault
* Création de rapports concernant l’état du chiffrement des machines virtuelles chiffrées
* Suppression des paramètres de configuration de chiffrement de disque sur la machine virtuelle
* Sauvegarde et restauration des machines virtuelles chiffrées à l’aide du service Sauvegarde Azure

Azure Disk Encryption pour les machines virtuelles pour Windows et Linux comprend :

* [L’extension de chiffrement de disque pour Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [L’extension de chiffrement de disque pour Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Les applets de commande PowerShell pour le chiffrement de disque](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Les applets de commande Azure CLI pour le chiffrement de disque](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Les modèles de chiffrement de disque Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Le chiffrement des disques de machine virtuelle avec Azure Disk Encryption n’occasionne pas de coûts supplémentaires. Les [tarifs standard de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) s’appliquent au coffre de clés utilisé pour stocker les clés de chiffrement. 

## <a name="encryption-workflow"></a>Workflow de chiffrement

Pour activer le chiffrement de disque pour les machines virtuelles Windows et Linux, effectuez les étapes suivantes :

1. Acceptez l’activation du chiffrement de disque avec le modèle Resource Manager Azure Disk Encryption, les cmdlets PowerShell ou Azure CLI et spécifiez la configuration de chiffrement.

   * Pour le scénario de disque dur virtuel chiffré par le client, téléchargez le disque dur virtuel chiffré dans votre compte de stockage et le support de clé de chiffrement dans votre coffre de clés. Ensuite, fournissez la configuration de chiffrement pour activer le chiffrement sur une nouvelle machine virtuelle.
   * Dans le cas des nouvelles machines virtuelles IaaS créées à partir d’images de la galerie prises en charge et des machines virtuelles existantes en cours d’exécution dans Azure, indiquez la configuration de chiffrement pour activer le chiffrement.

1. Accordez l’accès à la plateforme Azure pour lire le support de clé de chiffrement (clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) dans le coffre de clés afin d’activer le chiffrement sur la machine virtuelle.

1. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés, et configure vos machines virtuelles chiffrées.

   ![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Workflow de déchiffrement
Pour désactiver le chiffrement de disque pour les machines virtuelles, effectuez les étapes générales suivantes :

1. Choisissez de désactiver le chiffrement (déchiffrement) sur une machine virtuelle en cours d’exécution dans Azure et spécifiez la configuration du déchiffrement. La désactivation peut s’effectuer avec le modèle Resource Manager Azure Disk Encryption, les cmdlets PowerShell ou Azure CLI.

   Cette étape désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle Windows en cours d’exécution. Comme nous l’avons vu dans la section précédente, la désactivation du chiffrement du disque de système d’exploitation n’est pas prise en charge pour Linux. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur des machines virtuelles Linux tant que le disque du système d’exploitation n’est pas chiffré.

1. Azure met à jour le modèle de service de machine virtuelle, et la machine virtuelle est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos.

   > [!NOTE]
   > L’opération de désactivation du chiffrement ne supprime ni votre coffre de clés ni le support de clé de chiffrement (clés de chiffrement BitLocker pour les systèmes Windows ou phrase secrète pour Linux).
   >
   > La désactivation du chiffrement de disque de système d’exploitation n’est pas prise en charge pour Linux. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur les machines virtuelles Linux.
   >
   > La désactivation du chiffrement de disque de données n’est pas prise en charge pour Linux si le lecteur de système d’exploitation est chiffré.


## <a name="encryption-workflow-previous-release"></a>Flux de travail de chiffrement (version précédente)

La nouvelle version d’Azure Disk Encryption évite d’avoir à indiquer un paramètre d’application Azure Active Directory (Azure AD) pour activer le chiffrement de disque de machine virtuelle. Avec la nouvelle version, il n’est plus nécessaire de fournir des informations d’identification Azure AD à l’étape d’activation du chiffrement. Toutes les nouvelles machines virtuelles doivent alors être chiffrées sans les paramètres d’application Azure AD. Les machines virtuelles déjà chiffrées avec les paramètres d’application Azure AD, toujours prises en charge, doivent être gérées avec la syntaxe Azure AD. Pour activer le chiffrement de disque pour les machines virtuelles Windows et Linux (version précédente), effectuez les étapes suivantes :

1. Choisissez un scénario de chiffrement parmi les scénarios listés dans la section [Scénarios de chiffrement](#encryption-scenarios).

1. Acceptez l’activation du chiffrement de disque avec le modèle Resource Manager Azure Disk Encryption, les cmdlets PowerShell ou Azure CLI et spécifiez la configuration de chiffrement.

   * Pour le scénario de disque dur virtuel chiffré par le client, téléchargez le disque dur virtuel chiffré dans votre compte de stockage et le support de clé de chiffrement dans votre coffre de clés. Ensuite, fournissez la configuration de chiffrement pour activer le chiffrement sur une nouvelle machine virtuelle.
   * Dans le cas des nouvelles machines virtuelles IaaS créées sur la Place de marché Azure et des machines virtuelles existantes en cours d’exécution dans Azure, indiquez la configuration de chiffrement pour activer le chiffrement.

1. Accordez l’accès à la plateforme Azure pour lire le support de clé de chiffrement (clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) dans le coffre de clés afin d’activer le chiffrement sur la machine virtuelle.

1. Indiquez l’identité de l’application Azure AD pour écrire le support de clé de chiffrement dans votre coffre de clés. Cette étape active le chiffrement sur la machine virtuelle pour les scénarios mentionnés à l’étape 2.

1. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés, et configure vos machines virtuelles chiffrées.


## <a name="terminology"></a>Terminologie
Le tableau suivant définit certains termes courants utilisés dans la documentation d’Azure Disk Encryption :

| Terminologie | Définition |
| --- | --- |
| Azure AD | Un compte [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) est utilisé pour authentifier, stocker et extraire des secrets d’un coffre de clés. |
| Azure Key Vault | Key Vault est un service de gestion de clés de chiffrement basé sur des modules de sécurité matériels validés FIPS (Federal Information Processing Standard). Ces normes permettent de protéger vos clés de chiffrement et vos secrets sensibles. Pour plus d’informations, voir la documentation [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est une technologie de chiffrement de volume Windows qui permet d’activer le chiffrement de disque sur des machines virtuelles Windows. |
| BEK | Les clés de chiffrement BitLocker (BEK) servent à chiffrer les volumes de données et le volume de démarrage du système d’exploitation. Elles sont sauvegardées sous forme de secrets dans un coffre de clés. |
| D’Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) est optimisé pour gérer et administrer des ressources Azure en ligne de commande.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) est le sous-système de chiffrement de disque transparent Linux utilisé pour activer le chiffrement de disque sur les machines virtuelles Linux. |
| Clé de chiffrement principale (KEK) | Clé asymétrique (RSA 2048) que vous pouvez utiliser pour protéger ou encapsuler le secret. Vous pouvez fournir une clé protégée par un module de sécurité matériel ou une clé protégée par logiciel. Pour plus d’informations, voir la documentation [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Applets de commande PowerShell | Pour plus d’informations, voir [Cmdlets Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, consultez les [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

