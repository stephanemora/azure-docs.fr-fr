---
title: Vue d’ensemble - Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article donne une vue d’ensemble de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/16/2019
ms.custom: seodec18
ms.openlocfilehash: 66d788aec83e3e57a49b063f2ca80484360f639d
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295281"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption pour les machines virtuelles IaaS

Microsoft Azure s’engage à préserver la confidentialité et la souveraineté de vos données. Azure vous permet de contrôler vos données hébergées sur Azure grâce à une suite de technologies avancées servant à chiffrer, à contrôler et à gérer les clés de chiffrement, de même qu’à contrôler et à auditer l’accès aux données. Ce contrôle donne aux clients Azure la possibilité de choisir la solution qui répond le mieux à leurs besoins métier. Cet article vous présente une solution technologique : Azure Disk Encryption pour machines virtuelles IaaS Windows et Linux. Cette technologie vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Présentation

Azure Disk Encryption est une fonctionnalité permettant de chiffrer des disques de machine virtuelle IaaS Windows et Linux. Disk Encryption s’appuie sur la fonctionnalité standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour assurer le chiffrement de volume des disques de système d’exploitation et de données. La solution est intégrée à [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) pour faciliter le contrôle et la gestion des clés et des secrets de chiffrement des disques. Elle garantit également que toutes les données présentes sur les disques de machines virtuelles sont chiffrées au repos dans votre stockage Azure.

Disk Encryption pour les machines virtuelles IaaS Windows et Linux est en disponibilité générale dans toutes les régions publiques Azure et régions Azure Government pour les machines virtuelles standard et les machines virtuelles avec Stockage Premium Azure. La solution de gestion Disk Encryption répond aux besoins professionnels suivants :

* Les machines virtuelles IaaS sont sécurisées au repos par une technologie de chiffrement standard répondant aux exigences de sécurité et de conformité des organisations.
* Les machines virtuelles IaaS s’exécutent sous des stratégies et des clés contrôlées par le client. Vous pouvez auditer leur utilisation dans votre coffre de clés.

Si vous utilisez Azure Security Center, vous recevez une alerte dès lors que certaines de vos machines virtuelles ne sont pas chiffrées. Les alertes indiquent un niveau de gravité élevé et recommandent de chiffrer ces machines virtuelles.

![Alerte de chiffrement de disque Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Certaines recommandations peuvent impliquer une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul et déboucher sur des coûts de licence ou d’abonnement supplémentaires.


## <a name="encryption-scenarios"></a>Scénarios de chiffrement

La solution Disk Encryption prend en charge les scénarios client suivants :

* Activation du chiffrement sur les nouvelles machines virtuelles IaaS Windows créées à partir de disques durs virtuels préchiffrés et de clés de chiffrement.
* Activation du chiffrement sur les nouvelles machines virtuelles IaaS créées à partir des images Azure Gallery prises en charge.
* Activation du chiffrement sur les machines virtuelles IaaS existantes qui fonctionnent sur Azure.
* Activation du chiffrement sur les groupes de machines virtuelles identiques Windows.
* Activation du chiffrement des lecteurs de données dans les groupes de machines virtuelles identiques Linux.
* Désactivation du chiffrement sur les machines virtuelles IaaS Windows.
* Désactivation du chiffrement des lecteurs de données sur les machines virtuelles IaaS Linux.
* Désactivation du chiffrement sur les groupes de machines virtuelles identiques Windows.
* Désactivation du chiffrement des lecteurs de données sur les groupes de machines virtuelles identiques Linux.
* Activation du chiffrement des machines virtuelles avec disque managé.
* Mise à jour des paramètres de chiffrement d’une machine virtuelle de Stockage (Premium ou non) chiffrée existante.
* Sauvegarde et restauration de machines virtuelles chiffrées.

La solution prend en charge les scénarios de machines virtuelles IaaS suivants lorsqu’ils sont activés dans Microsoft Azure :

* Intégration avec Azure Key Vault.
* Machines virtuelles de niveau Standard : [Machines virtuelles IaaS des séries A, D, DS, G, GS, F, etc.](https://azure.microsoft.com/pricing/details/virtual-machines/). La mémoire requise pour les [machines virtuelles Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) de ces niveaux est de 7 Go au minimum.
* Activation du chiffrement sur les machines virtuelles IaaS Windows et Linux, le disque managé et les machines virtuelles des groupes identiques à partir des images Azure Gallery prises en charge.
* Désactivation du chiffrement des lecteurs de système d’exploitation et de données sur les machines virtuelles IaaS Windows, les machines virtuelles des groupes identiques et les machines virtuelles avec disque managé.
* Désactivation du chiffrement des lecteurs de données sur les machines virtuelles IaaS Linux, les machines virtuelles des groupes identiques et les machines virtuelles avec disque managé.
* Activation du chiffrement sur les machines virtuelles IaaS qui fonctionnent sur le système d’exploitation du client Windows.
* Activation du chiffrement sur les volumes comportant des chemins de montage.
* Activation du chiffrement sur les machines virtuelles Linux configurées avec l’entrelacement de disques (RAID) à l’aide de mdadm.
* Activation du chiffrement sur les machines virtuelles Linux qui utilisent LVM pour les disques de données.
* Activation du chiffrement des disques de système d’exploitation et de données sur les machines virtuelles Linux.

   > [!NOTE]
   > Le chiffrement de lecteur de système d’exploitation n’est pas pris en charge pour toutes les distributions Linux. Pour plus d’informations, voir l’article [FAQ Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
   
* Activer le chiffrement sur les machines virtuelles configurées avec le début d’espaces de stockage Windows dans Windows Server 2016.
* Mise à jour des paramètres de chiffrement sur une machine virtuelle de Stockage (Premium ou non) chiffrée existante.
* Sauvegarde et restauration de machines virtuelles chiffrées pour les scénarios KEK (Key Encryption Key) et autres.
* Toutes les régions publiques Azure et Azure Government sont prises en charge.

La solution ne prend pas en charge les scénarios, fonctionnalités et technologies suivants :

* Machines virtuelles IaaS de niveau de base.
* Désactivation du chiffrement d’un lecteur de système d’exploitation sur les machines virtuelles IaaS Linux.
* Désactivation du chiffrement d’un lecteur de données lorsque le lecteur de système d’exploitation est chiffré sur les machines virtuelles IaaS Linux.
* Le chiffrement de lecteur du système d’exploitation pour l’échelle de machine virtuelle Linux définit.
* Machines virtuelles IaaS créées suivant la méthode classique de création de machines virtuelles.
* Activation du chiffrement des images client personnalisées sur les machines virtuelles IaaS Linux.
* Intégration à votre système de gestion de clés local.
* Azure Files (système de fichiers partagés).
* NFS (système de gestion de fichiers en réseau).
* Volumes dynamiques.
* Machines virtuelles Windows configurées avec des systèmes RAID logiciels.

## <a name="encryption-features"></a>Fonctionnalités de chiffrement

Lorsque vous activez et déployez Disk Encryption pour les machines virtuelles IaaS Azure, les fonctionnalités suivantes sont activées en fonction de la configuration fournie :

* Chiffrement du volume du système d’exploitation pour protéger le volume de démarrage au repos dans votre espace de stockage.
* Chiffrement des volumes de données pour protéger les volumes de données au repos dans votre espace de stockage.
* Désactivation du chiffrement des lecteurs de systèmes d’exploitation et de données sur les machines virtuelles IaaS Windows.
* Désactivation du chiffrement des lecteurs de données sur les machines virtuelles IaaS Linux (à condition que le lecteur de système d’exploitation ne soit pas chiffré).
* Protection des secrets et clés de chiffrement dans votre abonnement Azure Key Vault.
* Signalement de l’état de chiffrement des machines virtuelles IaaS chiffrées.
* Suppression des paramètres de configuration de chiffrement de disque sur la machine virtuelle IaaS.
* Sauvegarde et restauration des machines virtuelles chiffrées à l’aide du service Sauvegarde Azure.

La solution Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux comprend :

* L’extension de chiffrement de disque pour Windows.
* L’extension de chiffrement de disque pour Linux.
* Les cmdlets PowerShell pour le chiffrement de disque.
* Les cmdlets Azure CLI pour le chiffrement de disque.
* Les modèles de chiffrement de disque Azure Resource Manager.

La solution Azure Disk Encryption est prise en charge sur les machines virtuelles IaaS qui exécutent un système d’exploitation Windows ou Linux. Pour plus d’informations sur les systèmes d’exploitation pris en charge, consultez l’article [Prérequis](azure-security-disk-encryption-prerequisites.md).

> [!NOTE]
> Le chiffrement des disques de machine virtuelle avec Azure Disk Encryption n’occasionne pas de coûts supplémentaires. Les [tarifs standard de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) s’appliquent au coffre de clés utilisé pour stocker les clés de chiffrement. 


## <a name="encryption-workflow"></a>Workflow de chiffrement

Pour activer le chiffrement de disque pour les machines virtuelles Windows et Linux, effectuez les étapes suivantes :

1. Choisissez un scénario de chiffrement parmi les scénarios listés dans la section [Scénarios de chiffrement](#encryption-scenarios).

1. Acceptez l’activation du chiffrement de disque avec le modèle Resource Manager Azure Disk Encryption, les cmdlets PowerShell ou Azure CLI et spécifiez la configuration de chiffrement.

   * Pour le scénario de disque dur virtuel chiffré par le client, téléchargez le disque dur virtuel chiffré dans votre compte de stockage et le support de clé de chiffrement dans votre coffre de clés. Ensuite, fournissez la configuration de chiffrement pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.
   * Dans le cas des nouvelles machines virtuelles IaaS créées sur la Place de marché Azure et des machines virtuelles IaaS existantes en cours d’exécution dans Azure, indiquez la configuration de chiffrement pour activer le chiffrement.

1. Accordez l’accès à la plateforme Azure pour lire le support de clé de chiffrement (clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) dans le coffre de clés afin d’activer le chiffrement sur la machine virtuelle IaaS.

1. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés, et configure vos machines virtuelles chiffrées.

   ![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Workflow de déchiffrement
Pour désactiver le chiffrement de disque pour les machines virtuelles IaaS, suivez les étapes de haut niveau suivantes :

1. Choisissez de désactiver le chiffrement (déchiffrement) sur une machine virtuelle IaaS en cours d’exécution dans Azure et spécifiez la configuration du déchiffrement. La désactivation peut s’effectuer avec le modèle Resource Manager Azure Disk Encryption, les cmdlets PowerShell ou Azure CLI.

   Cette étape désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle IaaS Windows en cours d’exécution. Comme nous l’avons vu dans la section précédente, la désactivation du chiffrement du disque de système d’exploitation n’est pas prise en charge pour Linux. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur des machines virtuelles Linux tant que le disque du système d’exploitation n’est pas chiffré.

1. Azure met à jour le modèle de service de machine virtuelle, et la machine virtuelle Iaas est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos.

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

   * Pour le scénario de disque dur virtuel chiffré par le client, téléchargez le disque dur virtuel chiffré dans votre compte de stockage et le support de clé de chiffrement dans votre coffre de clés. Ensuite, fournissez la configuration de chiffrement pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.
   * Dans le cas des nouvelles machines virtuelles IaaS créées sur la Place de marché Azure et des machines virtuelles IaaS existantes en cours d’exécution dans Azure, indiquez la configuration de chiffrement pour activer le chiffrement.

1. Accordez l’accès à la plateforme Azure pour lire le support de clé de chiffrement (clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) dans le coffre de clés afin d’activer le chiffrement sur la machine virtuelle IaaS.

1. Indiquez l’identité de l’application Azure AD pour écrire le support de clé de chiffrement dans votre coffre de clés. Cette étape active le chiffrement sur la machine virtuelle IaaS pour les scénarios mentionnés à l’étape 2.

1. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés, et configure vos machines virtuelles chiffrées.


## <a name="terminology"></a>Terminologie
Le tableau suivant définit certains des termes couramment utilisés dans cette technologie :

| Terminologie | Définition |
| --- | --- |
| Azure AD | Un compte [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) est utilisé pour authentifier, stocker et extraire des secrets d’un coffre de clés. |
| Azure Key Vault | Key Vault est un service de gestion de clés de chiffrement basé sur des modules de sécurité matériels validés FIPS (Federal Information Processing Standard). Ces normes permettent de protéger vos clés de chiffrement et vos secrets sensibles. Pour plus d’informations, voir la documentation [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est une technologie de chiffrement de volume Windows qui permet d’activer le chiffrement de disque sur des machines virtuelles IaaS Windows. |
| BEK | Les clés de chiffrement BitLocker (BEK) servent à chiffrer les volumes de données et le volume de démarrage du système d’exploitation. Elles sont sauvegardées sous forme de secrets dans un coffre de clés. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) est optimisé pour gérer et administrer des ressources Azure en ligne de commande.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) est le sous-système de chiffrement de disque transparent Linux utilisé pour activer le chiffrement de disque sur les machines virtuelles IaaS Linux. |
| KEK | La clé KEK (Key Encryption Key) est la clé asymétrique (RSA 2048) permettant de protéger ou d’encapsuler le secret. Vous pouvez fournir une clé protégée par un module de sécurité matériel ou une clé protégée par logiciel. Pour plus d’informations, voir la documentation [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Applets de commande PowerShell | Pour plus d’informations, voir [Cmdlets Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
