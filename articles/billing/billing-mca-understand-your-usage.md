---
title: Termes du fichier CSV détaillant l'utilisation et les frais Azure dans le cadre d'un Contrat client Microsoft | Microsoft Docs
description: Apprenez à lire et à comprendre les sections du fichier CSV détaillant l'utilisation et les frais Azure associés à votre profil de facturation
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371306"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Termes du fichier CSV détaillant l'utilisation et les frais Azure dans le cadre d'un Contrat client Microsoft

Cet article s'applique à un compte de facturation associé à un Contrat client Microsoft. [Vérifiez que vous avez accès à un Contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Le fichier CSV détaillant l'utilisation et les frais Azure contient les frais liés à l'utilisation quotidienne et à l'utilisation au niveau du compteur pour la période de facturation actuelle.

Pour obtenir le fichier détaillant votre utilisation et vos frais Azure, consultez [Afficher et télécharger l'utilisation et les frais Azure pour votre Contrat client Microsoft](billing-download-azure-daily-usage.md).
Il est disponible au format .csv, que vous pouvez ouvrir dans une application de feuille de calcul.

Les frais d’utilisation correspondent au total des frais **mensuels** d’un abonnement. Les frais d’utilisation ne prennent pas en compte les crédits et les remises.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Modifications apportées au fichier CSV détaillant l'utilisation et les frais Azure dans le cadre d'un Contrat Entreprise (EA)

Les clients EA remarqueront que les termes employés dans le fichier CSV d'utilisation Azure du profil de facturation sont différents de ceux employés dans le fichier CSV d'utilisation Azure du Contrat Entreprise. Le tableau suivant met en correspondance les termes d'un Contrat Entreprise et ceux du profil de facturation :

| Fichier CSV détaillant l'utilisation Azure dans le cadre d'un Contrat Entreprise | Fichier CSV détaillant l'utilisation et les frais Azure dans le cadre d'un Contrat client Microsoft |
| --- | --- |
| Date | date |
| Mois| date |
| jour | date |
| Year | date |
| Produit | product |
| ID du compteur | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantité |
| ResourceRate | effectivePrice | <!-- this was highlighted -->
| ExtendedCost | coût |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Balises | tags |
| StoreServiceIdentifier | N/A |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Description des termes employés dans le fichier détaillant votre utilisation et vos frais Azure

La section suivante décrit les termes importants employés dans le fichier détaillant l'utilisation et les frais Azure.

Terme | Description
--- | ---
invoiceId | Identifiant unique du document figurant sur la facture PDF
previousInvoiceId | Référence à la facture d'origine si cette ligne correspond à un remboursement
billingAccountName | Nom du compte de facturation
billingAccountId | Identificateur unique du compte de facturation racine
billingProfileId | Nom du profil de facturation auquel les frais facturés sont associés
billingProfileName | Identificateur unique du profil de facturation auquel les frais facturés sont associés
invoiceSectionId | Identificateur unique de la section de facture
invoiceSectionName | Nom de la section de facture
costCenter | Centre de coûts défini sur l'abonnement pour le suivi des coûts (uniquement disponible au niveau des périodes de facturation ouvertes)
billingPeriodStartDate | Date de début de la période de facturation pour laquelle la facture est générée
billingPeriodEndDate | Date de fin de la période de facturation pour laquelle la facture est générée
servicePeriodStartDate | Date de début de la période de tarification qui a défini et bloqué les prix correspondant au service consommé ou acheté
servicePeriodEndDate | Date de fin de la période de tarification qui a défini et bloqué les prix correspondant au service consommé ou acheté
date | Pour les frais basés sur l'utilisation d'Azure et de la Place de marché, il s'agit de la date de tarification. Pour les achats ponctuels (Réservations, Place de marché) ou les frais récurrents fixes (offres de support), il s'agit de la date d'achat.
serviceFamily | Famille de services à laquelle le service appartient
productOrderId | Identificateur unique de la commande de produit
productOrderName | Nom unique de la commande de produit
consumedService | Nom du service consommé
meterId | Identificateur unique du compteur
meterName | Nom du compteur
meterCategory | Nom de la catégorie de classification du compteur. Par exemple, *services Cloud*, *mise en réseau*, etc.
meterSubCategory | Nom de la catégorie de sous-classification du compteur
meterRegion | Nom de la région où le compteur du service est disponible. Identifie l'emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement.
offer | Nom de l'offre achetée
productId | Identificateur unique du produit auquel les frais s'appliquent
product | Nom du produit auquel les frais s'appliquent
ID d’abonnement | Identificateur unique de l'abonnement auquel les frais s'appliquent
subscriptionName | Nom de l'abonnement auquel les frais s'appliquent
reservationId | Identificateur unique de l'instance de réservation achetée
reservationName | Nom de l'instance de réservation achetée
publisherType | Type d'éditeur (valeurs : firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Éditeur des services de la Place de marché
resourceGroupId | Identificateur unique du groupe de ressources associé à la ressource
nom_groupe_ressources | Nom du groupe de ressources associé à la ressource
resourceId | Identificateur unique de l'instance de ressource
resourceType | Type d'instance de ressource
resourceLocation | Identifie l'emplacement du centre de données dans lequel la ressource est exécutée
location | Emplacement normalisé de la ressource si différents emplacements de ressource sont configurés pour les mêmes régions
quantité | Nombre d'unités achetées ou consommées
unitOfMeasure | Unité de mesure utilisée pour la facturation du service. Par exemple, les services de calcul sont facturés à l'heure.
chargeType | Type de frais. Valeurs : <ul><li>AsCharged-Usage : frais comptabilisés en fonction de l'utilisation d'un service Azure. Cela inclut l'utilisation sur des machines virtuelles non facturés pour cause d'instances réservées.</li><li>AsCharged-PurchaseMarketplace : frais récurrents ponctuels ou fixes liés à des achats effectués sur la Place de marché</li><li>AsCharged-UsageMarketplace : frais liés à des services de la Place de marché facturés en unités de consommation</li></ul>
isAzureCreditEligible | Indicateur signalant si les frais liés au service peuvent être payés à l'aide de crédits Azure (valeurs : True, False)
serviceInfo1 | Métadonnées spécifiques au service
serviceInfo2 | Champ hérité capturant les métadonnées facultatives propres au service
additionalInfo | Autres métadonnées spécifiques au service
tags | Balises que vous attribuez à la ressource

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Comment m'assurer que les frais mentionnés dans le fichier détaillant mon utilisation et mes frais Azure sont corrects ?

Pour plus de détails sur certains frais comptabilisés dans votre fichier d'utilisation détaillée, consultez [Comprendre les frais comptabilisés sur la facture de votre profil de facturation](billing-mca-understand-your-bill.md).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d'aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation et vos frais Microsoft Azure](billing-download-azure-daily-usage.md)
