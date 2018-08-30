---
title: Comprendre votre facture Azure | Microsoft Docs
description: Apprenez à lire et à comprendre votre utilisation et la facturation de votre abonnement Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: c782cadadb0250e6c3ca4912dbf8f81e19cb88c5
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919052"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprendre votre facture Microsoft Azure
Pour comprendre votre facture Azure, comparez-la avec le fichier détaillé sur l’utilisation quotidienne et les rapports de gestion des coûts dans le portail Azure.

>[!NOTE]
>Cet article ne s’applique pas aux clients Contrat Entreprise (EA). Si vous êtes client EA, [la documentation de facturation est disponible sur le portail d’entreprise.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Pour obtenir un fichier PDF de votre facture et une copie du fichier CSV détaillant votre utilisation quotidienne, consultez [Obtenir votre facture Azure et vos données sur l’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md). 

Pour les termes et descriptions détaillés de votre facture et de votre fichier détaillé sur l’utilisation quotidienne, consultez [Comprendre les termes indiqués sur votre facture Microsoft Azure](billing-understand-your-invoice.md) et [Comprendre les termes indiqués sur le fichier Microsoft Azure sur l’utilisation détaillée](billing-understand-your-usage.md). 

Pour plus d’informations sur les rapports de gestion des coûts, consultez [Gestion des coûts sur le portail Azure](https://docs.microsoft.com/azure/billing/billing-getting-started).

> [!div class="nextstepaction"]
> [Aidez-nous à améliorer les documents de facturation Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>Comment m’assurer que les frais indiqués sur ma facture sont corrects ?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Si vous voulez plus de détails sur des frais indiqués sur votre facture, vous disposez de deux options.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Option 1 : passez en revue votre facture et comparez l’utilisation et les coûts avec le fichier CSV sur l’utilisation détaillée.

Le fichier CSV sur l’utilisation détaillée indique vos frais par période de facturation et l’utilisation quotidienne. Pour obtenir le fichier CSV détaillant l’utilisation, consultez [Obtenir votre facture Azure et vos données d’utilisation quotidienne](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Vos frais d’utilisation sont affichés au niveau du compteur. Les termes suivants ont la même signification sur la facture et sur le fichier sur l’utilisation détaillée. Par exemple, le cycle de facturation sur la facture équivaut à la période de facturation indiquée dans le fichier sur l’utilisation détaillée.

 | Facture (PDF) | Utilisation détaillée (CSV)|
 | --- | --- |
|Cycle de facturation | Période de facturation |
 |NOM |Catégorie du compteur |
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

Pour afficher une répartition quotidienne de ces frais, accédez à la section **Utilisation quotidienne** du fichier CSV. Filtrer sur « Scheduler » sous *Catégorie du compteur* ; vous pouvez ainsi voir quels jours le compteur a été utilisé et la quantité qui a été consommée. Les informations *Ressource* et *Groupe de ressources* sont également répertoriées à des fins de comparaison. La somme des valeurs *Consommé* doit être égale à ce qui est indiqué sur la facture.

![Section Utilisation quotidienne dans le fichier CSV](./media/billing-understand-your-bill/3.png)

Pour obtenir le coût par jour, multipliez les montants *Consommé* par la valeur *Tarif* dans la section **Relevé**.

Pour plus d’informations sur la facture, consultez [Comprendre votre facture Azure](billing-understand-your-invoice.md).

Pour en savoir plus sur chacune des colonnes du fichier CSV, consultez [Comprendre votre utilisation détaillée d’Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Option 2 : passez en revue votre facture et comparez-la avec l’utilisation et les coûts indiqués dans le portail Azure.

Le portail Azure peut également vous aider à vérifier vos frais. Il fournit des graphiques sur la gestion des coûts qui offrent une vue d’ensemble rapide de votre utilisation et des frais sur votre facture.

Pour continuer avec l’exemple ci-dessus, visitez la [page Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), sélectionnez votre abonnement, puis sélectionnez **Analyse du coût**. À partir de là, vous pouvez spécifier l’intervalle de temps et voir les frais d’utilisation du service Azure Scheduler.

![Vue de l’analyse des coûts dans le portail Azure](./media/billing-understand-your-bill/4.png)

Pour afficher la répartition quotidienne des coûts dans **Historique des coûts**, cliquez sur la ligne.

![Affichage de l’historique des coûts dans le portail Azure](./media/billing-understand-your-bill/5.png)

Pour en savoir plus, cliquez sur [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing-getting-started.md#costs).

## <a name="external"></a>Quels sont les frais associés aux services externes ?
Les services externes (également appelés commandes de la Place de marché Microsoft Azure) sont fournis par les fournisseurs de services indépendants et sont facturés séparément. Les frais ne s’affichent pas sur votre facture Azure. Pour en savoir plus, consultez [Présentation de vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Comment effectuer un paiement ?

Si vous avez configuré une carte de crédit ou une carte bancaire comme moyen de paiement, le paiement est débité automatiquement dans les 10 jours qui suivent la fin de la période de facturation. Sur votre relevé de carte de crédit, le poste doit indiquer **MSFT Azure**.

Si vous [payez par facture](billing-how-to-pay-by-invoice.md), envoyez votre paiement à l’endroit indiqué au bas de celle-ci. Pour plus d’informations, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Comment vérifier le statut d’un paiement effectué par carte de crédit ?

[Créer un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour demander l’état de votre paiement. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Existe-t-il différents types de clients Azure ? Comment savoir quel type de client je suis ?
Il existe différents types de clients Azure. Pour mieux comprendre votre tarifs et votre facture, consultez les descriptions des types de clients suivantes.

- **Entreprise** : Les clients Entreprise ont signé un contrat Entreprise avec Azure pour fixer des prix négociés et bénéficier de tarifs personnalisés pour les ressources Azure.
- **Direct web** : Les clients Direct web n’ont pas signé de contrat personnalisé avec Azure. Ces clients se sont inscrits à Azure via azure.com et s’acquittent des prix publics pour toutes les ressources Azure.
- **Fournisseur de services de cloud** : Les fournisseurs de services cloud sont généralement des entreprises qui ont été engagées par un client pour créer des solutions sur Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Pourquoi je ne vois pas le coût de la ressource que j’ai créée dans ma facture ?
Azure ne facture pas directement en fonction du coût d’une ressource. La facturation est faite à partir d’un ou plusieurs compteurs qui permettent de suivre l’utilisation d’une ressource tout au long de sa durée de vie. Ces compteurs sont ensuite utilisés pour calculer la facture. Vous trouverez plus d’informations sur le système de mesures Azure ci-dessous.

## <a name="how-does-azure-charge-metering-work"></a>Comment fonctionne le système de mesures de facturation Azure ?
Quand vous créez une ressource Azure, par exemple une machine virtuelle, une ou plusieurs instances de compteur sont également créées. Ces compteurs sont utilisés pour suivre l’utilisation de la ressource au fil du temps. Chaque compteur émet des enregistrements d’utilisation qui sont ensuite utilisés par Azure dans notre système de mesure des coûts pour calculer la facture. 

Par exemple, les compteurs suivants peuvent être créés pour suivre l’utilisation d’une machine virtuelle créée dans Azure :

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

Une fois la machine virtuelle créée, chacun des compteurs ci-dessus commence à émettre des enregistrements d’utilisation. Cette utilisation est ensuite utilisée dans le système de mesures d’Azure, avec le prix du compteur pour déterminer le montant facturé au client.

> [!Note]
> Les exemples de compteurs ci-dessus peuvent n’être qu’un sous-ensemble des compteurs créés quand une machine virtuelle est créée.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Quelle est la différence entre Frais internes Azure et Frais de la Place de marché Azure ?
Les frais internes Azure concernent les ressources qui sont directement développées et offertes par Azure. Les frais de la Place de marché Azure concernent les ressources qui ont été créées par des fournisseurs de logiciels tiers et qui peuvent être utilisées via la Place de marché Azure. Par exemple, un pare-feu Barracuda est une ressource de la Place de marché Azure offerte par un tiers. Tous les frais pour le pare-feu et ses compteurs correspondants apparaissent comme frais de la Place de marché. 

## <a name="tips-for-cost-management"></a>Conseils pour la gestion des coûts
- Estimez les coûts en utilisant la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) et la [calculatrice de coût total de possession](https://aka.ms/azure-tco-calculator), et obtenez les [informations de tarification détaillées pour chaque service](https://azure.microsoft.com/pricing/).
- [Configurez des alertes de facturation](billing-set-up-alerts.md).
- [Passez en revue régulièrement votre utilisation et vos coûts dans le portail Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
