---
title: Vue d’ensemble d’Azure Disk Encryption pour les machines virtuelles IaaS | Microsoft Docs
description: Cet article donne une vue d’ensemble de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 0e81a48c1215e8590f90c42aee0861e6fda3db8e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392726"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption pour les machines virtuelles IaaS 
Microsoft Azure a pour objectif de préserver la confidentialité et la souveraineté de vos données, et de vous permettre de contrôler vos données hébergées Azure à l’aide de différentes technologies avancées pour chiffrer, contrôler et gérer les clés de chiffrement, puis contrôler et auditer l’accès aux données. Ce contrôle donne aux clients Azure la possibilité de choisir la solution qui répond le mieux à leurs besoins métier. Cet article vous présente une nouvelle solution technologique, « Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux », pour protéger et sauvegarder vos données, afin de répondre aux engagements de votre organisation en matière de sécurité et de conformité. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Vue d’ensemble
Azure Disk Encryption est une fonctionnalité qui vous permet de chiffrer les disques de vos machines virtuelles IaaS Windows et Linux. Elle utilise la fonctionnalité standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume des disques de système d’exploitation et de données. La solution est intégrée à [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) pour faciliter le contrôle et la gestion des clés et des secrets de chiffrement des disques. Elle garantit également que toutes les données sur les disques de vos machines virtuelles sont chiffrées au repos dans votre stockage Azure.

Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux est en **disponibilité générale** dans toutes les régions publiques Azure et les régions AzureGov pour les machines virtuelles standard et les machines virtuelles avec Stockage Premium. La solution de gestion Azure Disk Encryption répond aux besoins professionnels suivants :

* Les machines virtuelles IaaS sont sécurisées au repos via une technologie de chiffrement standard permettant de répondre aux exigences de sécurité et de conformité des organisations.
* Les machines virtuelles IaaS démarrent par le biais de stratégies et de clés contrôlées par les clients qui peuvent auditer leur utilisation dans le coffre de clés.

> [!NOTE]
> Certaines recommandations peuvent entraîner une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul débouchant sur des coûts de licence ou d’abonnement supplémentaires.


## <a name="encryption-scenarios"></a>Scénarios de chiffrement
Azure Disk Encryption prend en charge les scénarios client suivants :

* Activation du chiffrement sur de nouvelles machines virtuelles IaaS Windows créées à partir de disques durs virtuels préchiffrés et de clés de chiffrement 
* Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir d’images de la galerie Azure prises en charge
* Activation du chiffrement sur des machines virtuelles IaaS existantes et fonctionnant dans Azure
* Désactivation du chiffrement sur les machines virtuelles IaaS Windows.
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux
* Activation du chiffrement des machines virtuelles avec disque managé
* Mise à jour des paramètres de chiffrement d’une machine virtuelle de stockage non Premium et Premium chiffrée existante
* Sauvegarde et restauration de machines virtuelles chiffrées

La solution prend en charge les scénarios de machines virtuelles IaaS suivants lorsqu’ils sont activés dans Microsoft Azure :

* Prise en main d’Azure Key Vault
* Machines virtuelles de niveau standard : [Machines virtuelles IaaS des séries A, D, DS, G, GS, F, etc.](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * Les [machines virtuelles Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) dans ces niveaux doivent disposer d’une mémoire minimale de 7 Go
* Activation du chiffrement sur les machines virtuelles IaaS Windows et Linux et les machines virtuelles de disques gérés créées à partir d’images de la galerie Azure prises en charge
* Désactivation du chiffrement des lecteurs de système d’exploitation et de données pour des machines virtuelles IaaS Windows et des machines virtuelles avec disque managé
* Désactivation du chiffrement des lecteurs de données pour des machines virtuelles IaaS Linux et des machines virtuelles avec disque managé
* Activation du chiffrement sur les machines virtuelles IaaS exécutant le système d’exploitation du client Windows.
* Activation du chiffrement sur les volumes avec chemins d’accès de montage.
* Activation du chiffrement sur des machines virtuelles Linux configurées avec une agrégation de disques (RAID) utilisant mdadm
* Activation du chiffrement sur des machines virtuelles Linux en utilisant LVM pour les disques de données
* Activation du chiffrement sur les disques de système d’exploitation et de données des machines virtuelles Linux 
* Activation du chiffrement sur les machines virtuelles Windows configurées avec des espaces de stockage
* Mise à jour des paramètres de chiffrement d’une machine virtuelle de stockage non Premium et Premium chiffrée existante
* Sauvegarde et restauration de machines virtuelles chiffrées, pour les scénarios KEK et non-KEK (KEK - clé de chiffrement de clé)
* Toutes les régions publiques Azure et AzureGov sont prises en charge

La solution ne prend pas en charge les scénarios, fonctionnalités et technologies suivants :

* Machines virtuelles IaaS de niveau de base
* Désactivation du chiffrement sur un lecteur de système d’exploitation pour les machines virtuelles IaaS Linux
* Désactivation du chiffrement sur un lecteur de données lorsque le lecteur du système d’exploitation est chiffré pour les machines virtuelles Iaas Linux
* Machines virtuelles IaaS créées à l’aide de la méthode classique de création de machines virtuelles
* L’activation du chiffrement sur les images client personnalisées de machines virtuelles IaaS Linux n’est pas prise en charge.
* Intégration à votre service de gestion de clés local
* Azure Files (système de partage de fichiers), NFS (Network File System), volumes dynamiques et machines virtuelles Windows configurées avec des systèmes RAID logiciels

## <a name="encryption-features"></a>Fonctionnalités de chiffrement
Lorsque vous activez et déployez Azure Disk Encryption pour les machines virtuelles IaaS Azure, les fonctionnalités suivantes sont activées en fonction de la configuration fournie :

* Chiffrement du volume du système d’exploitation pour protéger le volume de démarrage au repos dans votre espace de stockage
* Chiffrement des volumes de données pour protéger les volumes de données au repos dans votre espace de stockage
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles IaaS Windows
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux (à condition que le lecteur de système d’exploitation NE SOIT PAS chiffré)
* Sauvegarde des clés et clés secrètes de chiffrement dans votre abonnement Key Vault
* Création de rapports concernant l’état du chiffrement des machines virtuelles IaaS chiffrées
* Suppression des paramètres de configuration de chiffrement de disque à partir de la machine virtuelle IaaS
* Sauvegarde et restauration des machines virtuelles chiffrées à l’aide du service Sauvegarde Azure

La solution Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux comprend :

* L’extension de chiffrement de disque pour Windows.
* L’extension de chiffrement de disque pour Linux.
* Les applets de commande PowerShell pour le chiffrement de disque.
* Les applets de commande de l’interface de ligne de commande Azure pour le chiffrement de disque.
* Les modèles Azure Resource Manager pour le chiffrement de disque.

La solution Azure Disk Encryption est prise en charge sur les machines virtuelles IaaS exécutant les systèmes d’exploitation Windows ou Linux. Pour plus d’informations sur les systèmes d’exploitation pris en charge, consultez l’article [Prérequis](azure-security-disk-encryption-prerequisites.md).

> [!NOTE]
> Le chiffrement des disques de machine virtuelle avec Azure Disk Encryption n’entraîne pas de coûts supplémentaires. La [tarification standard de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) s’applique au coffre de clés utilisé pour stocker les clés de chiffrement. 

## <a name="encryption-workflow"></a>Workflow de chiffrement
Pour activer le chiffrement de disque pour les machines virtuelles Windows et Linux, effectuez les étapes suivantes :

1. Choisissez un scénario de chiffrement parmi les scénarios de chiffrement précédents.
2. Optez pour l’activation du chiffrement de disque via le modèle Resource Manager de chiffrement de disque Azure, les applets de commande PowerShell ou les commandes CLI et spécifiez la configuration de chiffrement.

   * Pour le scénario de disque dur virtuel chiffré par le client, téléchargez le disque dur virtuel chiffré dans votre compte de stockage et le support de clé de chiffrement dans votre coffre de clés. Ensuite, fournissez la configuration de chiffrement pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.
   * Pour les nouvelles machines virtuelles créées à partir de la Place de marché Azure et les machines virtuelles existantes en cours d’exécution dans Azure, fournissez la configuration de chiffrement pour activer le chiffrement sur la machine virtuelle IaaS.

3. Accordez l’accès à la plateforme Azure pour lire le support de clé de chiffrement (systèmes de clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) du coffre de clés et activez le chiffrement sur la machine virtuelle IaaS.

4. Fournissez l’identité d’application Azure Active Directory (Azure AD) pour écrire le support de clé de chiffrement dans votre coffre de clés. Cette opération active le chiffrement sur la machine virtuelle IaaS pour les scénarios mentionnés à l’étape 2.

5. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés, et configure vos machines virtuelles chiffrées.

 ![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Workflow de déchiffrement
Pour désactiver le chiffrement de disque pour les machines virtuelles IaaS, suivez les étapes de haut niveau suivantes :

1. Choisissez de désactiver le chiffrement (déchiffrement) sur une machine virtuelle IaaS en cours d’exécution dans Azure et spécifiez la configuration du déchiffrement. Vous pouvez désactiver via le modèle Resource Manager Azure Disk Encryption, les applets de commande PowerShell ou Azure CLI.

 Cette étape désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle IaaS Windows en cours d’exécution. Toutefois, comme mentionné dans la section précédente, la désactivation du chiffrement du disque de système d’exploitation pour Linux n’est pas prise en charge. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur les machines virtuelles Linux tant que le disque du système d’exploitation n’est pas chiffré.
2. Azure met à jour le modèle de service de machine virtuelle et la machine virtuelle Iaas est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos.

> [!NOTE]
> La désactivation du chiffrement ne supprime ni votre coffre de clés ni le support de clé de chiffrement (clés de chiffrement BitLocker pour Windows ou phrase secrète pour Linux).
 > La désactivation du chiffrement de disque de système d’exploitation pour Linux n’est pas prise en charge. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur les machines virtuelles Linux.
La désactivation du chiffrement des disques de données pour Linux n’est pas prise en charge si le lecteur de système d’exploitation est chiffré.

## <a name="terminology"></a>Terminologie
Reportez-vous au tableau de terminologie suivant pour comprendre certains des termes couramment utilisés par cette technologie :

| Terminologie | Définition |
| --- | --- |
| Azure AD | Azure AD est [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Un compte Azure AD est utilisé pour l’authentification, le stockage et l’extraction des secrets d’un coffre de clés. |
| Azure Key Vault | Key Vault est un service de gestion de clés de chiffrement basé sur des modules de sécurité matériels FIPS (Federal Information Processing Standards), qui vous permet de protéger vos clés de chiffrement et des secrets sensibles. Pour plus d’informations, consultez la documentation relative à [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est une technologie de chiffrement de volume Windows qui permet d’activer le chiffrement de disque sur des machines virtuelles IaaS Windows. |
| BEK | Les clés de chiffrement BitLocker servent à chiffrer le volume de démarrage du système d’exploitation et les volumes de données. Les clés BitLocker sont sauvegardées dans un coffre de clés en tant que clés secrètes. |
| Interface de ligne de commande | Voir [Interface de ligne de commande Azure](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) est le sous-système de chiffrement de disque transparent Linux utilisé pour activer le chiffrement de disque sur les machines virtuelles IaaS Linux. |
| KEK | La clé de chiffrement à clé est la clé asymétrique (RSA 2048) que vous pouvez utiliser pour protéger ou encapsuler la clé secrète. Vous pouvez fournir une clé protégée par un module de sécurité matériel ou une clé protégée par logiciel. Pour plus d’informations, consultez la documentation [d’Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Applets de commande PS | Voir [Applets de commande Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Prérequis pour Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
