---
title: Fichier Include
description: Fichier Include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 03/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 99766ca4cc9d77927030f81cff6bb9c009874f89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Cœurs par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10 000 |
| [Coadministrateurs](../articles/billing-add-change-azure-subscription-administrator.md) par abonnement |200 |200 |
| [Comptes de stockage](../articles/storage/common/storage-create-storage-account.md) par région et par abonnement <sup>2</sup> |200 |250 |
| [Services cloud](../articles/cloud-services/cloud-services-choose-me.md) par abonnement |20 |200 |
| [Réseaux locaux](http://msdn.microsoft.com/library/jj157100.aspx) par abonnement |10 |500 |
| Serveurs de bases de données SQL par abonnement |6. |150 |
| Serveurs DNS par abonnement |9 |100 |
| Adresses IP réservées par abonnement |20 |100 |
| Certificats de services hébergés par abonnement |199 |199 |
| [Groupes d'affinités](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) par abonnement |256 |256 |


<sup>1</sup>Les très petites instances comptent comme un cœur pour la limite de cœurs, malgré l'utilisation d'un cœur partiel.

<sup>2</sup>La limite du compte de stockage comprend les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage dans une même région, sollicitez le [Support Azure](https://azure.microsoft.com/support/faq/)pour obtenir une assistance. L’équipe Azure Storage examinera votre cas d’entreprise et pourra approuver jusqu’à 250 comptes de stockage. 

