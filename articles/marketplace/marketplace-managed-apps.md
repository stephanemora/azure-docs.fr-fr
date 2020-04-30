---
title: 'Applications Azure : Guide de publication d’une offre d’application managée | Place de marché Azure'
description: Cet article décrit les conditions requises pour publier une application managée sur la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084870"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Applications Azure : Exigences relatives à la publication d’une offre d’application managée

Cet article explique la configuration requise pour le type d’offre d’application managée, qui est une façon de publier une offre d’application Azure dans la Place de marché Azure. Les applications managées sont des offres de transaction qui sont déployées et facturées via la Place de marché Azure. L’appel à l’action qu’un utilisateur voit est « Obtenir maintenant ».

Utilisez le type d’offre d’application managée lorsque les conditions suivantes sont requises :

- Vous déployez une solution basée sur un abonnement pour votre client à l’aide d’une machine virtuelle ou d’une solution IaaS complète.
- Vous ou votre client exigez que la solution soit gérée par un partenaire.

>[!NOTE]
>Il peut par exemple s’agir d’un intégrateur système ou d’un fournisseur de service géré.  

## <a name="managed-application-offer"></a>Offre d’application managée

|Spécifications |Détails  |
|---------|---------|
|Déployée dans l’abonnement Azure d’un client | Les applications managées doivent être déployées dans l’abonnement du client et peuvent être gérées par un tiers. |
|Facturation et mesure    |  Les ressources seront approvisionnées dans l’abonnement Azure du client. Les machines virtuelles avec paiement à l’utilisation (PAYGO) feront l’objet de transactions avec le client par le biais de Microsoft et seront facturées dans le cadre de l’abonnement Azure (PAYGO) du client. <br> Dans le cas de BYOL (apportez votre propre licence), Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, mais vous effectuerez la transaction de vos frais de licence logicielle directement avec le client.        |
|Disque dur virtuel compatible avec Azure    |   Les machines virtuelles doivent être basées sur Windows ou Linux.<ul> <ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez [Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez [Créer une offre d’application Azure](./partner-center-portal/create-new-azure-apps-offer.md).</li> </ul> |

>[!NOTE]
> Les applications managées doivent pouvoir être déployées via la Place de marché. Si la communication avec le client constitue un sujet d’inquiétude, nous vous recommandons de joindre les clients intéressés après avoir activé le partage des prospects.  

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible. Pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft, consultez [Fournisseurs de solutions cloud](./cloud-solution-providers.md).

## <a name="next-steps"></a>Étapes suivantes

- Si vous ne l’avez pas déjà fait, [renseignez-vous](https://azuremarketplace.microsoft.com/sell) au sujet de la Place de marché Azure.
- [Connectez-vous à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Créer une offre d’application Azure](./partner-center-portal/create-new-azure-apps-offer.md).
