---
title: Comprendre les termes du contrat dans votre table de tarification pour un contrat de client de Microsoft - Azure | Microsoft Docs
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880753"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Comprendre les termes du contrat dans votre table de tarification pour un contrat de client de Microsoft

Cet article s’applique à un compte de facturation d’un contrat de client de Microsoft. [Vérifiez si vous avez accès à un contrat de client Microsoft](#check-access-to-a-microsoft-customer-agreement).

Si vous êtes propriétaire, collaborateur, lecteur ou Gestionnaire de factures un profil de facturation, vous pouvez télécharger la grille tarifaire de votre organisation à partir du portail Azure. Consultez [affichage et téléchargement de tarification de votre organisation](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Termes et descriptions dans votre table de tarification de contrat de client Microsoft détaillés

La section suivante décrit les termes importants indiqués dans votre table de tarification de contrat de client de Microsoft.

| **Nom du champ**   | **Description**   |
| --- | --- |
| billingAccountId  | Identificateur unique pour le compte de facturation.   |
| billingAccountName  | Nom du compte de facturation.  |
| billingProfileId  | Identificateur unique pour le profil de facturation.   |
| billingProfileName  | Nom du profil de facturation qui est configuré pour recevoir des factures. Les prix dans la table de tarification sont associés à ce profil de facturation. |
| productOrderName  | Nom du plan de produit acheté. |
| serviceFamily  | Type de service Azure. Ex : Calcul, Analytique, sécurité |
| Produit  | Nom du produit comptabiliser des frais. Ex : Les types de base de données SQL Standard SQL DB  |
| productId  | Identificateur unique pour le produit dont le compteur est consommé. |
| unitOfMeasure  | Identifie les unités de mesure pour la facturation pour le service. Par exemple, les services de calcul sont facturées par heure. |
| meterId  | Identificateur unique pour le compteur. |
| meterName  | Nom du compteur. Le compteur représente la ressource déployable d’un service Azure. |
| meterCategory  | Nom de la catégorie de classification pour le compteur. Par exemple, _services Cloud_, _mise en réseau_, etc. |
| meterType  |  Nom du type de compteur. |
| meterSubCategory  | Nom de la catégorie du compteur sous-classification.  |
| meterRegion  | Nom de la région où le compteur pour le service est disponible. Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement.    |
| tierId  | Identifie le niveau tarifaire, le cas échéant. Cela fonctionne conjointement avec tierMinimumUnits permettant de définir à plusieurs niveaux de prix lorsque les prix varient en fonction du nombre d’unités consommées.    |
| tierMinimumUnits  | Définit la limite inférieure de la plage de niveau pour lequel les prix sont définis. Par exemple, si la plage est de 0 à 100, tierMinimumUnits serait 0.  |
| effectiveStartDate  | Démarrer la date à laquelle le prix devient effectif. |
| effectiveEndDate  | Date de fin du prix efficace. |
| unitPrice  | Prix unitaire au moment de la facturation (pas le prix combinée efficace) comme étant spécifiques à un nom de compteur et produit l’ordre.  Remarque : Le prix unitaire n’est pas le même comme le prix applicable dans les détails d’utilisation télécharge en cas de services dont le prix différentielles au sein des niveaux.  En cas de services à plusieurs niveaux de tarification, le prix applicable est un taux combinée au sein des niveaux et n’affiche pas un prix unitaire de niveau spécifique. Le prix mixte ou le prix applicable sont le prix net pour la quantité consommée la répartition au sein des niveaux multiples (où chaque niveau a un prix unitaire spécifique). |
| basePrice  | Le prix du marché au moment où le client ouvre une session ou le prix du marché au moment où le compteur de service lance s’il s’agit d’après l’authentification.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Vérifiez l’accès à un contrat de client de Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créer une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

- [Afficher et télécharger la tarification de votre organisation](billing-ea-pricing.md)
