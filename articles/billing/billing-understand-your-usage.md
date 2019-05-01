---
title: Comprendre votre utilisation détaillée et des frais | Microsoft Docs
description: Découvrez comment lire et comprendre votre utilisation détaillée et des frais
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
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683982"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Comprendre les termes du contrat dans votre fichier d’utilisation et votre facturation Azure

Le fichier détaillé sur l’utilisation et frais contient l’utilisation quotidienne évaluée selon les taux négocié, les achats (par exemple, les réservations, les frais de place de marché) et les remboursements pendant la période spécifiée.
Les frais ne comprennent des crédits, taxes, ou d’autres frais ou remises.
Le tableau suivant couvre les frais sont inclus pour chaque type de compte.

Type de compte | Utilisation d’Azure | Utilisation de la place de marché | Achats | Remboursements
--- | --- | --- | --- | ---
Contrat Entreprise (EA) | Oui | OUI | Oui | Non 
Contrat client Microsoft (CCM) | Oui | OUI | OUI | Oui
Paiement à l’utilisation | Oui | Non  | Non  | Non 

Pour en savoir plus sur les commandes Marketplace (aussi appelés services externes), consultez [comprendre vos frais de service externe Azure](billing-understand-your-azure-marketplace-charges.md).

Consultez [comment obtenir votre facture et tous les jours les données d’utilisation Azure](billing-download-azure-invoice-daily-usage-date.md) pour obtenir des instructions de téléchargement.
Le fichier d’utilisation et des frais est disponible dans un format de fichier de valeurs séparées par des virgules (.csv), vous pouvez ouvrir dans une application de feuille de calcul.

## <a name="list-of-terms-and-descriptions"></a>Liste des termes et descriptions

Le tableau suivant décrit les termes importants utilisés dans la dernière version du fichier d’utilisation et votre facturation Azure.
La liste couvre le paiement à l’utilisation (PAYG), contrat entreprise (EA) et les comptes de contrat de client de Microsoft (MCA).

Terme | Type de compte | Description
--- | --- | ---
AccountName | EA | Nom complet du compte d’inscription.
ID du propriétaire du compte | EA | Identificateur unique pour le compte d’inscription.
Informations supplémentaires | Tous | Métadonnées relatives au service. Par exemple, le type d’image d’une machine virtuelle.
BillingAccountId | EA, MCA | Identificateur unique pour la racine du compte de facturation.
BillingAccountName | EA, MCA | Nom du compte de facturation.
billingCurrency | EA, MCA | Devise associée au compte de facturation.
BillingPeriod | EA | La période de facturation de frais.
BillingPeriodEndDate | EA, MCA | La date de fin de la période de facturation.
BillingPeriodStartDate | EA, MCA | La date de début de la période de facturation.
BillingProfileId | EA, MCA | Identificateur unique de l’inscription EA ou MCA profil de facturation.
BillingProfileName | EA, MCA | Nom de l’inscription EA ou MCA profil de facturation.
ChargeType | EA, MCA | Indique si les frais représente l’utilisation (**utilisation**), un achat (**acheter**), ou un remboursement (**remboursement**).
ConsumedQuantity | PAIEMENT À L’UTILISATION | Consultez la quantité.
Service consommé | Tous | Nom du service, les frais est associé.
Coût | EA | Consultez CostInBillingCurrency.
CostCenter | EA, MCA | Le centre de coût défini pour l’abonnement pour le suivi des coûts (disponibles uniquement dans des périodes de facturation ouverts pour les comptes MCA).
CostInBillingCurrency | MCA | Coût des frais dans la devise de facturation avant de crédits ou de taxes.
CostInPricingCurrency | MCA | Coût des frais dans la devise de tarification avant de crédits ou de taxes.
Devise | PAIEMENT À L’UTILISATION | Consultez BillingCurrency.
Date | EA, MCA | La date d’achat ou de l’utilisation des frais.
ExchangeRateDate | MCA | Date à laquelle que le taux de change a été établi.
ExchangeRatePricingToBilling | MCA | Taux de change utilisé pour convertir le coût dans la devise du prix de la devise de facturation.
Fréquence | EA, MCA | Indique si un coût est attendu à répéter. Facture peut soit se produire qu’une seule fois (**OneTime**), répétez sur une base mensuelle ou annuelle (**périodique**), ou être basée sur l’utilisation (**UsageBased**).
IncludedQuantity | PAIEMENT À L’UTILISATION | Quantité du compteur qui est inclus sans frais dans votre période de facturation actuelle.
InstanceId | PAGY | Consultez l’ID de ressource.
InvoiceId | EA, MCA | ID unique du document figurant sur la facture PDF.
InvoiceSection | MCA | Consultez InvoiceSectionName.
InvoiceSectionId | EA, MCA | Identificateur unique du service de contrat entreprise ou d’une section de facture MCA.
InvoiceSectionName | EA, MCA | Nom du service de contrat entreprise ou de la section de facture MCA.
IsAzureCreditEligible | EA, MCA | Indique si les frais sont éligibles à payer pour l’utilisation de crédits Azure (valeurs : Valeur True, False).
Lieu | EA, MCA | Emplacement de centre de données où la ressource est en cours d’exécution.
Catégorie du compteur | Tous | Nom de la catégorie de classification pour le compteur. Par exemple, *services Cloud* et *mise en réseau*.
ID du compteur | Tous | Identificateur unique pour le compteur.
Nom du compteur | Tous | Le nom du compteur.
Région du compteur | Tous | Nom de l’emplacement de centre de données pour des services facturés en fonction de l’emplacement. Afficher l’emplacement.
Sous-catégorie du compteur | Tous | Nom de la catégorie du compteur sous-classification.
OfferId | EA, MCA | Nom de l’offre achetée.
PartNumber | EA | Identificateur utilisé pour obtenir la tarification de compteur spécifique.
PlanName | EA | Nom du plan de la place de marché.
PreviousInvoiceId | MCA | Référence à une facture d’origine si cette ligne est un remboursement.
pricingCurrency | MCA | Devise utilisée lors de l’évaluation basée sur les prix négociés.
Produit | MCA | Consultez ProductName.
ProductId | EA, MCA | Identificateur unique pour le produit.
ProductName | EA | Nom du produit.
ProductOrderId | EA, MCA | Identificateur unique de l’ordre de produit.
ProductOrderName | EA, MCA | Nom unique de l’ordre de produit.
PublisherName | EA, MCA | Serveur de publication pour les services de place de marché.
PublisherType | EA, MCA | Type de serveur de publication (valeurs : firstParty, thirdPartyReseller, thirdPartyAgency).
Quantité | EA, MCA | Le nombre d’unités achetées ou consommés.
Tarif | PAIEMENT À L’UTILISATION | Consultez UnitPrice.
ID de réservation | EA, MCA | Identificateur unique de l’instance d’acheter la réservation.
ReservationName | EA, MCA | Nom de l’instance d’acheter la réservation.
ResourceGroupId | EA, MCA | Identificateur unique pour le [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) la ressource se trouve dans.
ResourceGroupName | EA, MCA | Nom de la [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) la ressource se trouve dans.
ResourceId | EA, MCA | Identificateur unique de la [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) ressource.
Emplacement des ressources | EA, MCA | Emplacement de centre de données où la ressource est en cours d’exécution. Afficher l’emplacement.
ResourceName | EA | Nom de la ressource.
ResourceType | MCA | Type d’instance de la ressource.
ServiceFamily | EA, MCA | Famille de service auquel appartient le service.
Informations de service 1 | Tous | Métadonnées relatives au service.
Informations de service 2 | Tous | Champ hérité avec des métadonnées facultatives propres au service.
ServicePeriodEndDate | MCA | La date de fin de la période d’évaluation défini et verrouillé de tarification pour le service consommé ou acheté.
ServicePeriodStartDate | MCA | La date de début de la période d’évaluation défini et verrouillé de tarification pour le service consommé ou acheté.
SubscriptionId | Tous | Identificateur unique de l’abonnement.
SubscriptionName | Tous | Nom de l’abonnement.
Balises | Tous | Balises assignées à la ressource. N’inclut pas les balises de groupe de ressources. Peut être utilisé pour regrouper ou répartir les coûts de refacturation interne. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Unité | PAIEMENT À L’UTILISATION | Consultez UnitOfMeasure.
Unité de mesure | Tous | L’unité de mesure pour la facturation pour le service. Par exemple, les services de calcul sont facturées par heure.
UnitPrice | EA | Le prix unitaire pour les frais.
UsageDate | PAIEMENT À L’UTILISATION | Consultez la Date.

Notez que certains champs peuvent différer de la casse et l’espacement entre les types de comptes.
Les versions antérieures des fichiers de paiement à l’utilisation de l’utilisation ont des sections séparées pour l’instruction et l’utilisation quotidienne.

## <a name="ensure-that-your-charges-are-correct"></a>Assurez-vous que vos frais sont correctes

Pour en savoir plus sur l’utilisation détaillée et des frais, découvrez comment comprendre votre [paiement à l’utilisation](./billing-understand-your-bill.md) ou [contrat de client Microsoft](billing-mca-understand-your-bill.md) facture.

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation de Microsoft Azure et des frais](billing-download-azure-daily-usage.md)
