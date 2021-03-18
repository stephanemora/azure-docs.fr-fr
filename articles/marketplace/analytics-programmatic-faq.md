---
title: Questions fréquentes sur l’accès programmatique aux données d’analyse
description: Questions fréquentes sur l’accès programmatique aux données d’analyse dans l’Espace partenaires pour vos offres de la place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583459"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Questions fréquentes sur l’accès programmatique aux données d’analyse

Cet article répond aux questions fréquemment posées sur la façon d’accéder par programme aux données d’analyse dans l’Espace partenaires pour vos offres de la place de marché commerciale.

## <a name="api-responses"></a>Réponses de l’API

Quels sont les différents scénarios dans lesquels je peux recevoir une réponse de l’API autre que 200 (réussite) ?

Ce tableau décrit les réponses de l’API et la procédure à suivre si vous les recevez.

| Description de l'erreur | Code d'erreur | Dépanner |
| ------------ | ------------- | ------------- |
| Non autorisé | 401 | Il s’agit d’une exception d’authentification. Vérifiez que le jeton Azure Active Directory (Azure AD) est correct. Le jeton Azure AD est valide pendant 60 minutes, après quoi vous devrez le regénérer. |
| Nom de table non valide | 400 | Le nom du jeu de données est incorrect. Revérifiez le nom du jeu de données en appelant l’API « Obtenir tous les jeux de données ». |
| Nom de colonne incorrect | 400| Le nom de la colonne de la requête est incorrect. Revérifiez le nom de la colonne en appelant l’API « Obtenir tous les jeux de données » ou reportez-vous aux noms des colonnes dans les tableaux suivants :<br><ul><li>[Tableau Détails des commandes](orders-dashboard.md#orders-details-table)</li><li>[Tableau des détails de l’utilisation](usage-dashboard.md#usage-details-table)</li><li>[Tableau des détails des commandes](customer-dashboard.md#customer-details-table)</li><li>[Insights sur la place de marché – Tableau des détails](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Valeur null ou manquante | 400 | Il se peut que des paramètres obligatoires soient absents de la charge utile de requête de l’API. |
| Paramètres de rapport non valides | 400 | Assurez-vous que les paramètres de rapport sont corrects. Par exemple, vous pouvez attribuer une valeur inférieure à 4 au paramètre `RecurrenceInterval`. |
| L’intervalle de périodicité doit être compris entre 4 et 90. | 400 | Assurez-vous que la valeur du paramètre de requête `RecurrenceInterval` est comprise entre 4 et 90. |
| QueryId non valide | 400 | Revérifiez le `QueryId` généré. |
| Paramètres de rapport non valides pour la création : l’heure de début du rapport doit se situer à au moins 4 heures par rapport à l’heure UTC actuelle. | 400 | Le paramètre d’heure de début de la charge utile de la requête ne doit pas être dans le passé. L’heure de début du rapport doit se situer à au moins 4 heures par rapport à l’heure UTC actuelle. |
| Valeur demandée « chaîne » introuvable. | 400 | Vérifiez que vous avez mis à jour les paramètres de la requête `callbackurl` ou `format` . |
| Aucun élément n’a été trouvé avec les filtres spécifiés. | 404 | Vérifiez le paramètre `reportID` utilisé dans l’API Obtenir les exécutions de rapport. |
| Aucune exécution n’est intervenue pour les conditions de filtre données. Revérifiez la valeur reportId ou executionId et réessayez l’API après l’heure d’exécution planifiée du rapport. | 404 | Assurez-vous que la valeur `reportId` est correcte. Réessayez l’API après l’heure d’exécution planifiée du rapport, comme indiqué dans la charge utile de la requête. |
| Une erreur interne s’est produite lors de la création du rapport. ID de corrélation <> | 500 | Assurez-vous que le format de date des champs « StartTime », « QueryStartTime » et « QueryEndTime » est correct. |
| Service indisponible | 500 | Si vous recevez continuellement une erreur de service non disponible (erreur 5xx), créez un [ticket de support](support.md). |
||||

## <a name="no-records"></a>Aucun enregistrement

Je reçois une réponse d’API 200 lorsque je télécharge le rapport à partir de l’emplacement sécurisé. Pourquoi est-ce que je n’obtiens aucun enregistrement ?

Vérifiez que la chaîne de la requête présente une valeur autorisée pour l’en-tête de colonne. Par exemple, cette requête ne renvoie aucun résultat :

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

Dans cet exemple, les valeurs autorisées pour SKUBillingType sont Payant ou Gratuit. Reportez-vous aux tableaux suivants pour connaître les valeurs possibles pour les différentes colonnes :

- [Tableau Détails des commandes](orders-dashboard.md#orders-details-table)
- [Tableau des détails de l’utilisation](usage-dashboard.md#usage-details-table)
- [Tableau des détails des commandes](customer-dashboard.md#customer-details-table)
- [Insights sur la place de marché – Tableau des détails](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Étapes suivantes

- [API permettant d’accéder aux données d’analyse de la place de marché commerciale](analytics-available-apis.md)
