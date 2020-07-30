---
title: Problèmes courants lors de la création d’un disque dur virtuel (FAQ)
description: Questions fréquentes sur les problèmes couramment rencontrés lors de la création d’un disque dur virtuel.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: d717d93b616e8acd296c89322b7277cda5ade3a3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323993"
---
# <a name="common-issues-during-vhd-creation"></a>Problèmes couramment rencontrés lors de la création d’un disque dur virtuel

Ces questions fréquentes couvrent les problèmes courants que vous pouvez rencontrer lors de la création d’un disque dur virtuel pour votre offre de machine virtuelle Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Comment créer une machine virtuelle à partir du portail Azure à l’aide d’un disque dur virtuel dans le stockage Premium ?

Pour le moment, la Place de marché Microsoft Azure ne prend pas en charge la création d’offres de machine virtuelle à partir d’images situées dans le stockage managé ou à partir du stockage Premium Azure. Pour plus d’informations, consultez [Vue d’ensemble des disques managés Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Est-ce que je peux utiliser des machines virtuelles de 2e génération pour les offres ?

Non, seuls les disques durs virtuels de 1re génération sont pris en charge. Toutefois, nous travaillons actuellement avec l’équipe de la plateforme Microsoft Azure pour étudier la prise en charge des machines virtuelles de 2e génération. Pour plus d’informations, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="how-do-i-change-the-name-of-the-host"></a>Comment modifier le nom de l’hôte ?

Désolé... Cette migration est impossible. Une fois la machine virtuelle créée, les utilisateurs (y compris les propriétaires) ne peuvent plus mettre à jour le nom de l’hôte.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion ?

Ces articles expliquent comment réinitialiser le service Bureau à distance pour les machines virtuelles Windows et Linux :

* [Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Réinitialiser un mot de passe de machine virtuelle Linux ou une clé SSH, corriger la configuration SSH et vérifier la cohérence des disques à l’aide de l’extension VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Comment générer de nouveaux certificats SSH ?

La génération de certificats est expliquée dans [Certification des images de machine virtuelle Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Comment configurer un réseau privé virtuel (VPN) pour qu’il fonctionne avec mes machines virtuelles ?

Si vous utilisez le modèle de déploiement Azure Resource Manager, vous disposez de trois options :

* [Créer une passerelle VPN basée sur des itinéraires à l’aide du Portail Azure](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Créer une passerelle VPN basée sur un itinéraire à l’aide d’Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Créer une passerelle VPN basée sur des itinéraires à l’aide de l’interface CLI](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quelles sont les stratégies de prise en charge de Microsoft pour exécuter des logiciels serveur Microsoft sur des machines virtuelles Azure ?

Pour plus d’informations, consultez [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Des identificateurs uniques sont-ils associés aux machines virtuelles ?

Oui, si elles sont hébergées sur Azure. Azure attribue un identificateur unique, nommé [ID unique de machine virtuelle Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), à chaque nouvelle ressource de machine virtuelle créée. Pour plus d’informations, consultez « ID unique de la machine virtuelle Azure ». Vous pouvez également récupérer cet identificateur via l’[API List](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Dans une machine virtuelle, comment gérer l’extension de script personnalisé lors de la tâche de démarrage ?

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé avec le module Azure PowerShell, sur les modèles Azure Resource Manager et sur les étapes de dépannage des systèmes Windows, consultez [Extension de script personnalisé pour Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Les services et les applications 32 bits sont-ils pris en charge dans la Place de marché Azure ?

En général, non. Les systèmes d’exploitation et services standard pris en charge pour les machines virtuelles Azure sont tous au format 64 bits. Bien que la plupart des systèmes d’exploitation 64 bits prennent en charge les versions 32 bits des applications à des fins de compatibilité descendante, l’utilisation d’applications 32 bits dans le cadre de votre solution de machine virtuelle n’est pas prise en charge et est fortement déconseillée. Vous devez recréer votre application sous la forme d’un projet 64 bits.

Pour plus d’informations, voir les articles suivants :

* [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Exécution d’applications 32 bits)
* [Prise en charge pour les systèmes d’exploitation 32 bits sur des machines virtuelles Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erreur : Le disque dur virtuel est déjà inscrit auprès du référentiel d’images en tant que ressource

Chaque fois que j’essaie de créer une image à partir de mes disques durs virtuels, j’obtiens l’erreur « VHD is already registered with image repository as the resource » (Le disque dur virtuel est déjà inscrit auprès du référentiel d’images en tant que ressource) dans Azure PowerShell. Or, je n’ai pas créé d’image, et je n’ai pas non plus trouvé d’image portant ce nom dans Azure. Comment résoudre ce problème ?

Ce problème se produit généralement si vous avez créé une machine virtuelle à partir d’un disque dur virtuel verrouillé. Vérifiez qu’aucune machine virtuelle n’est allouée à partir de ce disque dur virtuel, puis tentez à nouveau l’opération. Si le problème persiste, ouvrez un ticket de support. Consultez [Prise en charge de l’Espace partenaires](support.md).
