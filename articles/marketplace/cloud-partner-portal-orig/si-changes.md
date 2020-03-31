---
title: Notes de publication de Seller Insights
description: Fournit des informations sur les modifications apportées à la fonctionnalité Seller Insights.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: dsindona
ms.openlocfilehash: 1b88a8353c1cb9f97f18d720171f0827d7d85254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285399"
---
# <a name="seller-insights-release-notes"></a>Notes de publication de Seller Insights 

(Date de publication : 1er mars 2019)

Cet article fournit des informations sur les modifications apportées à la fonctionnalité Seller Insights dans le [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Présentation de la nouvelle version au 1er mars 2019

* *Tendance de client* ajoutée au résumé
* *5 meilleurs clients* du résumé reflétant tous les abonnements Azure d’un client
* *Tendance d’utilisation normalisée et tendance de commandes actives* du résumé rattachées aux *commandes mensuelles en un coup d’œil*
* *Rapport de rapprochement des paiements* mis à jour
* *5 meilleurs clients* pour le paiement reflétant tous les abonnements Azure d’un client
* *Rapport d’utilisation* mis à jour avec l’ID de client
* *Ancienneté du client* sur les commandes et l’utilisation reflétant tous les abonnements Azure d’un client


(Date de publication : 28 juillet 2018)

## <a name="release-highlights-for-july-28-2018"></a>Présentation de la nouvelle version au 28 juillet 2018


-   L’option *Estimation du prix* donne au client une estimation du coût et des frais de change associés.
-   *Paiements prévus* fournit une vue anticipée des paiements potentiels.
-  *Identificateurs de référence de l’utilisation* assure la concordance des données entre l’utilisation et les commandes du client et les paiements
-   *Utilisation quotidienne* permet une meilleure analyse et une connaissance plus approfondie de l’utilisation par les clients.


### <a name="changes-to-data-structure-and-taxonomy"></a>Modifications apportées à la structure de données et à la taxonomie

Le tableau suivant répertorie les métriques qui ont été ajoutées ou modifiées dans cette version. 

| **Nouveau terme**                   |    **Définition**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Prix (DC)                     | Prix d’une unité d’utilisation pour une référence SKU donnée (dans la devise du client).       |
| Estimation des frais d’extension (DC) | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée (dans la devise du client). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Devise de l’éditeur (DE)        | Devise privilégiée par l’éditeur pour le paiement.                               |
| Prix estimé (DE)           | Estimation du prix d’une unité d’utilisation pour une référence SKU donnée en fonction du taux de change à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Estimation des frais d’extension (DE) | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation. |
| Paiement estimé (DE)          | Estimation du paiement relatif à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Référence d’utilisation                | Identificateur correspondant à un ou plusieurs jours d’utilisation client pour une référence SKU associée à une entrée du rapport de paiement. |
|  |  |
