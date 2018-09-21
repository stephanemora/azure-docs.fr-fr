---
title: Fichier Include
description: Fichier Include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: c6ef868d80e628f9120acc0775179e89ccd03674
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979143"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Processeurs virtuels par [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) <sup>1</sup> |20 |10 000 |
| [Coadministrateurs](../articles/billing-add-change-azure-subscription-administrator.md) par abonnement |200 |200 |
| [Comptes de stockage](../articles/storage/common/storage-quickstart-create-account.md) par région et par abonnement <sup>2</sup> |200 |250 |
| [Services cloud](../articles/cloud-services/cloud-services-choose-me.md) par abonnement |20 |200 |
| [Réseaux locaux](http://msdn.microsoft.com/library/jj157100.aspx) par abonnement |10 |500 |
| Serveurs de bases de données SQL par abonnement |6. |200 |
| Serveurs DNS par abonnement |9 |100 |
| Adresses IP réservées par abonnement |20 |100 |
| Certificats de services hébergés par abonnement |199 |199 |
| [Groupes d'affinités](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) par abonnement |256 |256 |


<sup>1</sup>Les très petites instances comptent comme un processeur virtuel pour la limite de processeurs virtuels, malgré l’utilisation d’un cœur de processeur partiel.

<sup>2</sup>La limite du compte de stockage comprend les comptes de stockage Standard et Premium. Si vous avez besoin de plus de 200 comptes de stockage dans une région donnée, sollicitez le [Support Azure](https://azure.microsoft.com/support/faq/) pour obtenir une assistance. L’équipe Stockage Azure examinera le cas de votre entreprise et pourra approuver jusqu’à 250 comptes de stockage pour une région donnée. 

