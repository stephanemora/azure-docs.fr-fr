---
title: Collecter le journal d’activité Azure avec les paramètres de diagnostic – Azure Monitor | Microsoft Docs
description: Utilisez des paramètres de diagnostic pour transférer les journaux d’activité Azure vers les journaux Azure Monitor, Stockage Azure ou Azure Event Hubs.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096896"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Mettre à jour la collecte et l'exportation du journal d'activité Azure
Le [journal d’activité Azure](platform-logs-overview.md) est un [journal de plateforme](platform-logs-overview.md) qui fournit un aperçu de tous les événements de niveau d’abonnement qui se sont produits dans Azure. La méthode d'envoi des entrées du journal d'activité vers [un hub d'événements ou un compte de stockage](activity-log-export.md) ou vers un [espace de travail Log Analytics](activity-log-collect.md) a changé pour utiliser les [paramètres de diagnostic](diagnostic-settings.md). Cet article compare les méthodes, et explique comment effacer les paramètres hérités en vue de passer aux paramètres de diagnostic.


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

Les colonnes suivantes ont été supprimées. Le remplacement de ces colonnes se fait dans un format différent. Par conséquent, vous devrez peut-être modifier les requêtes de journal qui les utilisent. Les colonnes supprimées dans le schéma seront peut-être encore visibles, mais elles ne contiendront pas de données.

| Colonne supprimée | Colonne de remplacement |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| NomOpération     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

La colonne suivante a été ajoutée :

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> Dans certains cas, les valeurs de ces colonnes peuvent être en majuscules. Si vous avez une requête qui inclut ces colonnes, vous devez utiliser l’[opérateur =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) pour effectuer une comparaison non sensible à la casse.

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
