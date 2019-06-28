---
title: Comprendre les conditions de votre grille tarifaire pour un Contrat client Microsoft - Azure | Microsoft Docs
description: Apprenez à lire et à comprendre votre utilisation et la facturation de votre abonnement Azure
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371357"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Comprendre les conditions de votre grille tarifaire pour un Contrat client Microsoft

Cet article s’applique à un compte de facturation associé à un Contrat client Microsoft. [Vérifiez que vous avez accès à un Contrat client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Si vous êtes Propriétaire, Collaborateur, Lecteur ou Gestionnaire de factures d’un profil de facturation, vous pouvez télécharger la grille tarifaire de votre organisation à partir du Portail Azure. Consultez [Afficher et télécharger les tarifs de votre organisation](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Conditions et descriptions détaillées de la grille tarifaire de votre Contrat client Microsoft

La section suivante décrit les conditions importantes figurant dans la grille tarifaire de votre Contrat client Microsoft.

| **Nom du champ**   | **Description**   |
| --- | --- |
| billingAccountId  | Identificateur unique pour le compte de facturation.   |
| billingAccountName  | Nom du compte de facturation.  |
| billingProfileId  | Identificateur unique du profil de facturation.   |
| billingProfileName  | Nom du profil de facturation configuré pour la réception des factures. Les tarifs de la grille tarifaire sont associés à ce profil de facturation. |
| productOrderName  | Nom de l’offre du produit acheté. |
| serviceFamily  | Type de service Azure. Ex. : Calcul, Analytics, Sécurité |
| Produit  | Nom du produit auquel les frais s'appliquent. Ex. : base de données SQL de Base et base de données SQL standard  |
| productId  | Identificateur unique pour le produit dont le compteur est utilisé. |
| unitOfMeasure  | Identifie les unités de mesure pour la facturation du service. Par exemple, les services de calcul sont facturés à l’heure. |
| meterId  | Identificateur unique de la mesure. |
| meterName  | Nom du compteur. Le compteur représente la ressource déployable d’un service Azure. |
| meterCategory  | Nom de la catégorie de classification pour le compteur. Par exemple, _services Cloud_, _mise en réseau_, etc. |
| meterType  |  Nom du type de compteur. |
| meterSubCategory  | Nom de la catégorie de sous-classification du compteur.  |
| meterRegion  | Nom de la région où le compteur du service est disponible. Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement.    |
| tierId  | Identifie le niveau tarifaire, le cas échéant. Cela fonctionne conjointement à tierMinimumUnits pour fixer des prix différenciés lorsque les prix varient en fonction du nombre d’unités consommées.    |
| tierMinimumUnits  | Définit la limite inférieure de la plage de niveaux pour laquelle les prix sont définis. Par exemple, si la plage est comprise de 0 à 100, tierMinimumUnits est égal à 0.  |
| effectiveStartDate  | Date d’entrée en vigueur du prix. |
| effectiveEndDate  | Date de fin du prix effectif. |
| unitPrice  | Prix unitaire au moment de la facturation (et non le prix combiné effectif) spécifique à un compteur et au nom du produit commandé.  Remarque : Le prix unitaire est différent du prix effectif dans les téléchargements de détails d’utilisation dans le cas de services qui ont des prix différentiels entre niveaux.  Dans le cas de services à tarification multiniveaux, le prix effectif est un taux combiné pour l’ensemble des niveaux et ne correspond pas à un prix unitaire propre à un niveau. Le prix combiné ou le prix effectif est le prix net de la quantité consommée sur plusieurs niveaux (où chaque niveau a un prix unitaire spécifique). |
| basePrice  | Prix du marché au moment où le client se connecter ou prix du marché au moment du lancement du compteur de service s’il est postérieur à la connexion.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifier l’accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une requête de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger les tarifs de votre organisation](billing-ea-pricing.md)
