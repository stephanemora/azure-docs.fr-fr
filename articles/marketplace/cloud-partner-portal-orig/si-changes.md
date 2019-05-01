---
title: Notes de publication Insights de vendeur
description: Fournit des informations sur les modifications apportées à la fonctionnalité Seller Insights.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: c6e9e4fe672c7e171ed4b1cd60655f9e71a562e6
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943120"
---
# <a name="seller-insights-release-notes"></a>Notes de publication de Seller Insights 

(Date de publication : Le 1 mars 2019)

Cet article fournit des informations sur les modifications apportées à la fonctionnalité Seller Insights dans le [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Met en évidence de version pour le 1 mars 2019

* *Tendance de client* ajouté au résumé
* *Meilleurs cinq clients* résumé refléter tous les abonnements Azure a un client
* *Normalisée des tendances d’utilisation et les tendances de commandes Active* sur Résumé rattaché *commandes mensuelles en un clin de œil*
* *Rapport de rapprochement des paiements* mis à jour
* *Meilleurs cinq clients* sur paiement refléter tous les abonnements Azure a un client
* *Rapport d’utilisation* mis à jour avec l’ID de client
* *Ancienneté du client* sur les commandes et utilisation reflète tous les abonnements Azure a un client


(Date de publication : Le 28 juillet 2018)

## <a name="release-highlights-for-july-28-2018"></a>Met en évidence de version pour le 28 juillet 2018


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
| Prix estimé (DE)           | Estimation du prix d’une unité d’utilisation pour une référence SKU donnée en fonction du taux de change des devises à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Estimation des frais d’extension (DE) | Estimation des frais d’extension relatifs à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change des devises à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation. |
| Paiement estimé (DE)          | Estimation du paiement relatif à la quantité d’unités d’utilisation pour une référence SKU donnée en fonction du taux de change des devises à la date de calcul de l’utilisation (dans la devise de l’éditeur). Cette valeur peut ne pas être exacte pour cause d’erreurs d’arrondi ou de troncation.   |
| Référence d’utilisation                | Identificateur correspondant à un ou plusieurs jours d’utilisation client pour une référence SKU associée à une entrée du rapport de paiement. |
|  |  |
