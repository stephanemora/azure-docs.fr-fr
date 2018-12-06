---
title: Comprendre votre facture Azure | Microsoft Docs
description: Apprenez à lire et à comprendre votre utilisation et la facturation de votre abonnement Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: alherz
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: cwatson
ms.openlocfilehash: b9b1496c71e61fce815d225310c8beb57e8f9b19
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584568"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendre votre facture Microsoft Azure
Pour comprendre votre facture Azure, comparez-la avec le fichier détaillé sur l’utilisation quotidienne et les rapports de gestion des coûts dans le portail Azure.

Cet article ne s’applique pas aux clients Azure disposant d’un Contrat Entreprise (clients EA). Si vous êtes client Contrat Entreprise, consultez la section [Comprendre la facture des clients Azure ayant un Contrat Entreprise](billing-understand-your-bill-ea.md).  

## <a name="charges"></a>Passer en revue vos frais

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Si vous souhaitez plus d’informations sur des frais figurant sur votre facture, vous pouvez comparer l’utilisation et les coûts avec le fichier d’utilisation ou avec le portail Azure.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Option 1 : Comparer l’utilisation et les coûts avec le fichier d’utilisation

Le fichier CSV sur l’utilisation détaillée indique vos frais par période de facturation et l’utilisation quotidienne. Pour accéder au fichier, consultez [Obtenir votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md).

Vos frais d’utilisation sont affichés au niveau du compteur. Les termes suivants ont la même signification sur la facture et sur le fichier sur l’utilisation détaillée. Par exemple, le cycle de facturation de la facture est identique à la période de facturation indiquée dans le fichier relatif à l’utilisation détaillée.

 | Facture (PDF) | Utilisation détaillée (CSV)|
 | --- | --- |
|Cycle de facturation | Période de facturation |
 |Nom |Catégorie du compteur |
 |type |Sous-catégorie du compteur |
 |Ressource |Nom du compteur |
 |Région |Région du compteur |
 |Consommé |Quantité consommée |
 |Inclus |Quantité incluse |
 |Facturable |Quantité de dépassement |

La section **Frais d’utilisation** de votre facture affiche la valeur totale de chaque compteur consommé pendant la période de facturation. Par exemple, la capture d’écran suivante montre les frais d’utilisation du service Azure Scheduler.

![Frais d’utilisation indiqués sur la facture](./media/billing-understand-your-bill/1.png)

La section **Relevé** de votre fichier CSV sur l’utilisation détaillée indique les mêmes frais. Le montant *Consommé* et la *Valeur* correspondent à la facture.

![Frais d’utilisation sur le fichier CSV](./media/billing-understand-your-bill/2.png)

Pour voir le détail de ces frais tous les jours, accédez à la section **Utilisation quotidienne** du fichier CSV. Filtrez sur « Scheduler » sous *Catégorie du compteur*. Vous pouvez voir les jours où le compteur a été utilisé et la quantité consommée. Les informations *Ressource* et *Groupe de ressources* sont également répertoriées à des fins de comparaison. La somme des valeurs *Consommé* doit être égale à ce qui est indiqué sur la facture.

![Section Utilisation quotidienne dans le fichier CSV](./media/billing-understand-your-bill/3.png)

Pour obtenir le coût par jour, multipliez les montants *Consommé* par la valeur *Tarif* dans la section **Relevé**.

Pour plus d'informations, consultez les rubriques suivantes :

- [Comprendre votre facture Azure](billing-understand-your-invoice.md)
- [Comprendre votre utilisation détaillée d’Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-with-the-azure-portal"></a>Option 2 : Comparer l’utilisation et les coûts avec le portail Azure

Le portail Azure peut également vous aider à vérifier vos frais. Pour obtenir une vue d’ensemble rapide de l’utilisation et des frais facturés, consultez les graphiques de gestion des coûts.

1. Dans le portail Azure, accédez à [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Sélectionnez votre abonnement > **Analyse du coût**. 
1. Filtrez sur **Intervalle de temps**.
1. Pour poursuivre l’exemple précédent, vous voyez les frais d’utilisation du service Azure Scheduler.

   ![Vue de l’analyse des coûts dans le portail Azure](./media/billing-understand-your-bill/4.png)

1. Sélectionnez cette ligne pour voir le détail quotidien des coûts.

   ![Affichage de l’historique des coûts dans le portail Azure](./media/billing-understand-your-bill/5.png)

Pour en savoir plus, cliquez sur [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md#costs).

## <a name="external"></a>Services externes facturés séparément

Les services externes, ou frais de la Place de marché, concernent les ressources créées par des fournisseurs de logiciels tiers. Ces ressources sont disponibles sur la Place de marché Azure. Par exemple, un pare-feu Barracuda est une ressource de la Place de marché Azure offerte par un tiers. Tous les frais du pare-feu et les compteurs correspondants apparaissent en tant que frais de services externes.

Les frais de services externes sont facturés séparément. Les frais ne s’affichent pas sur votre facture Azure. Pour en savoir plus, consultez [Présentation de vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Ressources facturées d’après les compteurs d’utilisation

Azure ne facture pas directement en fonction du coût de la ressource. Les frais d’une ressource sont calculés à l’aide d’un ou de plusieurs compteurs. Ces compteurs permettent de suivre l’utilisation d’une ressource tout au long de sa durée de vie. Ces compteurs sont ensuite utilisés pour calculer la facture.

Ainsi, quand vous créez une ressource Azure, par exemple une machine virtuelle, une ou plusieurs instances de compteur sont créées. Ces compteurs sont utilisés pour suivre l’utilisation de la ressource au fil du temps. Chaque compteur émet des enregistrements d’utilisation qui sont ensuite utilisés par Azure pour calculer la facture.

Par exemple, les compteurs suivants peuvent être créés pour suivre l’utilisation d’une machine virtuelle créée dans Azure :

- Heures de calcul
- Heures d’adresse IP
- Transfert de données entrant
- Transfert de données sortant
- Disques managés Standard
- Opérations disque managées Standard
- E/S standard - Disque
- E/S standard - Lecture d’objet blob
- E/S standard - Écriture d’objet blob
- E/S standard - Suppression d’objet blob

Une fois la machine virtuelle créée, chacun de ces compteurs commence à émettre des enregistrements d’utilisation. Cette utilisation et le tarif du compteur font l’objet d’un suivi dans le système de comptabilisation d’Azure.

## <a name="payment"></a>Régler votre facture

Si vous avez configuré une carte de crédit ou une carte bancaire comme moyen de paiement, le paiement est débité automatiquement dans les 10 jours qui suivent la fin de la période de facturation. Sur votre relevé de carte de crédit, le poste doit indiquer **MSFT Azure**.

Pour changer la carte de crédit ou de débit utilisée, consultez [Ajouter, mettre à jour ou supprimer une carte de crédit ou débit pour Azure](billing-how-to-change-credit-card.md).

Si vous [payez par facture](billing-how-to-pay-by-invoice.md), envoyez votre paiement à l’adresse indiquée au bas de la facture.

Pour vérifier l’état de votre paiement, [créez un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Conseils pour la gestion des coûts

- Estimez les coûts à l’aide de ce qui suit :
  - [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Calculatrice de coût total de possession](https://aka.ms/azure-tco-calculator)
  - [Informations détaillées sur la tarification de chaque service](https://azure.microsoft.com/pricing/)
- [Passez en revue régulièrement votre utilisation et vos coûts dans le portail Azure](billing-getting-started.md#costs).

## <a name="learn-more"></a>En savoir plus

- [Obtenir votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md)
- [Comprendre les termes figurant sur votre facture Microsoft Azure](billing-understand-your-invoice.md)
- [Comprendre les termes figurant dans le détail de votre utilisation de Microsoft Azure](billing-understand-your-usage.md)
- [Gestion des coûts dans le portail Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md#costs)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
