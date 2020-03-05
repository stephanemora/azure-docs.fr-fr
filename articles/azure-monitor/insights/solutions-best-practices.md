---
title: Bonnes pratiques en matière de solution de gestion dans Azure | Microsoft Docs
description: ''
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 837fb87d73698961ec1550b122840563d1707f8a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663196"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Bonnes pratiques pour la création de solutions de gestion dans Azure (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans Azure qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.  

Cet article décrit les bonnes pratiques pour la [création d’un fichier de solution de gestion](solutions-solution-file.md) dans Azure.  Ces informations seront mises à jour lorsque de meilleures pratiques supplémentaires seront identifiées.

## <a name="data-sources"></a>Sources de données
- Des sources de données peuvent être [configurées avec un modèle Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), mais elles ne doivent pas être incluses dans un fichier solution.  La raison en est que la configuration des sources de données n’est actuellement pas idempotente, ce qui signifie que votre solution pourrait remplacer la configuration existante dans l’espace de travail de l’utilisateur.<br><br>Par exemple, votre solution peut nécessiter des événements d’avertissement et d’erreur du journal des événements de l’application.  Si vous spécifiez cela en tant source de données dans votre solution, vous risquez de supprimer des événements d’informations si l’utilisateur avait configuré cela dans son espace de travail.  Si vous avez inclus tous les événements, vous collecterez peut-être trop d’événements d’informations dans l’espace de travail de l’utilisateur.

- Si votre solution nécessite des données à partir d’une des sources de données standard, vous devez définir cela comme une condition préalable.  Indiquez dans la documentation que le client doit configurer la source de données lui-même.  
- Ajoutez un message de [vérification du flux de données](../../azure-monitor/platform/view-designer-tiles.md) à toutes les vues dans votre solution pour indiquer à l’utilisateur les sources de données qui doivent être configurées pour les données requises à collecter.  Ce message s’affiche sur la vignette de l’affichage lorsque les données requises ne sont pas trouvées.


## <a name="runbooks"></a>Runbooks
- Ajoutez une [planification d’automation](../../automation/automation-schedules.md) pour chaque runbook dans votre solution qui doit s’exécuter selon une planification.
- Incluez le [module IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) dans votre solution pour qu’il puisse être utilisé par les runbooks pour écrire des données dans le dépôt Log Analytics.  Configurez la solution pour [référencer](solutions-solution-file.md#solution-resource) cette ressource afin qu’elle reste si la solution est supprimée.  Cela permet à plusieurs solutions de partager le module.
- Utilisez des [variables Automation](../../automation/automation-schedules.md) pour fournir des valeurs à la solution que les utilisateurs souhaiteront peut-être modifier ultérieurement.  Même si la solution est configurée pour contenir la variable, sa valeur peut toujours être modifiée.

## <a name="views"></a>Les vues
- Toutes les solutions doivent inclure une vue unique qui s’affiche dans le portail de l’utilisateur.  La vue peut contenir plusieurs [parties de visualisation](../../azure-monitor/platform/view-designer-parts.md) pour illustrer les différents ensembles de données.
- Ajoutez un message de [vérification du flux de données](../../azure-monitor/platform/view-designer-tiles.md) à toutes les vues dans votre solution pour indiquer à l’utilisateur les sources de données qui doivent être configurées pour les données requises à collecter.
- Configurez la solution pour qu’elle [contienne](solutions-solution-file.md#solution-resource) la vue afin qu’elle soit supprimée si la solution est supprimée.

## <a name="alerts"></a>Alertes
- Définissez la liste des destinataires en tant que paramètre dans le fichier solution afin que l’utilisateur puisse les définir lorsqu’ils installent la solution.
- Configurez la solution pour qu’elle fasse [référence](solutions-solution-file.md#solution-resource) aux règles d’alerte de façon à ce que les utilisateurs puissent modifier leur configuration.  Ils souhaiteront peut-être apporter des modifications telles que la modification de la liste des destinataires, la modification du seuil de l’alerte ou la désactivation de la règle d’alerte. 


## <a name="next-steps"></a>Étapes suivantes
* Passez en revue le processus de base de [conception et de création d’une solution de gestion](solutions-creating.md).
* Découvrez comment [créer un fichier solution](solutions-solution-file.md).
* [Ajoutez des alertes et des recherches enregistrées](solutions-resources-searches-alerts.md) à votre solution de gestion.
* [Ajoutez des vues](solutions-resources-views.md) à votre solution de gestion.
* [Ajoutez des runbooks Automation et d’autres ressources](solutions-resources-automation.md) à votre solution de gestion.

