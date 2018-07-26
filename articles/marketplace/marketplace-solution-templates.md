---
title: Guide de publication d’offres de modèle de solution d’applications Azure
description: Cet article décrit les conditions requises pour publier un modèle de solution sur la place de marché
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
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057881"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applications Azure : Guide de publication d’offres de modèle de solution

Les modèles de solution constituent l’un des principaux moyens de publier une solution sur la place de marché. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Il s’agit d’offres de transaction qui sont déployées et facturées via la place de marché. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».

Utilisez le type d’offre Application Azure : modèle de solution si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, en plus d’une simple machine virtuelle. Vous pouvez automatiser la mise en service d’une ou plusieurs machines virtuelles à l’aide du type d’offre Applications Azure : modèles de solution. Vous pouvez également approvisionner des ressources réseau et de stockage. Le type d’offre Applications Azure : modèles de solution offre les avantages de l’automatisation pour des machines virtuelles individuelles et des solutions IaaS complètes.

## <a name="requirements-for-solution-templates"></a>Exigences requises pour les modèles de solution

|Configuration requise |Détails  |
|---------|---------|
|Facturation et mesure    |  Les ressources seront approvisionnées dans l’abonnement Azure du client. Des machines virtuelles de paiement à l’utilisation (PAYGO) feront l’objet de transactions avec le client par le biais de Microsoft, facturées par le biais de l’abonnement (PAYGO) Azure du client 
Dans le cas de BYOL (apportez votre propre licence), tandis que Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, vous effectuerez la transaction de vos frais de licence logicielle directement avec le client        |
|Disque dur virtuel compatible avec Azure    |   Les machines virtuelles doivent être basées sur Windows ou Linux.<ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez la section Créer un disque dur virtuel compatible avec Azure (Linux) à l’adresse [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez la section Créer un disque dur virtuel compatible avec Azure (Windos) à l’adresse [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà fait, 

- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou terminer votre offre
