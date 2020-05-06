---
title: Guide de publication d’une offre de machine virtuelle pour la Place de marché Azure
description: Cet article décrit les conditions requises pour publier une machine virtuelle et un essai logiciel gratuit à déployer à partir de la place de marché.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687499"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guide de publication d’une offre de machine virtuelle

Les images de machine virtuelle constituent l’un des principaux moyens de publier une solution sur la Place de marché Azure. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Il s’agit d’offres de transaction qui sont déployées et facturées via la place de marché. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».

## <a name="free-trial"></a>Version d’évaluation gratuite 

Vous pouvez donner aux utilisateurs la possibilité de tester votre offre en leur proposant des licences logicielles à durée limitée suivant le modèle de facturation BYOL (apportez votre propre licence). 

## <a name="test-drive"></a>Version d’évaluation

Vous déployez une ou plusieurs machines virtuelles par le biais d’applications IaaS (infrastructure as a service) ou SaaS (software as a service). L’avantage de l’option de publication Test drive est l’approvisionnement automatique d’une machine virtuelle ou de la solution complète dirigé par une visite guidée hébergée par un partenaire. Un test drive permet d’évaluer le produit sans frais supplémentaires pour votre client. Votre client n’est pas tenu d’être un client Azure existant pour opter pour l’expérience d’essai. 

Contactez-nous à l’adresse [amp-testdrive](mailto:amp-testdrive@microsoft.com) pour commencer. 

|Spécifications  |Détails |
|---------|---------|
| Vous avez une application de la place de marché   |    Une ou plusieurs machines virtuelles via IaaS ou SaaS.      |

## <a name="interactive-demo"></a>Démonstration interactive

Vous fournissez une expérience guidée de votre solution à vos clients à l’aide d’une démonstration interactive. L’avantage de l’option de publication Démonstration interactive est qu’elle permet d’essayer un produit sans avoir à effectuer une mise en service compliquée de votre solution complexe. 

## <a name="virtual-machine-offer"></a>Offre de machine virtuelle

Utilisez le type d’offre Machine virtuelle si vous déployez une appliance virtuelle dans le cadre de l’abonnement associé à votre client. Les machines virtuelles sont entièrement commercialisables à l’aide des modèles de licence Paiement à l’utilisation ou BYOL (apportez votre propre licence). Microsoft héberge la transaction commerciale et facture votre client pour votre compte. Vous avez ainsi l’avantage utiliser la relation de paiement préférée entre votre client et Microsoft, et notamment un éventuel contrat Entreprise.

> [!NOTE]
> À ce jour, les engagement monétaires associés à un Contrat Entreprise peuvent être utilisés pour l’utilisation par Azure de votre machine virtuelle, mais pas pour vos frais de licence logicielle.  
> 
> [!NOTE]
> Vous pouvez limiter la découverte et le déploiement de votre machine virtuelle à un ensemble spécifique de clients en publiant l’image et les tarifs sous forme d’offre privée. Les offres privées vous permettent de créer des offres exclusives pour vos clients les plus fidèles et de proposer des conditions et des logiciels personnalisés. Les conditions personnalisées vous permettent de présenter différents scénarios, notamment des offres économiques avec des conditions et des tarifs adaptés ainsi qu’un accès en avant-première à des logiciels en version limitée. Les offres privées vous permettent de proposer des tarifs ou des produits spécifiques à un ensemble limité de clients en créant une référence SKU avec ces détails.  
> *   Pour plus d’informations sur les offres privées, visitez la page Offres privées sur la Place de marché Azure à l’adresse [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Condition requise | Détails |  
|:--- |:--- | 
| Facturation et mesure | Votre machine virtuelle doit prendre en charge la facturation mensuelle BYOL ou avec paiement à l’utilisation. |  
| Disque dur virtuel compatible avec Azure | Les machines virtuelles doivent être basées sur Windows ou Linux. <ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez [Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez [Créer un disque dur virtuel compatible avec Azure](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](./cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, 

- [En savoir plus](https://azuremarketplace.microsoft.com/sell) sur le marketplace.

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous à Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Créer une machine virtuelle](./partner-center-portal/azure-vm-create-offer.md).
