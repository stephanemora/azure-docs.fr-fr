---
title: Collecter le journal d’activité Azure avec les paramètres de diagnostic (préversion) – Azure Monitor | Microsoft Docs
description: Utilisez des paramètres de diagnostic pour transférer les journaux d’activité Azure vers les journaux Azure Monitor, Stockage Azure ou Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586990"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Collecter le journal d’activité Azure avec les paramètres de diagnostic (préversion)
Le [journal d’activité Azure](activity-logs-overview.md) est un [journal de plateforme](platform-logs-overview.md) qui fournit un aperçu de tous les événements de niveau d’abonnement qui se sont produits dans Azure. Jusqu’à présent, vous avez créé un profil de journal pour envoyer des entrées de journal d’activité à un [Event Hub ou à un compte de stockage](activity-log-export.md) et utilisé un connecteur pour les collecter dans un [espace de travail Log Analytics](activity-log-collect.md).

Vous pouvez maintenant configurer la collecte du journal d’activité Azure à l’aide des mêmes [paramètres de diagnostic](diagnostic-settings.md) que ceux utilisés pour collecter les [journaux de ressources](resource-logs-overview.md). L’utilisation des paramètres de diagnostic présente les avantages suivants par rapport aux méthodes actuelles :

- Méthode cohérente pour collecter tous les journaux de plateforme.
- Collecte du journal d’activité pour plusieurs abonnements et locataires.
- Filtrage de la collecte pour collecter uniquement les journaux de catégories spécifiques.

## <a name="considerations"></a>Considérations
Avant d’activer cette fonctionnalité, tenez compte des détails suivants de la collecte des journaux d’activité à l’aide des paramètres de diagnostic.

- Vous devez désactiver la collecte existante de l’activité avant de l’activer à l’aide des paramètres de diagnostic. Si les deux sont activées, les données peuvent être dupliquées.
- Le paramètre de rétention pour la collecte du journal d’activité sur le stockage Azure a été supprimé, ce qui signifie que les données seront stockées indéfiniment jusqu’à ce que vous les supprimiez.
- Vous pouvez envoyer le journal d’activité à partir de plusieurs abonnements vers un seul espace de travail Log Analytics. Vous pouvez envoyer le journal d’activité d’un abonnement unique vers un maximum de cinq espaces de travail Log Analytics.

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic
Utilisez la procédure suivante pour créer un paramètre de diagnostic dans le Portail Azure pour collecter le journal d’activité Azure. Actuellement, vous ne pouvez pas créer un paramètre de diagnostic de niveau d’abonnement à l’aide d’autres méthodes.

1. Désactivez la collecte existante de l’espace de travail Log Analytics pour le journal d’activité :
   1. Ouvrez le menu **Espaces de travail Log Analytics** du Portail Azure et sélectionnez l’espace de travail pour collecter le journal d’activité.
   2. Dans la section **Sources de données de l’espace de travail** du menu de l’espace de travail, sélectionnez **Journal d’activité Azure**.
   3. Cliquez sur l’abonnement que vous souhaitez déconnecter.
   4. Cliquez sur **Déconnecter**, puis sur **Oui** lorsque vous êtes invité à confirmer votre choix.
2. À partir du menu **Azure Monitor** du Portail Azure, sélectionnez **Journal d’activité**.
3. Cliquez sur **Paramètres de diagnostic**.
   
   ![Paramètres de diagnostic](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Cliquez sur la bannière violette pour l’expérience héritée et désactivez toutes les collectes en cours sur le stockage ou les Event Hubs. 

    ![Expérience héritée](media/diagnostic-settings-subscription/legacy-experience.png)

5. Suivez les procédures décrites dans [Créer des paramètres de diagnostic dans le portail Azure](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) pour créer un paramètre de diagnostic. Pour obtenir une explication des catégories que vous pouvez utiliser pour filtrer les événements à partir du journal d’activité, consultez [Catégories dans le journal d’activités](activity-logs-overview.md#categories-in-the-activity-log). 


## <a name="differences-in-data"></a>Différences de données
Les paramètres de diagnostic collectent les mêmes données que les méthodes précédentes utilisées pour collecter le journal d’activité, avec les différences actuelles suivantes :

Les propriétés suivantes ont été supprimées :

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

Les propriétés suivantes ont été ajoutées :

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>Solution de supervision du journal d’activité
La solution de supervision Activity Log Analytics inclut plusieurs requêtes de journal et vues pour analyser les enregistrements du journal d’activité dans votre espace de travail Log Analytics. Cette solution utilise les données de journal collectées dans un espace de travail Log Analytics et continue de fonctionner sans aucune modification si vous collectez le journal d’activité à l’aide des paramètres de diagnostic. Pour plus d’informations sur cette solution, consultez [Solution de supervision Activity Log Analytics](activity-log-collect.md#activity-logs-analytics-monitoring-solution).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le journal d’activité](../../azure-resource-manager/resource-group-audit.md)
* [En savoir plus sur les paramètres de diagnostic](diagnostic-settings.md)
