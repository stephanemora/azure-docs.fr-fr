---
title: Collecter les journaux d’activité de ressources Azure dans l’espace de travail Log Analytics
description: Découvrez comment diffuser en continu les journaux de diagnostic Azure vers un espace de travail Log Analytics dans Azure Monitor.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 82738627b84713669cb6ddfc94c22b6f24b49e3a
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530848"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Collecter les journaux de plateforme Azure dans l’espace de travail Log Analytics dans Azure Monitor
[Les journaux de plateforme](resource-logs-overview.md) dans Azure, y compris le journal d’activité Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Cet article décrit la collecte de journaux de ressources dans un espace de travail Log Analytics, ce qui vous permet d’analyser ceux-ci avec d’autres données de surveillance collectées dans les journaux Azure Monitor à l’aide de puissantes requêtes de journal, ainsi que d’exploiter d’autres fonctionnalités d’Azure Monitor, telles que les alertes et les visualisations. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>Comment utiliser les journaux de plateforme dans un espace de travail
La collecte de journaux de plateforme dans un espace de travail Log Analytics vous permet d’analyser les journaux de toutes vos ressources Azure et d’utiliser toutes les fonctionnalités disponibles pour les [Journaux Azure Monitor](data-platform-logs.md), notamment :

* **Requêtes de journal** - Créez des [requêtes de journal](../log-query/log-query-overview.md) à l’aide d’un puissant langage de requête pour analyser et obtenir rapidement des informations sur vos données de diagnostic et les analyser avec les données issues d’autres sources dans Azure Monitor.
* **Génération d'alertes** - Recevez une notification proactive des conditions et modèles critiques identifiés dans vos journaux de ressources à l’aide des [alertes de journal dans Azure Monitor](alerts-log.md).
* **Visualisations** - Épinglez les résultats d’une requête de journal dans un tableau de bord Azure ou incluez-les dans un classeur en tant que rapport interactif.

## <a name="prerequisites"></a>Conditions préalables requises
Si vous n’en avez pas déjà, vous devez [créer un espace de travail](../learn/quick-create-workspace.md). Cet espace de travail ne doit pas nécessairement se trouver dans le même abonnement que la ressource qui envoie des journaux d’activité, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

## <a name="create-a-diagnostic-setting"></a>Créer un paramètre de diagnostic
Envoyez les journaux de plateforme à un espace de travail Log Analytics et à d’autres destinations en créant un paramètre de diagnostic pour une ressource Azure. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter des journaux et des métriques dans Azure](diagnostic-settings.md).


## <a name="activity-log-collection"></a>Collecte des journaux d’activité
Vous pouvez envoyer le journal d’activité d’un abonnement unique vers un maximum de cinq espaces de travail Log Analytics. Les données des journaux de ressources collectées dans un espace de travail Log Analytics sont stockées dans la table **AzureActivity**. 

## <a name="resource-log-collection-mode"></a>Mode de collecte des journaux de ressources
Les données des journaux de ressources collectées dans un espace de travail Log Analytics sont stockées dans des tables, comme décrit dans [Structure des journaux Azure Monitor](../log-query/logs-structure.md). Les tables utilisées par les journaux de ressources dépendent du type de collection que la ressource utilise :

- Diagnostics Azure - Toutes les données sont écrites dans la table _AzureDiagnostics_.
- Spécifique à la ressource - Les données sont écrites dans une table individuelle pour chaque catégorie de la ressource.

### <a name="azure-diagnostics-mode"></a>Mode Diagnostics Azure 
Dans ce mode, toutes les données, quel que soit le [paramètre de diagnostic](diagnostic-settings.md), sont collectées dans la table _AzureDiagnostics_. Il s’agit de la méthode héritée qu'utilise aujourd’hui la plupart des services Azure.

De nombreuses ressources envoyant des données à la même table, son schéma constitue le sur-ensemble des schémas des différents types de données collectés.

Prenons l’exemple suivant dans lequel les paramètres de diagnostic sont collectés dans le même espace de travail pour les types de données suivants :

- Journaux d'audit du service 1 (schéma constitué des colonnes A, B et C)  
- Journaux des erreurs du service 1 (schéma constitué des colonnes D, E et F)  
- Journaux d'audit du service 2 (schéma constitué des colonnes G, H et I)  

La table AzureDiagnostics se présente comme suit :  

| ResourceProvider    | Category     | Un  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Spécifique à la ressource
Dans ce mode, les tables individuelles de l’espace de travail sélectionné sont créées pour chaque catégorie sélectionnée dans le paramètre de diagnostic. Cette méthode est recommandée car elle facilite considérablement l’utilisation des données dans les requêtes de journal, offre une meilleure détectabilité des schémas et de leur structure, améliore les performances en termes de latence d’ingestion et de temps de requête, de même que la possibilité d’accorder des droits de contrôle d'accès en fonction du rôle (RBAC) sur une table spécifique. À termes, tous les services Azure migreront vers le mode Spécifique à la ressource. 

L’exemple ci-dessus se traduirait par la création de trois tables :
 
- Table *Service1AuditLogs* comme suit :

    | Fournisseur de ressources | Category | Un | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- Table *Service1ErrorLogs* comme suit :  

    | Fournisseur de ressources | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- Table *Service2AuditLogs* comme suit :  

    | Fournisseur de ressources | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>Sélectionner le mode de collecte
La plupart des ressources Azure écrivent des données dans l’espace de travail dans **Diagnostics Azure** ou le **mode Spécifique à la ressource**, sans vous laisser le choix. Pour plus d’informations sur le mode utilisé, consultez la [documentation propre à chaque service](diagnostic-logs-schema.md). À terme, tous les services Azure utiliseront le mode Spécifique à la ressource. Dans le cadre de cette transition, certaines ressources vous permettront de sélectionner un mode dans le paramètre de diagnostic. Il vous est conseillé d'opter pour le mode Spécifique à la ressource pour les nouveaux paramètres de diagnostic, car cela facilite la gestion des données et vous évitera plus tard de complexes migrations.
  
   ![Sélecteur du mode Paramètres de diagnostic](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Actuellement, **Diagnostics Azure** et le mode **Spécifique à la ressource** peuvent uniquement être sélectionnés lors de la configuration du paramètre de diagnostic dans le portail Azure. Si vous configurez le paramètre à l’aide de l’interface CLI, de PowerShell ou de l’API REST, la valeur par défaut est **Diagnostics Azure**.

Vous pouvez modifier un paramètre de diagnostic existant sur le mode Spécifique à la ressource. Dans ce cas, les données déjà collectées sont conservées dans la table _AzureDiagnostics_ jusqu’à leur suppression conformément au paramètre de conservation pour l'espace de travail. Les nouvelles données sont collectées dans la table dédiée. Utilisez l’opérateur [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) pour interroger les données dans les deux tables.

Consultez régulièrement le blog [Mises à jour Azure](https://azure.microsoft.com/updates/) pour vous tenir informé des annonces relatives aux services Azure prenant en charge le mode Spécifique à la ressource.

### <a name="column-limit-in-azurediagnostics"></a>Limite de colonnes dans AzureDiagnostics
Dans les journaux d'activité Azure Monitor, les propriétés sont limitées à 500. Une fois cette limite atteinte, toutes les lignes contenant des données avec plus de 500 propriétés sont supprimées au moment de l'ingestion. La table *AzureDiagnostics* est particulièrement sensible à cette limite car elle comprend les propriétés de tous les services Azure qui y écrivent.

Si vous collectez des journaux de ressources à partir de plusieurs services, _AzureDiagnostics_ peut dépasser cette limite et certaines données n’y seront pas consignées. En attendant que tous les services Azure prennent en charge le mode Spécifique à la ressources, il vous est conseillé de configurer les ressources de manière à ce qu'elles écrivent dans plusieurs espaces de travail afin d'éviter d’atteindre la limite de 500 colonnes.

### <a name="azure-data-factory"></a>Azure Data Factory
En raison d’un ensemble très détaillé de journaux, Azure Data Factory est connu pour écrire un grand nombre de colonnes et, peut amener _AzureDiagnostics_ à dépasser sa limite. Pour tous les paramètres de diagnostic configurés avant l’activation du mode Spécifique à la ressource, une colonne est créée pour chaque paramètre utilisateur portant un nom unique par rapport à une activité. D’autres colonnes sont créées en raison de la nature détaillée des entrées et des sorties d’activité.
 
Vous devez migrer vos journaux pour utiliser le mode Spécifique à la ressource dans les meilleurs délais. Si vous ne pouvez pas le faire immédiatement, une alternative temporaire consiste à isoler les journaux Azure Data Factory dans leur propre espace de travail afin de minimiser les risques d'impact de ceux-ci sur d'autres types de journaux collectés dans vos espaces de travail.


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez plus d’informations sur les journaux de ressource](resource-logs-overview.md)
* [Créer un paramètre de diagnostic pour collecter les journaux et les mesures dans Azure](diagnostic-settings.md).
