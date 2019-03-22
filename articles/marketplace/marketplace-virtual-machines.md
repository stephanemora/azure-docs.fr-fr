---
title: Guide de publication d’une offre de machine virtuelle pour la Place de marché Azure
description: Cet article décrit les conditions requises pour publier une machine virtuelle et un essai logiciel gratuit à déployer à partir de la place de marché.
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
ms.openlocfilehash: eb49d0fe610a78b8d1a407644a206fddaba0a67a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116213"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guide de publication d’une offre de machine virtuelle

Les images de machine virtuelle constituent l’un des principaux moyens de publier une solution sur la Place de marché Azure. Utilisez ce guide pour comprendre les exigences propres à cette offre. 

Il s’agit d’offres de transaction qui sont déployées et facturées via la place de marché. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».

## <a name="free-trial"></a>Version d’évaluation gratuite 

Vous pouvez donner aux utilisateurs la possibilité de tester votre offre en leur proposant des licences logicielles à durée limitée suivant le modèle de facturation BYOL (apportez votre propre licence). Voici les exigences requises pour déployer cette offre : 

|Configuration requise  |Détails  |
|---------|---------|
|Période d’essai gratuite et évaluation     |   Vos clients peuvent utiliser votre application gratuitement pendant une durée limitée. Ils n’ont pas de frais de licence ou d’abonnement à payer pour votre offre. Vos clients ne sont pas obligés de payer le produit ou service interne Microsoft sous-jacent. Toutes les options de la version d’évaluation sont déployées sur votre abonnement Azure. Vous avez le contrôle exclusif de la gestion et de l’optimisation des coûts. Vous pouvez choisir un essai gratuit ou une démonstration interactive. Quel que soit votre choix, votre essai gratuit doit permettre aux clients de tester gratuitement votre offre pendant une durée définie.|
|Solution facile à configurer et prête à l’emploi    |  Votre application doit être facile et rapide à configurer.       |
|Disponibilité/Temps d’activité    |    La durée de fonctionnement de votre plateforme ou application SaaS doit être au minimum égale à 99,9 % du temps total.     |
|Azure Active Directory     |    Votre offre doit permettre l’authentification unique fédérée Azure Active Directory (Azure AD) avec activation du consentement.     |

## <a name="test-drive"></a>Version d’évaluation

Vous déployez une ou plusieurs machines virtuelles par le biais d’applications IaaS (infrastructure as a service) ou SaaS (software as a service). L’avantage de l’option de publication Test drive est l’approvisionnement automatique d’une machine virtuelle ou de la solution complète dirigé par une visite guidée hébergée par un partenaire. Un test drive permet d’évaluer le produit sans frais supplémentaires pour votre client. Votre client n’est pas tenu d’être un client Azure existant pour opter pour l’expérience d’essai. 

Contactez-nous à l’adresse [amp-testdrive](mailto:amp-testdrive@microsoft.com) pour commencer. 

|Configuration requise  |Détails |
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

| Prérequis | Détails |  
|:--- |:--- | 
| Facturation et mesure | Votre machine virtuelle doit prendre en charge la facturation mensuelle BYOL ou avec paiement à l’utilisation. |  
| Disque dur virtuel compatible avec Azure | Les machines virtuelles doivent être basées sur Windows ou Linux. <ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez [Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez [Créer un disque dur virtuel compatible avec Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Cloud fournisseurs de solutions partenaires canal opt-in est désormais disponible.  Consultez [les fournisseurs de solutions de Cloud](./cloud-solution-providers.md) canaux du partenaire pour plus d’informations sur le marketing de votre offre via le CSP de Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, 

- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Offre de machines virtuelles](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer).
