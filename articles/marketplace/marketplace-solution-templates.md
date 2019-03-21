---
title: Guide de publication d’offres de modèle de solution d’applications Azure
description: Cet article décrit les conditions requises pour publier un modèle de solution sur la Place de marché Azure.
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
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: bbf9ab56bd7e070e40e2454a69f0a5e313597f14
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215368"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applications Azure : Guide de publication d’offres de modèle de solution

Les modèles de solution constituent l’un des principaux moyens de publier une solution sur la Place de marché. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Utilisez le type d’offre Application Azure : modèle de solution si votre solution nécessite une automatisation supplémentaire du déploiement et de la configuration, en plus d’une machine virtuelle. Vous pouvez automatiser la mise en service d’une ou plusieurs machines virtuelles à l’aide du type d’offre Applications Azure : modèles de solution. Vous pouvez également approvisionner des ressources réseau et de stockage. Le type d’offre Applications Azure : modèles de solution offre les avantages de l’automatisation pour des machines virtuelles individuelles et des solutions IaaS complètes.

Ces modèles de solution sont des offres de type Transaction qui sont déployées et facturées via la Place de marché. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».


## <a name="requirements-for-solution-templates"></a>Exigences requises pour les modèles de solution

| **Configuration requise** | **Détails**  |
| ---------------  | -----------  |
|Facturation et mesure    |  Les ressources seront approvisionnées dans l’abonnement Azure du client. Les machines virtuelles avec paiement à l’utilisation (PAYGO) feront l’objet de transactions avec le client par le biais de Microsoft, facturées dans le cadre de l’abonnement (PAYGO) Azure du client.  <br/> Dans le cas de BYOL (apportez votre propre licence), tandis que Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, vous effectuerez la transaction de vos frais de licence logicielle directement avec le client.   |
|Disque dur virtuel compatible avec Azure  |   Les machines virtuelles doivent être basées sur Windows ou Linux.  Pour plus d’informations, consultez [Créer un disque dur virtuel compatible avec Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Attribution de l’utilisation de client | L’activation de l’attribution de l’utilisation de client est obligatoire pour tous les modèles de solution publiés dans la Place de marché Azure. Pour plus d’informations sur l’attribution de l’utilisation de client et sur son activation, consultez [Attribution de l’utilisation de client partenaire Azure](./azure-partner-customer-usage-attribution.md).  |
|  |  |

## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà fait, [inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la Place de marché.

Si vous êtes inscrit et que vous créez une offre ou modifiez une offre existante, connectez-vous au [Portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou finaliser votre offre.
