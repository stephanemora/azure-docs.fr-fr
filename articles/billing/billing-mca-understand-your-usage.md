---
title: Comprendre les termes du contrat sur votre utilisation d’Azure et vos frais CSV pour un contrat de client de Microsoft | Microsoft Docs
description: Découvrez comment lire et comprendre les sections de l’utilisation d’Azure et CSV des frais pour votre profil de facturation
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57836944"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Comprendre les termes du contrat sur votre utilisation d’Azure et vos frais CSV pour un contrat de client de Microsoft

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Le fichier CSV utilisation et des frais Azure contient les frais d’utilisation au niveau de tous les jours et les compteurs pour la période de facturation actuelle.

Pour obtenir votre fichier d’utilisation et votre facturation Azure, consultez [affichage et téléchargement de l’utilisation d’Azure et des frais pour votre contrat de client Microsoft](billing-download-azure-daily-usage.md).
Il est disponible au format .csv, que vous pouvez ouvrir dans une application de feuille de calcul.

Les frais d’utilisation correspondent au total des frais **mensuels** d’un abonnement. Les frais d’utilisation ne prennent pas en compte les crédits et les remises.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Modifications apportées à l’utilisation de contrat entreprise Azure et les frais de volume partagé de cluster

Si vous êtes client EA, vous remarquerez que les termes du contrat dans le profil de facturation Azure utilisation de fichier CSV sont différent dans les termes du contrat dans le fichier CSV d’utilisation Azure d’EA. Voici un mappage de conditions de l’utilisation de ce dernier pour vos conditions d’utilisation de profil de facturation :

| Fichier CSV d’utilisation Azure d’EA | Utilisation de l’accord de client de Microsoft Azure et des frais CSV |
| --- | --- |
| Date | date |
| Mois| date |
| jour | date |
| Year | date |
| Produit | product |
| ID du compteur | meterID |
| Catégorie du compteur | meterCategory |
| Sous-catégorie du compteur | meterSubCategory |
| Région du compteur | meterRegion |
| Nom du compteur | meterName |
| ConsumedQuantity | quantité |
| Taux de ressources | effectivePrice | <!-- this was highlighted -->
| Coût étendu | coût |
| Emplacement des ressources | resourceLocation |
| Service consommé | consumedService |
| InstanceId | instanceId |
| Informations de service 1 | serviceInfo1 |
| Informations de service 2 | serviceInfo2 |
| Informations supplémentaires | additionalInfo |
| Balises | tags |
| Identificateur du service Store | S.O. |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| Unité de mesure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Termes et descriptions dans votre fichier d’utilisation et des frais Azure détaillés

La section suivante décrit les termes importants indiqués dans le fichier d’utilisation et votre facturation Azure.

Terme | Description
--- | ---
invoiceId | L’ID unique du document de la facture PDF
previousInvoiceId | Référence à une facture d’origine si cette ligne est un remboursement
billingAccountName | Nom du compte de facturation
billingAccountId | Identificateur unique pour la racine du compte de facturation
billingProfileId | Nom du profil de facturation qui accumule des frais facturés
billingProfileName | Identificateur unique pour le profil de facturation qui accumule des frais facturés
invoiceSectionId | Identificateur unique pour la section de la facture
invoiceSectionName | Nom de la section de la facture
costCenter | Le centre de coût défini sur l’abonnement pour le suivi des coûts (disponibles uniquement dans des périodes de facturation open)
billingPeriodStartDate | La date de début de la période de facturation pour lequel la facture est générée.
billingPeriodEndDate | La date de fin de la période de facturation pour lequel la facture est générée.
servicePeriodStartDate | La date de début de la période d’évaluation qui a défini et verrouillé de tarification pour le service consommé ou acheté
servicePeriodEndDate | La date de fin de la période d’évaluation qui a défini et verrouillé de tarification pour le service consommé ou acheté
date | Pour Azure et à la place de marché basée sur l’utilisation des frais, il s’agit de la date d’évaluation. Pour les achats à usage unique (réservations, place de marché) ou frais récurrents fixes (offres de support), il s’agit de la date d’achat.
serviceFamily | Famille de service auquel appartient le service
productOrderId | Identificateur unique de l’ordre de produit
productOrderName | Nom unique pour l’ordre de produit
consumedService | Nom du service consommé
meterId | L’identificateur unique pour le compteur
meterName | Le nom du compteur
meterCategory | Nom de la catégorie de classification pour le compteur. Par exemple, *services Cloud*, *mise en réseau*, etc.
meterSubCategory | Nom de la catégorie du compteur sous-classification
meterRegion | Nom de la région où le compteur pour le service est disponible. Identifie l’emplacement du centre de données pour certains services tarifés en fonction de l’emplacement de centre de données.
offer | Nom de l’offre achetée
productId | Identificateur unique pour le produit comptabiliser des frais
product | Nom du produit comptabiliser des frais
ID d’abonnement | Identificateur unique pour l’abonnement comptabiliser des frais
subscriptionName | Nom de l’abonnement comptabiliser des frais
reservationId | Identificateur unique pour l’instance d’acheter la réservation
reservationName | Nom de l’instance d’acheter la réservation
publisherType | Type de serveur de publication (valeurs : firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Serveur de publication pour les services de place de marché
resourceGroupId | Identificateur unique du groupe de ressources associé à la ressource
nom_groupe_ressources | Nom du groupe de ressources associé à la ressource
ResourceId | Identificateur unique pour l’instance de ressource
resourceType | Type d’instance de la ressource
resourceLocation | Identifie l’emplacement du centre de données où la ressource est en cours d’exécution.
location | Emplacement normalisée de la ressource si les emplacements de ressources différents sont configurés pour les mêmes régions
quantité | Le nombre d’unités achetées ou consommés
unitOfMeasure | L’unité de mesure pour la facturation pour le service. Par exemple, les services de calcul sont facturées par heure.
chargeType | Type de frais. Valeurs : <ul><li>Utilisation de AsCharged : Frais cumulés selon l’utilisation d’un service Azure. Cela inclut l’utilisation sur les machines virtuelles qui ne sont pas facturés en raison des instances réservées.</li><li>AsCharged-PurchaseMarketplace : Frais récurrents à usage unique ou fixes à partir d’achats Marketplace</li><li>AsCharged-UsageMarketplace: Les frais pour les services de place de marché qui sont facturés basés sur des unités de consommation</li></ul>
isAzureCreditEligible | Indicateur qui indique si la charge sur le service est éligible à payer pour l’utilisation de crédits Azure (valeurs : Valeur True, False)
serviceInfo1 | Métadonnées propres au service
serviceInfo2 | Champ hérité capturant les métadonnées facultatives propres au service
additionalInfo | Métadonnées supplémentaires spécifiques au service.
tags | Balises que vous affectez à la ressource

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Comment m’assurer que les frais indiqués sur mon fichier de l’utilisation et frais Azure sont corrects ?

S’il existe des frais sur votre fichier d’utilisation détaillées qui vous voulez plus d’informations sur, consultez [comprendre les frais de facture de votre profil facturation](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation de Microsoft Azure et des frais](billing-download-azure-daily-usage.md)
