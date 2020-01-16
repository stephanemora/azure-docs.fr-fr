---
title: Guide de publication d’une offre d’application managée Azure Application
description: Cet article décrit les conditions requises pour publier une application managée sur la place de marché
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: MaggiePucciEvans
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: evansma
ms.openlocfilehash: 764212ac148b336b07d29c29a72314c5d889d47c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934648"
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

|Spécifications |Détails  |
|---------|---------|
|Déployée dans l’abonnement Azure d’un client | Les applications managées doivent être déployées dans l’abonnement du client et peuvent être gérées par un tiers. | 
|Facturation et mesure    |  Les ressources seront approvisionnées dans l’abonnement Azure du client. Les machines virtuelles avec paiement à l’utilisation (PAYGO) feront l’objet de transactions avec le client par le biais de Microsoft et seront facturées dans le cadre de l’abonnement Azure (PAYGO) du client. <br> Dans le cas de BYOL (apportez votre propre licence), Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, mais vous effectuerez la transaction de vos frais de licence logicielle directement avec le client.        |
|Disque dur virtuel compatible avec Azure    |   Les machines virtuelles doivent être basées sur Windows ou Linux.<ul> <ul> <li>Pour plus d’informations sur la création d’un disque dur virtuel Linux, consultez [Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Pour plus d’informations sur la création d’un disque dur virtuel Windows, consultez [Créer un disque dur virtuel compatible avec Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Les applications managées doivent pouvoir être déployées via la Place de marché. Si la communication avec le client constitue un sujet d’inquiétude, nous vous recommandons de joindre les clients intéressés après avoir activé le partage des prospects.  

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](./cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas déjà fait, 

- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,

- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com) pour créer ou terminer votre offre.
