---
title: Problèmes courants lors de la création d’un disque dur virtuel (FAQ) pour la Place de marché Azure
description: Forum aux questions sur la création d’un disque dur virtuel (VHD) et les problèmes associés.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: 988121effb35949cce442ba9458789105e88feec
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273441"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Problèmes courants lors de la création d’un disque dur virtuel (FAQ)

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions fournies dans [Forum aux questions sur la création de VHD](https://aka.ms/VHDcreationIssues) pour gérer vos offres migrées.

Le forum aux questions (FAQ) suivant traite des problèmes courants rencontrés lors de la création d’un disque dur virtuel (VHD) et d’une machine virtuelle. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Comment créer une machine virtuelle à partir du Portail Azure à l’aide du disque dur virtuel qui est chargé vers le stockage Premium ?

Pour le moment, Place de marché Microsoft Azure ne prend pas en charge la création d’offres de machine virtuelle à partir d’images résidant sur le stockage géré ou depuis le Stockage Premium Azure.  Pour plus d’informations sur ces options de stockage, voir la page [Vue d’ensemble d’Azure Disques managés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Peut-on utiliser des machines virtuelles de 2e génération pour les offres ?

Non, seuls les disques durs virtuels de 1re génération sont pris en charge.  Toutefois, nous travaillons actuellement avec l’équipe de la plateforme Microsoft Azure pour étudier la prise en charge des machines virtuelles de 2e génération.  Pour plus d’informations sur les différences, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Comment modifier le nom de l’hôte ?

Ce n’est pas possible.  Une fois la machine virtuelle créée, les utilisateurs (y compris les propriétaires) ne peuvent pas mettre à jour le nom de l’hôte.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion ?

Les articles suivants expliquent comment réinitialiser le service Bureau à distance pour les machines virtuelles Windows et Linux :   

- [Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Réinitialiser un mot de passe de machine virtuelle Linux ou une clé SSH, corriger la configuration SSH et vérifier la cohérence des disques à l’aide de l’extension VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Comment générer de nouveaux certificats SSH ?

La génération de certificats est expliquée dans la section [Create technical assets for a VM offer](./cpp-create-technical-assets.md) (Créer des ressources techniques pour une offre de machine virtuelle) de l’article [Get shared access signature URI for your VM image](./cpp-get-sas-uri.md) (Obtention d’un URI de signature d’accès partagé pour votre image de machine virtuelle).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Comment configurer un réseau privé virtuel (VPN) pour travailler avec mes machines virtuelles ?

Si vous utilisez le modèle de déploiement Azure Resource Manager, vous disposez de trois options communes de configuration d’un VPN :
- [Créer une passerelle VPN basée sur des itinéraires à l’aide du Portail Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Créer une passerelle VPN basée sur un itinéraire à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Créer une passerelle VPN basée sur des itinéraires à l’aide de l’interface CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quelles sont les stratégies de prise en charge de Microsoft pour exécuter des logiciels serveur Microsoft sur des machines virtuelles Azure ?

Ces stratégies de prise en charge sont détaillées dans l’article [Prise en charge du logiciel serveur Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Des identificateurs uniques sont-ils associés aux machines virtuelles ?

Oui, si elles sont hébergées sur Azure.  Azure attribue un identificateur unique, nommé ID unique de machine virtuelle Azure à chaque nouvelle ressource de machine virtuelle créée.  Pour plus d’informations, consultez l’article de blog [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) (ID unique de machine virtuelle Azure).  Vous pouvez également obtenir cet identificateur par programmation via [l’API List](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Dans une machine virtuelle, comment gérer l’extension de script personnalisé lors de la tâche de démarrage ?

L’article suivant explique en détail l’utilisation de l’extension de script personnalisé à l’aide du module Azure PowerShell, des modèles Azure Resource Manager, et détaille également les étapes de résolution de problèmes sur les systèmes Windows : [Extension de script personnalisé pour Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Les services ou les applications 32 bits sont-ils pris en charge dans Place de marché Microsoft Azure ?

En général, non.  Les systèmes d’exploitation et services standard pris en charge pour les machines virtuelles Azure sont tous au format 64 bits.  Toutefois, d’un point de vue technique, la plupart des systèmes d’exploitation 64 bits prennent en charge les versions 32 bits des applications pour assurer la compatibilité descendante.  Mais l’utilisation des applications 32 bits dans le cadre de votre solution de machine virtuelle n’est pas prise en charge et est donc *déconseillée*.  À la place, recompilez votre application sous la forme d’un projet 64 bits.

Pour plus d’informations, consultez les articles suivants :
- [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Exécution d’applications 32 bits)
- [Prise en charge pour les systèmes d’exploitation 32 bits sur des machines virtuelles Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Chaque fois que j’essaie de créer une image à partir de mes disques durs virtuels, j’obtiens l’erreur `.VHD is already registered with image repository as the resource` dans PowerShell. Je n’ai pas créé d’image au préalable et je n’ai trouvé aucune image portant ce nom dans Azure. Comment faire pour résoudre ce problème ?

Ce problème se produit généralement si l’utilisateur a approvisionné une machine virtuelle à partir d’un disque dur virtuel verrouillé.  Vérifiez qu’aucune machine virtuelle n’est allouée à partir de ce disque dur virtuel, puis tentez à nouveau l’opération.  Si le problème persiste, ouvrez un ticket de support, comme expliqué dans [Bénéficier du support dans le Portail Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

