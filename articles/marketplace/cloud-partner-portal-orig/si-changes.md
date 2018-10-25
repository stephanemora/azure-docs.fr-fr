---
title: Notes de publication de Seller Insights | Microsoft Docs
description: Fournit des informations sur les modifications apportées à la fonctionnalité Seller Insights.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806034"
---
<a name="seller-insights-release-notes"></a>Notes de publication de Seller Insights 
===============================
(Date de publication : 28 juillet 2018)

Cet article fournit des informations sur les modifications apportées à la fonctionnalité Seller Insights dans le [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/#insights).


<a name="release-highlights"></a>Présentation de la nouvelle version
------------------

-   L’option *Estimation du prix* donne au client une estimation du coût et des frais de change associés.
-   *Paiements prévus* fournit une vue anticipée des paiements potentiels.
-  *Identificateurs de référence de l’utilisation* assure la concordance des données entre l’utilisation et les commandes du client et les paiements
-   *Utilisation quotidienne* permet une meilleure analyse et une connaissance plus approfondie de l’utilisation par les clients.


<a name="changes-to-data-structure-and-taxonomy"></a>Modifications apportées à la structure de données et à la taxonomie
--------------------------------------

Le tableau suivant répertorie les métriques qui ont été ajoutées ou modifiées dans cette version. 

| **Nouveau terme**                   |    **Définition**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Prix (DC)                     | Prix d’une unité d’utilisation pour une référence SKU donnée (dans la devise du client).       |
| Estimation des frais d’extension (DC) | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée (dans la devise du client). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Devise de l’éditeur (DE)        | Devise privilégiée par l’éditeur pour le paiement.                               |
| Prix estimé (DE)           | Estimation du prix d’une unité d’utilisation pour une référence SKU donnée en fonction du taux de change des devises à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Estimation des frais d’extension (DE) | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change des devises à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation. |
| Paiement estimé (DE)          | Estimation du paiement relatif à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change des devises à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Référence d’utilisation                | Identificateur correspondant à un ou plusieurs jours d’utilisation client pour une référence SKU associée à une entrée du rapport de paiement. |
|  |  |
