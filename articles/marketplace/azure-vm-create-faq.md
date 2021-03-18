---
title: Questions courantes sur les machines virtuelles dans Place de marché Azure
description: Questions courantes rencontrées lors de la création d’une machine virtuelle dans Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: d045af3b170d585b4bf1f8c57b7ba924c6b30695
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489784"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Questions courantes sur les machines virtuelles dans Place de marché Azure

Ces questions fréquentes couvrent les problèmes courants que vous pouvez rencontrer lors de la création d’une offre de machine virtuelle dans Place de marché Azure.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Comment configurer un réseau privé virtuel (VPN) pour qu’il fonctionne avec mes machines virtuelles ?

Si vous utilisez le modèle de déploiement Azure Resource Manager, vous disposez de trois options :

- [Créer une passerelle VPN basée sur des itinéraires à l’aide du Portail Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Créer une passerelle VPN basée sur un itinéraire à l’aide d’Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Créer une passerelle VPN basée sur des itinéraires à l’aide de l’interface CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quelles sont les stratégies de prise en charge de Microsoft pour exécuter des logiciels serveur Microsoft sur des machines virtuelles Azure ?

Pour plus d’informations, consultez [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Dans une machine virtuelle, comment gérer l’extension de script personnalisé lors de la tâche de démarrage ?

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé avec le module Azure PowerShell, sur les modèles Azure Resource Manager et sur les étapes de dépannage des systèmes Windows, consultez [Extension de script personnalisé pour Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Les services et les applications 32 bits sont-ils pris en charge dans la Place de marché Azure ?

Non. Les systèmes d’exploitation et services standard pris en charge pour les machines virtuelles Azure sont tous au format 64 bits. Bien que la plupart des systèmes d’exploitation 64 bits prennent en charge les versions 32 bits des applications à des fins de compatibilité descendante, l’utilisation d’applications 32 bits dans le cadre de votre solution de machine virtuelle n’est pas prise en charge et est fortement déconseillée. Vous devez recréer votre application sous la forme d’un projet 64 bits.

Pour plus d’informations, voir les articles suivants :

- [Running 32-bit applications](/windows/desktop/WinProg64/running-32-bit-applications) (Exécution d’applications 32 bits)
- [Prise en charge pour les systèmes d’exploitation 32 bits sur des machines virtuelles Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erreur : Le disque dur virtuel est déjà inscrit auprès du référentiel d’images en tant que ressource

Chaque fois que j’essaie de créer une image à partir de mes disques durs virtuels, j’obtiens l’erreur « VHD is already registered with image repository as the resource » (Le disque dur virtuel est déjà inscrit auprès du référentiel d’images en tant que ressource) dans Azure PowerShell. Or, je n’ai pas créé d’image, et je n’ai pas non plus trouvé d’image portant ce nom dans Azure. Comment résoudre ce problème ?

Ce problème se produit généralement si vous avez créé une machine virtuelle à partir d’un disque dur virtuel verrouillé. Vérifiez qu’aucune machine virtuelle n’est allouée à partir de ce disque dur virtuel, puis tentez à nouveau l’opération. Si le problème persiste, ouvrez un ticket de support. Consultez [Prise en charge de l’Espace partenaires](support.md).

## <a name="how-do-i-test-a-hidden-preview-image"></a>Comment tester une image d’aperçu masquée ?

Vous pouvez déployer des images d’aperçu masquées à l’aide de modèles de démarrage rapide.
Pour déployer une image d’aperçu Linux, procédez comme suit : 
1. Accédez à ce [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux), puis sélectionnez « Déployer sur Azure ». Vous accédez ainsi au Portail Azure.
2. Sur le Portail Azure, sélectionnez « Modifier le modèle ».
3. Dans le modèle JSON, recherchez imageReference et mettez à jour les valeurs publisherid, offerid, skuid, et version de l’image. Pour tester l’image d’aperçu, ajoutez « -PREVIEW » à offerid.
 ![image](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Cliquez sur Enregistrer.
5. Renseignez les autres détails. Vérifier et créer



## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de certification de machines virtuelles](azure-vm-create-certification-faq.md)
