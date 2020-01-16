---
title: Collecter le journal d’activité Azure avec les paramètres de diagnostic (préversion) – Azure Monitor | Microsoft Docs
description: Utilisez des paramètres de diagnostic pour transférer les journaux d’activité Azure vers les journaux Azure Monitor, Stockage Azure ou Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 12/20/2019
ms.openlocfilehash: 55efdfe2bb1b37e566654b8041f2cf5ed411cc3f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977568"
---
# <a name="collect-azure-activity-log-with-legacy-settings"></a>Collecter le journal d’activité Azure avec des paramètres hérités
Le [journal d’activité Azure](platform-logs-overview.md) est un [journal de plateforme](platform-logs-overview.md) qui fournit un aperçu de tous les événements de niveau d’abonnement qui se sont produits dans Azure. Jusqu’à récemment, vous avez créé un profil de journal pour envoyer des entrées de journal d’activité à un [hub d’événements ou à un compte de stockage](activity-log-export.md) et utilisé un connecteur pour les collecter dans un [espace de travail Log Analytics](activity-log-collect.md). Cet article compare les méthodes, et explique comment utiliser les paramètres hérités existants et comment effacer les paramètres hérités dans la préparation des paramètres de diagnostic.


## <a name="differences-between-methods"></a>Différences entre les méthodes

### <a name="advantages"></a>Avantages
L’utilisation des paramètres de diagnostic présente les avantages suivants par rapport aux méthodes actuelles :

- Méthode cohérente pour collecter tous les journaux de plateforme.
- Collecte du journal d’activité pour plusieurs abonnements et locataires.
- Filtrage de la collecte pour collecter uniquement les journaux de catégories spécifiques.
- Collecte de toutes les catégories du journal d’activité. Certaines catégories ne sont pas collectées à l’aide de la méthode héritée.
- Latence plus rapide pour l’ingestion des journaux. La méthode précédente a une latence d’environ 15 minutes, tandis que les paramètres de diagnostic n’ajoutent qu’environ 1 minute.

### <a name="considerations"></a>Considérations
Avant d’activer cette fonctionnalité, tenez compte des détails suivants de la collecte des journaux d’activité à l’aide des paramètres de diagnostic.

- Le paramètre de rétention pour la collecte du journal d’activité sur le stockage Azure a été supprimé, ce qui signifie que les données seront stockées indéfiniment jusqu’à ce que vous les supprimiez.
- Vous pouvez uniquement créer un paramètre de diagnostic de niveau d’abonnement à l’aide du portail Azure. Pour utiliser d’autres méthodes telles que PowerShell ou CLI, vous pouvez créer un modèle Resource Manager.


### <a name="differences-in-data"></a>Différences de données
Les paramètres de diagnostic collectent les mêmes données que les méthodes précédentes utilisées pour collecter le journal d’activité, avec les différences actuelles suivantes :

Les propriétés suivantes ont été supprimées :

- ActivityStatus
- ActivitySubstatus
- NomOpération
- ResourceProvider

Les propriétés suivantes ont été ajoutées :

- Authorization_d
- Claims_d
- Properties_d

## <a name="work-with-legacy-settings"></a>Utiliser des paramètres hérités
Les paramètres hérités pour la collecte du journal d’activité continuent de fonctionner si vous ne choisissez pas de substituer un paramètre de diagnostic. Utilisez la méthode suivante pour gérer le profil de journal lié à un abonnement.

1. À partir du menu **Azure Monitor** du Portail Azure, sélectionnez **Journal d’activité**.
3. Cliquez sur **Paramètres de diagnostic**.

   ![Paramètres de diagnostic](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Cliquez sur la bannière violette pour obtenir l’expérience héritée.

    ![Expérience héritée](media/diagnostic-settings-subscription/legacy-experience.png)


Consultez les articles suivants pour plus d’informations sur l’utilisation des méthodes de collecte héritées.

- [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](activity-log-collect.md)
- [Collecter les journaux d’activité Azure dans Azure Monitor auprès de locataires Azure Active Directory](activity-log-collect-tenants.md)
- [Exporter le journal d’activité vers le stockage ou Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Désactiver des paramètres existants
Vous devez désactiver la collecte existante de l’activité avant de l’activer à l’aide des paramètres de diagnostic. Si les deux sont activées, les données peuvent être dupliquées.

### <a name="disable-collection-into-log-analytics-workspace"></a>Désactiver la collecte dans l’espace de travail Log Analytics

1. Ouvrez le menu **Espaces de travail Log Analytics** du Portail Azure et sélectionnez l’espace de travail pour collecter le journal d’activité.
2. Dans la section **Sources de données de l’espace de travail** du menu de l’espace de travail, sélectionnez **Journal d’activité Azure**.
3. Cliquez sur l’abonnement que vous souhaitez déconnecter.
4. Cliquez sur **Déconnecter**, puis sur **Oui** lorsque vous êtes invité à confirmer votre choix.

### <a name="disable-log-profile"></a>Désactiver un profil de journal

1. Utilisez la procédure décrite dans [Utiliser des paramètres hérités](#work-with-legacy-settings) pour ouvrir les paramètres hérités.
2. Désactivez toute collecte dans le stockage ou des hubs d’événements.



## <a name="activity-log-monitoring-solution"></a>Solution de supervision du journal d’activité
La solution de supervision Activity Log Analytics inclut plusieurs requêtes de journal et vues pour analyser les enregistrements du journal d’activité dans votre espace de travail Log Analytics. Cette solution utilise les données de journal collectées dans un espace de travail Log Analytics et continue de fonctionner sans aucune modification si vous collectez le journal d’activité à l’aide des paramètres de diagnostic. Pour plus d’informations sur cette solution, consultez [Solution de supervision Activity Log Analytics](activity-log-collect.md#activity-logs-analytics-monitoring-solution).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le journal d’activité](../../azure-resource-manager/management/view-activity-logs.md)
* [En savoir plus sur les paramètres de diagnostic](diagnostic-settings.md)
