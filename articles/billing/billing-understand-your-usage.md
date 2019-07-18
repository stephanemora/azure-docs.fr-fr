---
title: Comprendre le détail de l’utilisation et des frais | Microsoft Docs
description: Découvrez comment lire et comprendre le détail de l’utilisation et des frais
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 2eb9f8e19be2a7b6220bc34bf4ce0c72c4ac0b4f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275054"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Comprendre les termes du fichier sur l’utilisation et les frais Azure

Le fichier détaillé sur l’utilisation et les frais contient l’utilisation quotidienne évaluée sur la base des tarifs négociés, des achats (par exemple, réservations, dépenses sur l Place de marché) et des remboursements au cours de la période spécifiée.
Les frais ne comprennent les crédits, taxes, ou d’autres frais ou remises.
Le tableau suivant couvre les frais inclus pour chaque type de compte.

Type de compte | Utilisation d’Azure | Utilisation de la Place de marché | Achats | Remboursements
--- | --- | --- | --- | ---
Contrat Entreprise (EA) | OUI | OUI | OUI | Non
Contrat client Microsoft (MCA) | OUI | OUI | OUI | OUI
Paiement à l’utilisation | OUI | Non | Non | Non

Pour en savoir plus sur les commandes passées sur la Place de marché (également appelées services externes), voir [Comprendre vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

Pour télécharger des instructions, voir [Comment obtenir votre facture Azure et vos données d’utilisation quotidienne](billing-download-azure-invoice-daily-usage-date.md).
Le fichier sur l’utilisation et les frais est disponible au format .csv. Vous pouvez l’ouvrir dans une application de feuille de calcul.

## <a name="list-of-terms-and-descriptions"></a>Liste de termes et descriptions

Le tableau suivant présente les termes importants utilisés dans la dernière version du fichier sur l’utilisation et les frais Azure.
La liste couvre les comptes associés à un paiement à l’utilisation (PAYG), à un Contrat Entreprise (EA) et à un Contrat client Microsoft (MCA).

Terme | Type de compte | Description
--- | --- | ---
AccountName | EA | Nom d’affichage du compte d’inscription.
AccountOwnerId | EA | Identificateur unique pour le compte d’inscription.
AdditionalInfo | Tous | Métadonnées relatives au service. Par exemple, le type d’image d’une machine virtuelle.
BillingAccountId | EA, MCA | Identificateur unique pour le compte de facturation racine.
BillingAccountName | EA, MCA | Nom du compte de facturation.
BillingCurrency | EA, MCA | Devise associée au compte de facturation.
BillingPeriod | EA | Période de facturation des frais.
BillingPeriodEndDate | EA, MCA | Date de fin de la période de facturation.
BillingPeriodStartDate | EA, MCA | La date de début de la période de facturation.
BillingProfileId | EA, MCA | Identificateur unique de l’accord de mise en œuvre EA ou du profil de facturation MCA.
BillingProfileName | EA, MCA | Nom de l’accord de mise en œuvre EA ou du profil de facturation MCA.
ChargeType | EA, MCA | Indique si les frais ont trait à l’utilisation (**Usage**), à un achat (**Purchase**) ou à un remboursement (**Refund**).
ConsumedQuantity | PAYG | Voir Quantité.
ConsumedService | Tous | Nom du service auquel les frais sont associés.
Coût | EA | Voir CostInBillingCurrency.
CostCenter | EA, MCA | Centre de coûts défini pour l’abonnement au fins de suivi des coûts (uniquement disponible dans les des périodes de facturation ouvertes pour les comptes MCA).
CostInBillingCurrency | MCA | Coût des frais dans la devise de facturation avant crédits ou taxes.
CostInPricingCurrency | MCA | Coût des frais dans la devise de tarification avant crédits ou taxes.
Devise | PAYG | Voir BillingCurrency.
Date | EA, MCA | Date d’achat ou d’utilisation à laquelle les frais correspondent.
ExchangeRateDate | MCA | Date à laquelle que le taux de change a été établi.
ExchangeRatePricingToBilling | MCA | Taux de change utilisé pour convertir le coût de la devise de tarification vers la devise de facturation.
Fréquence | EA, MCA | Indique si un coût est supposé se répéter. Les coûts peut être ponctuels (**OneTime**), se répéter mensuellement ou annuellement (**Recurring**) ou être basés sur l’utilisation (**UsageBased**).
IncludedQuantity | PAYG | Montant du compteur inclus gratuitement dans votre période de facturation en cours.
InstanceId | PAGY | Voir ResourceId.
InvoiceId | EA, MCA | Référence de document unique figurant sur la facture au format PDF.
InvoiceSection | MCA | Voir InvoiceSectionName.
InvoiceSectionId | EA, MCA | Identificateur unique du service EA ou de la section de facture MCA.
InvoiceSectionName | EA, MCA | Nom du service EA ou de la section de facture MCA.
IsAzureCreditEligible | EA, MCA | Indique si les frais sont éligibles pour un paiement à l’aide de crédits Azure (valeurs : True, False).
IsEstimated | Tous | Indique si la période de facturation est clôturée/finalisée. Les données d’utilisation peuvent changer tout au long de la période de facturation jusqu’à la génération de la facture. Ces enregistrements sont marqués comme « estimé ».
Location | EA, MCA | Emplacement du centre de données où la ressource est exécutée.
MeterCategory | Tous | Nom de la catégorie de classification du compteur. Par exemple, *Services cloud* ou *Mise en réseau*.
ID du compteur | Tous | Identificateur unique du compteur.
MeterName | Tous | Nom du compteur.
MeterRegion | Tous | Nom de l’emplacement du centre de données pour des services tarifés en fonction de l’emplacement. Voir Location.
MeterSubCategory | Tous | Nom de la catégorie de sous-classification du compteur.
OfferId | EA, MCA | Nom de l’offre achetée.
PartNumber | EA | Identificateur utilisé pour obtenir la tarification du compteur spécifique.
PlanName | EA | Nom du plan sur la Place de marché.
PreviousInvoiceId | MCA | Référence à une facture d’origine si cette ligne correspond à un remboursement.
PricingCurrency | MCA | Devise utilisée lors de l’évaluation basée sur les prix négociés.
Produit | MCA | Voir ProductName.
ProductId | EA, MCA | Identificateur unique pour le produit.
ProductName | EA | Nom du produit.
ProductOrderId | EA, MCA | Identificateur unique de la commande du produit.
ProductOrderName | EA, MCA | Nom unique de la commande du produit.
PublisherName | EA, MCA | Éditeur pour les services de la Place de marché.
PublisherType | EA, MCA | Type d’éditeur (valeurs : firstParty, thirdPartyReseller, thirdPartyAgency).
Quantité | EA, MCA | Nombre d’unités achetées ou consommés.
Tarif | PAYG | Voir UnitPrice.
ID de réservation | EA, MCA | Identificateur unique de l’instance de réservation achetée.
ReservationName | EA, MCA | Nom de l’instance de réservation achetée.
ResourceGroupId | EA, MCA | Identificateur unique du [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) dans lequel figure la ressource.
ResourceGroupName | EA, MCA | Nom du [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) dans lequel figure la ressource.
ResourceId | EA, MCA | Identificateur unique de la ressources [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources).
ResourceLocation | EA, MCA | Emplacement du centre de données où la ressource est exécutée. Voir Location.
ResourceName | EA | Nom de la ressource.
ResourceType | MCA | Type d’instance de ressource.
ServiceFamily | EA, MCA | Famille de services à laquelle appartient le service.
ServiceInfo1 | Tous | Métadonnées relatives au service.
ServiceInfo2 | Tous | Champ hérité avec des métadonnées facultatives spécifiques du service.
ServicePeriodEndDate | MCA | Date de fin de la période d’évaluation utilisée pour définir et fixer la tarification correspondant au service consommé ou acheté.
ServicePeriodStartDate | MCA | Date de début de la période d’évaluation utilisée pour définir et fixer la tarification correspondant au service consommé ou acheté.
SubscriptionId | Tous | Identificateur unique de l’abonnement.
SubscriptionName | Tous | Nom de l’abonnement.
Balises | Tous | Balises affectées à la ressource. N’inclut pas les balises de groupe de ressources. Peut être utilisée pour grouper ou répartir des coûts de refacturation interne. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Unité | PAYG | Voir UnitOfMeasure.
UnitOfMeasure | Tous | Unité de mesure pour la facturation du service. Par exemple, les services de calcul sont facturés à l’heure.
UnitPrice | EA | Prix unitaire des frais.
UsageDate | PAYG | Voir Date.

Notez que certains champs peuvent différer par leur casse ou leur espacement d’un type de compte à l’autre.
Les versions antérieures des fichiers sur le paiement à l’utilisation comportent des sections séparées pour le relevé et l’utilisation quotidienne.

## <a name="ensure-that-your-charges-are-correct"></a>Vérifier que vos frais sont corrects

Pour en savoir plus sur les détails de l’utilisation et des frais, découvrez comment comprendre votre facture de [paiement à l’utilisation](./billing-understand-your-bill.md) ou de [Contrat client Microsoft](billing-mca-understand-your-bill.md).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation et vos frais Microsoft Azure](billing-download-azure-daily-usage.md)
