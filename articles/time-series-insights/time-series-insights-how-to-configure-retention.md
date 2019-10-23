---
title: Comment configurer la conservation des données dans votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment configurer la conservation des données dans votre environnement Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: bf6788bac7003bec0ccfc171d622cd9b28bf43e0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330886"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configuration de la conservation des données dans Time Series Insights

Cet article explique comment configurer la **durée de conservation des données** et le **comportement de limite de stockage dépassée** dans Azure Time Series Insights.

## <a name="summary"></a>Résumé

Chaque environnement Azure Time Series Insights dispose d’un paramètre permettant de configurer la **durée de conservation des données**. La valeur est comprise entre 1 et 400 jours. Les données sont supprimées en fonction de la capacité de stockage ou de la durée de conservation de l’environnement (1-400), selon ce qui se présente en premier.

Chaque environnement Time Series Insights dispose d’un paramètre supplémentaire **Comportement de limite de stockage dépassée**. Ce paramètre contrôle le comportement d’entrée et de vidage lorsque la capacité maximale d’un environnement est atteinte. Vous pouvez choisir entre deux comportements :

- **Vidage des données anciennes** (par défaut)
- **Suspendre l’entrée**

Pour plus d’informations sur ces paramètres, voir [Présentation de la conservation des données dans Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurer la rétention de données

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Recherchez votre environnement Time Series Insights existant. Sélectionnez **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez votre environnement Time Series Insights.

1. Sous le titre **Paramètres**, sélectionnez **Configurer**.

    [![Sélectionnez Paramètres, puis Configurer](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Sélectionnez la **durée de conservation des données (en jours)** pour configurer la conservation à l’aide du curseur, ou saisissez un nombre dans la zone de texte.

1. Notez le paramètre **Capacité**, car il affecte la quantité maximale d’événements de données et la capacité de stockage totale pour le stockage des données.

1. Activez/désactivez le paramètre **Comportement de limite de stockage dépassée**. Sélectionnez le comportement **Vidage des données anciennes** ou **Suspendre l’entrée**.

    [![Conservation des données - Accepter et enregistrer.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Acceptez la case à cocher indiquant que vous avez consulté la documentation et que vous comprenez les risques potentiels de perte de données. Sélectionnez **Enregistrer** pour configurer les modifications.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, voir [Présentation de la conservation des données dans Time Series Insights](time-series-insights-concepts-retention.md).

- Apprenez comment [Mettre à l’échelle votre environnement Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- En savoir plus sur la [planification de votre environnement](time-series-insights-environment-planning.md).