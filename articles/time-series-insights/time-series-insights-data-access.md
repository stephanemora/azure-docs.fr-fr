---
title: Configurer la sécurité pour accéder et gérer Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment configurer la sécurité et les autorisations sous forme de stratégies d’accès de gestion et d’accès aux données pour sécuriser Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364809"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Accorder l’accès aux données d’un environnement Time Series Insights à l’aide du portail Azure

Les environnements Time Series Insights comprennent deux types indépendants des stratégies d’accès :

* Stratégies d’accès de gestion
* Stratégies d’accès aux données

Ces deux stratégies accordent aux principaux Azure Active Directory (utilisateurs et applications) diverses autorisations sur un environnement spécifique. Les principaux (utilisateurs et applications) doivent appartenir à l’annuaire Active Directory (ou « locataire Azure ») associé à l’abonnement contenant l’environnement.

Les stratégies d’accès de gestion accordent des autorisations liées à la configuration de l’environnement, telles que la
*   création et la suppression de l’environnement, des sources d’événements, des ensembles de données de référence et la
*   gestion des stratégies d’accès aux données.

Les stratégies d’accès aux données accordent des autorisations pour générer des requêtes de données, manipuler des données de référence dans l’environnement, et des requêtes partagées enregistrées et des perspectives associées à l’environnement.

Les deux types de stratégies permettent de distinguer clairement l’accès à la gestion de l’environnement et l’accès aux données contenues dans l’environnement. Par exemple, vous pouvez configurer un environnement de sorte que le propriétaire/créateur de l’environnement soit supprimé de l’accès aux données. Par ailleurs, les utilisateurs et services qui sont autorisés à lire des données de l’environnement peuvent ne pas avoir accès à la configuration de l’environnement.

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [Guide pratique pour ajouter une source d’événement Event Hub à votre environnement Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement.
* Affichez votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
