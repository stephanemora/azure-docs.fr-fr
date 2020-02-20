---
title: Conditions de la grille tarifaire de votre Contrat client Microsoft - Azure
description: Découvrir comment lire et comprendre vos données d’utilisation et de facture pour un Contrat client Microsoft.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 1e776e351fe723f0fbc5b1d6a8f5593a9fc975b7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199617"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Conditions de la grille tarifaire de votre Contrat client Microsoft

Cet article s’applique à un compte de facturation associé à un contrat client Microsoft. [Vérifiez que vous avez accès à un contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Si vous êtes Propriétaire, Collaborateur, Lecteur ou Gestionnaire de factures d’un profil de facturation, vous pouvez télécharger la grille tarifaire de votre organisation à partir du Portail Azure. Consultez [Afficher et télécharger les tarifs de votre organisation](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Conditions et descriptions de votre grille tarifaire

La section suivante décrit les conditions importantes figurant dans la grille tarifaire de votre Contrat client Microsoft.

| **Nom du champ**   | **Description**   |
| --- | --- |
| basePrice  | Prix du marché au moment où le client se connecter ou prix du marché au moment du lancement du compteur de service s’il est postérieur à la connexion.   |
| billingAccountId  | Identificateur unique pour le compte de facturation.   |
| billingAccountName  | Nom du compte de facturation.  |
| billingCurrency | Monnaie locale dans laquelle les frais sont affichés |
| billingProfileId  | Identificateur unique du profil de facturation.   |
| billingProfileName  | Nom du profil de facturation configuré pour la réception des factures. Les tarifs de la grille tarifaire sont associés à ce profil de facturation. |
| currency | Monnaie locale dans laquelle tous les tarifs sont affichés. |
| discount | Remise accordée pour les niveaux Diplôme, Gratuit, Quantité incluse ou les remises négociées, le cas échéant. Représenté sous forme de pourcentage. |
| effectiveEndDate  | Date de fin du prix effectif. |
| effectiveStartDate  | Date d’entrée en vigueur du prix. |
| includedQuantity | Quantité d’un service donné qu’un client est autorisé à utiliser sans frais supplémentaires. |
| marketPrice | Prix actuel du marché en vigueur pour un service donné. |
| meterId  | Identificateur unique du compteur. |
| meterCategory  | Nom de la catégorie de classification du compteur. Par exemple, _services Cloud_, _mise en réseau_, etc. |
| meterName  | Nom du compteur. Le compteur représente la ressource déployable d’un service Azure. |
| meterSubCategory  | Nom de la catégorie de sous-classification du compteur.  |
| meterType  |  Nom du type de compteur. |
| meterRegion  | Nom de la région où le compteur du service est disponible. Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement.    |
| Produit  | Nom du produit auquel les frais s'appliquent. Ex. : base de données SQL de Base et base de données SQL standard  |
| productId  | Identificateur unique pour le produit dont le compteur est utilisé. |
| productOrderName  | Nom de l’offre du produit acheté. |
| serviceFamily  | Type de service Azure. Ex. : Calcul, Analytics, Sécurité |
| tierMinimumUnits  | Définit la limite inférieure de la plage de niveaux pour laquelle les prix sont définis. Par exemple, si la plage est comprise de 0 à 100, tierMinimumUnits est égal à 0.  |
| unitOfMeasure  | Identifie les unités de mesure pour la facturation du service. Par exemple, les services de calcul sont facturés à l’heure. |
| unitPrice  | Prix unitaire au moment de la facturation (et non le prix combiné effectif) spécifique à un compteur et au nom du produit commandé.  Remarque : Le prix unitaire est différent du prix effectif dans les téléchargements de détails d’utilisation dans le cas de services qui ont des prix différentiels entre niveaux.  Dans le cas de services à tarification multiniveaux, le prix effectif est un taux combiné pour l’ensemble des niveaux et ne correspond pas à un prix unitaire propre à un niveau. Le prix combiné ou le prix effectif est le prix net de la quantité consommée sur plusieurs niveaux (où chaque niveau a un prix unitaire spécifique). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l'accès à un contrat client Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger les tarifs de votre organisation](ea-pricing.md)
