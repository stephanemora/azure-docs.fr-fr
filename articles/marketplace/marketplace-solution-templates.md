---
title: Guide de publication d’offres de modèle de solution d’applications Azure | Place de marché Azure
description: Cet article décrit les conditions requises pour publier un modèle de solution sur la Place de marché Azure.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288731"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applications Azure : Guide de publication d’offres de modèle de solution

Les modèles de solution constituent l’un des principaux moyens de publier une solution sur la Place de marché. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Utilisez le type d’offre Application Azure : modèle de solution si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, en plus d’une machine virtuelle. Vous pouvez automatiser la mise en service d’une ou plusieurs machines virtuelles à l’aide du type d’offre Applications Azure : modèles de solution. Vous pouvez également approvisionner des ressources réseau et de stockage. Le type d’offre Applications Azure : modèles de solution offre les avantages de l’automatisation pour des machines virtuelles individuelles et des solutions IaaS complètes.

Ces modèles de solution ne sont pas des offres de transaction, mais peuvent être utilisés pour déployer des offres de machines virtuelles payantes facturées via la place de marché commerciale de Microsoft. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».


## <a name="requirements-for-solution-templates"></a>Exigences requises pour les modèles de solution

| **Configuration requise** | **Détails**  |
| ---------------  | -----------  |
|Facturation et mesure    |  Les ressources seront approvisionnées dans l’abonnement Azure du client. Les machines virtuelles avec paiement à l’utilisation (PAYGO) feront l’objet de transactions avec le client par le biais de Microsoft et seront facturées dans le cadre de l’abonnement Azure (PAYGO) du client.  <br/> Dans le cas de BYOL (apportez votre propre licence), tandis que Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, vous effectuerez la transaction de vos frais de licence logicielle directement avec le client.   |
|Disque dur virtuel compatible avec Azure  |   Les machines virtuelles doivent être basées sur Windows ou Linux.  Pour plus d’informations, consultez [Créer un disque dur virtuel compatible avec Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Attribution de l’utilisation de client | L’activation de l’attribution de l’utilisation de client est obligatoire pour tous les modèles de solution publiés dans la Place de marché Azure. Pour plus d’informations sur l’attribution de l’utilisation de client et sur son activation, consultez [Attribution de l’utilisation de client partenaire Azure](./azure-partner-customer-usage-attribution.md).  |
| Utiliser des disques managés | Les [disques managés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) représentent l’option par défaut pour les disques persistants des machines virtuelles IaaS dans Azure. Vous devez utiliser des disques managés dans les modèles de solution. <br> <br> 1. Suivez les [conseils](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) et les [exemples](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) pour l’utilisation de disques managés dans les modèles Azure Resource Manager pour mettre à jour vos modèles de solution. <br> <br> 2. Suivez les instructions ci-dessous pour importer le disque dur virtuel sous-jacent des disques managés dans un compte de stockage pour publier le disque dur virtuel en tant qu’image sur la Place de marché : <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [INTERFACE DE LIGNE DE COMMANDE](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà fait, [inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la Place de marché.

Si vous êtes inscrit et que vous créez une offre ou modifiez une offre existante, connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou finaliser votre offre.
