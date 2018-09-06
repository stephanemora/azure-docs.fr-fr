---
title: Guide de publication d’une offre d’application managée Azure Application
description: Cet article décrit les conditions requises pour publier une application managée sur la place de marché
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: c8ead3dc34faefce0f113dee2074960fddfa11a1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144589"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Applications Azure : Guide de publication d’une offre d’application managée

Une application managée constitue l’un des principaux moyens de publier une solution sur la place de marché. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Il s’agit d’offres de transaction qui sont déployées et facturées via la place de marché. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».

Utilisez le type d’offre Application Azure : application managée lorsque les conditions suivantes sont requises :
- Vous déployez une solution basée sur un abonnement pour votre client à l’aide d’une machine virtuelle ou d’une solution IaaS complète.
- Vous ou votre client exigez que la solution soit gérée par un partenaire.

>[!NOTE]
>Il peut par exemple s’agir d’un intégrateur système ou d’un fournisseur de service géré.  

## <a name="managed-application-offer"></a>Offre d’application managée

|Configuration requise |Détails  |
|---------|---------|
|Déployée dans le cadre de l’abonnement Azure d’un client | Les applications managées doivent être déployées dans l’abonnement du client et peuvent être gérées par une tierce partie | 
|Facturation et mesure    |  Les ressources seront approvisionnées dans l’abonnement Azure du client. Des machines virtuelles de paiement à l’utilisation (PAYGO) feront l’objet de transactions avec le client par le biais de Microsoft, facturées par le biais de l’abonnement (PAYGO) Azure du client 
Dans le cas de BYOL (apportez votre propre licence), tandis que Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, vous effectuerez la transaction de vos frais de licence logicielle directement avec le client        |
|Disque dur virtuel compatible avec Azure    |   Les machines virtuelles doivent être basées sur Windows ou Linux.<ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez la section Créer un disque dur virtuel compatible avec Azure (Linux) à l’adresse [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez la section Créer un disque dur virtuel compatible avec Azure (Windos) à l’adresse [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Les applications managées doivent pouvoir être déployées via la Place de marché. Si la communication avec le client constitue un sujet d’inquiétude, nous vous recommandons de joindre les clients intéressés après avoir activé le partage des prospects.  


## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà fait, 

- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou terminer votre offre
