---
title: Comment configurer la conservation des données dans votre environnement Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment configurer la conservation des données dans votre environnement Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.custom: seodec18
ms.openlocfilehash: d88bf309d2afeb4e6fdd09ff1317bec3fec2fe75
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695849"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configuration de la conservation des données dans Time Series Insights
Cet article explique comment configurer la **durée de conservation des données** et le **comportement de limite de stockage dépassée** dans Azure Time Series Insights.

Chaque environnement Time Series Insights (TSI) dispose d’un paramètre permettant de configurer la **durée de conservation des données**. La valeur est comprise entre 1 et 400 jours. Les données sont supprimées en fonction de la capacité de stockage ou de la durée de conservation de l’environnement (1-400), selon ce qui se présente en premier.

Chaque environnement TSI dispose d’un paramètre supplémentaire **Comportement de limite de stockage dépassée**. Ce paramètre contrôle le comportement d’entrée et de vidage lorsque la capacité maximale d’un environnement est atteinte. Vous pouvez choisir entre deux comportements :
- **Vidage des données anciennes** (par défaut)  
- **Suspendre l’entrée**

Pour plus d’informations sur ces paramètres, voir [Présentation de la conservation des données dans Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurer la rétention de données

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights existant. Sélectionnez **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez votre environnement Time Series Insights.

3. Sous le titre **PARAMÈTRES**, sélectionnez **Configurer**.

4. Sélectionnez la **durée de conservation des données** pour configurer la conservation à l’aide du curseur, ou saisissez un nombre dans la zone de texte.

5. Notez le paramètre **Capacité**, car il affecte la quantité maximale d’événements de données et la capacité de stockage totale pour le stockage des données. 

6. Activez/désactivez le paramètre **Comportement de limite de stockage dépassée**. Sélectionnez le comportement **Vidage des données anciennes** ou **Suspendre l’entrée**.

7. Sélectionnez **Enregistrer** pour configurer les modifications.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, voir [Présentation de la conservation des données dans Time Series Insights](time-series-insights-concepts-retention.md).