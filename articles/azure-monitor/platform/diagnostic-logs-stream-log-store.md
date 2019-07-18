---
title: Diffuser en continu les journaux de diagnostic Azure vers un espace de travail Log Analytics d'Azure Monitor
description: Apprenez à diffuser en continu les journaux de diagnostic Azure vers un espace de travail Log Analytics d'Azure Monitor.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 13eb1a8fcea2f74cda5921a51b8c2e8816be975f
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303716"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>Diffuser en continu les journaux de diagnostic Azure vers un espace de travail Log Analytics d'Azure Monitor

**[Les journaux de diagnostic Azure](diagnostic-logs-overview.md)** peuvent être diffusés en continu et quasiment en temps réel vers un espace de travail Log Analytics d'Azure Monitor à l'aide du portail, des cmdlets PowerShell ou d'Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>Ce que vous pouvez faire avec les journaux de diagnostic dans un espace de travail Log Analytics

Azure Monitor est un outil flexible d'analytique et d'interrogation de journaux qui vous offre une meilleure visibilité sur les données de journal brutes générées à partir des ressources Azure. Il inclut, entre autres, les fonctionnalités suivantes :

* **Interrogation des journaux** : écrivez des requêtes avancées pour interroger les données de vos journaux, mettez des journaux en corrélation à partir de différentes sources, et générez des graphiques qui peuvent être épinglés à votre tableau de bord Azure.
* **Génération d'alertes** : soyez informé par e-mail ou appel webhook lorsqu'un ou plusieurs événements correspond(ent) à une requête particulière grâce aux alertes d'Azure Monitor.
* **Analytique avancée** : appliquez des algorithmes de Machine Learning et de correspondance à des critères spéciaux pour identifier d’éventuels problèmes consignés dans vos journaux d’activité.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>Activer la diffusion en continu des journaux de diagnostic vers l'espace de travail Log Analytics

Vous pouvez activer la diffusion en continu des journaux de diagnostic par programme, via le portail ou à l’aide des [API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Dans tous les cas, vous créez un paramètre de diagnostic dans lequel vous spécifiez un espace de travail Log Analytics et les catégories de journal d’activité et les indicateurs de performance que vous voulez envoyer dans cet espace de travail. Une **catégorie de journal** de diagnostic est un type de journal qu’une ressource peut générer.

Il n’est pas nécessaire que l’espace de travail Log Analytics se trouve dans le même abonnement que la ressource générant des journaux d’activité, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diffuser en continu les journaux de diagnostic à l’aide du portail
1. Sur le portail, accédez à Azure Monitor et cliquez sur **Paramètres de diagnostic** dans le menu **Paramètres**.


2. Vous pouvez également filtrer la liste par type ou groupe de ressources, puis cliquez sur la ressource pour laquelle vous souhaitez définir un paramètre de diagnostic.

3. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur « Activer les diagnostics ».

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   S’il existe des paramètres existants sur la ressource, vous verrez une liste de paramètres déjà configurés sur cette ressource. Cliquez sur « Ajouter le paramètre de diagnostic ».

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Donnez un nom à votre définition, cochez la case **Envoyer à Log Analytics**, puis sélectionnez un espace de travail Log Analytics.

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux de diagnostic sont diffusés en continu dans cet espace de travail dès que de nouvelles données d’événement sont générées. Un délai de 15 minutes peut s’écouler entre l’événement et sa consignation dans Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour activer la diffusion en continu via les [applets de commande Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), vous pouvez utiliser l’applet de commande `Set-AzDiagnosticSetting` avec ces paramètres :

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Notez que la propriété workspaceID tient compte de l’ID complet de la ressource Azure dans l’espace de travail, et non de la clé/de l’ID de l’espace de travail affiché(e) dans le portail Log Analytics.

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure

Pour activer la diffusion en continu par le biais [d’Azure CLI](../../azure-monitor/platform/cli-samples.md), vous pouvez utiliser la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Vous pouvez ajouter des catégories supplémentaires dans le journal de diagnostic par l’adjonction de dictionnaires au tableau JSON transmis en tant que paramètre `--logs`.

L’argument `--resource-group` est obligatoire seulement si `--workspace` n’est pas un ID d’objet.

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>Comment interroger les données dans un espace de travail Log Analytics ?

Dans le panneau Journaux du portail Azure Monitor, vous pouvez interroger les journaux de diagnostic dans le cadre de la solution de gestion des journaux au niveau de la table AzureDiagnostics. Il existe également [pour les ressources Azure plusieurs solutions de supervision](../../azure-monitor/insights/solutions.md) que vous pouvez installer pour bénéficier d'une visibilité immédiate sur les données de journal que vous envoyez à Azure Monitor.

## <a name="azure-diagnostics-vs-resource-specific"></a>Diagnostics Azure contre mode Spécifique à la ressource  
Une fois qu’une destination Log Analytics est activée dans une configuration de Diagnostics Azure, les données s’afficheront de deux façons distinctes dans votre espace de travail :  
- **Diagnostics Azure** est l’ancienne méthode aujourd’hui utilisée par la plupart des services Azure. Dans ce mode, toutes les données de n’importe quel paramètre de diagnostic pointant vers un espace de travail donné se retrouvent dans la table _AzureDiagnostics_. 
<br><br>Dans la mesure où de nombreuses ressources envoient des données à la même table (_AzureDiagnostics_), le schéma de cette table constitue le super-ensemble des schémas des différents types de données collectés. Par exemple, si vous avez créé des paramètres de diagnostic pour la collecte des types de données suivants, ils sont tous envoyés au même espace de travail :
    - Journaux d'audit de la Ressource 1 (schéma constitué des colonnes A, B et C)  
    - Journaux d'erreurs de la Ressource 2 (schéma constitué des colonnes D, E et F)  
    - Journaux de flux de données de la Ressource 3 (schéma constitué des colonnes G, H et I)  

    La table AzureDiagnostics se présente comme suit, avec des exemples de données :  

    | ResourceProvider | Catégorie | A | b | C | D | E | F | G | H | I |
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
    | Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
    | Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
    | Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
    | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- **Propre à la ressource** - Dans ce mode, les tables individuelles dans l’espace de travail sélectionné sont créées par chaque catégorie sélectionnée dans la configuration des paramètres de diagnostic. Cette méthode plus récente permet de trouver beaucoup plus facilement exactement ce que vous souhaitez via une séparation explicite des problèmes : une table pour chaque catégorie. En outre, elle offre des avantages dans sa prise en charge des types dynamiques. Vous pouvez déjà voir ce mode pour certains types de ressources Azure, comme les journaux [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ou [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor). Au final, nous nous attendons à ce que chaque type de données migre vers le mode Propre à la ressource. 

    Dans l’exemple ci-dessus, cela se traduirait par la création de trois tables : 
    - La table _AuditLogs_ comme suit :

        | ResourceProvider | Catégorie | A | b | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - La table _ErrorLogs_ comme suit :  

        | ResourceProvider | Catégorie | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - La table _DataFlowLogs_ comme suit :  

        | ResourceProvider | Catégorie | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    Les autres avantages de l’utilisation du mode Propre à la ressource comprennent l’amélioration des performances à la fois en matière de latence d’ingestion et de durée de requête, une meilleure détectabilité des schémas et de leur structure, la capacité d’octroyer des droits RBAC sur une table spécifique et bien plus encore.

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>Choix du mode Diagnostic Azure ou Propre à la ressource
Pour la plupart des ressources Azure, vous n’aurez pas le choix d’utiliser le mode Diagnostic Azure ou Propre à la ressource : les données circuleront automatiquement via la méthode que la ressource a choisi d’utiliser. Consultez la documentation fournie par la ressource que vous avez autorisée à envoyer des données à Log Analytics pour plus d’informations sur le mode employé. 

Comme indiqué dans la section précédente, l’objectif final d’Azure Monitor est que tous les services dans Azure utilisent le mode Propre à la ressource. Pour faciliter cette transition et s’assurer qu’aucune donnée n’est perdue en cours de route, certains services Azure proposeront une sélection du mode lors de l’intégration à Log Analytics :  
   ![Sélecteur du mode Paramètres de diagnostic](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

Nous vous conseillons **fortement**, pour éviter des migrations potentiellement difficiles à l’avenir, d’utiliser le mode Propre à la ressource pour les paramètres de diagnostic nouvellement créés.  

Pour les paramètres de diagnostic existants, après activation par une ressource Azure particulière, vous ne pourrez pas rétroactivement basculer des diagnostics Azure au mode Propre à la ressource. Vos données ingérées précédemment continueront à être disponibles dans la table _AzureDiagnostics_ jusqu'à ce qu’elles deviennent obsolètes, comme configuré dans votre paramètre de rétention sur l’espace de travail, mais toutes les nouvelles données seront envoyées à la table dédiée. Cela signifie que pour les requêtes qui doivent couvrir à la fois les anciennes données et les nouvelles (jusqu'à ce que les anciennes données deviennent entièrement obsolètes), un opérateur [union](https://docs.microsoft.com/azure/kusto/query/unionoperator) dans vos requêtes sera nécessaire pour combiner les deux jeux de données.

Recherchez les dernières actualités concernant les nouveaux journaux de soutien des services Azure en mode Propre à la ressource sur le blog [Mises à jour Azure](https://azure.microsoft.com/updates/) !

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Limitation connue : limite de colonnes dans AzureDiagnostics
Il existe une limite explicite qui empêche une table de journal Azure donnée de dépasser les 500 colonnes. Une fois cette limite atteinte, toutes les lignes contenant des données avec plus de 500 colonnes sont supprimées au moment de l'ingestion. La table AzureDiagnostics est particulièrement susceptible d'être impactée par cette limite. Celle-ci est généralement atteinte lorsqu'une grande variété de sources de données ou plusieurs sources de données détaillées sont envoyées dans le même espace de travail. 

#### <a name="azure-data-factory"></a>Azure Data Factory  
Compte tenu de ses journaux très détaillés, Azure Data Factory est particulièrement impacté par cette limite. En particulier, pour les paramètres de diagnostic configurés avant l’activation du mode Propre à la ressource ou choisissant explicitement d’utiliser le mode Propre à la ressource pour des raisons de compatibilité inverse :  
- *Paramètres utilisateur définis pour toute activité de votre pipeline* : une nouvelle colonne est créée pour chaque paramètre utilisateur portant un nom unique et pour chaque activité. 
- *Entrées et sorties d'activité* : celles-ci varient d'une activité à l'autre et génèrent un grand nombre de colonnes en raison de leur nature détaillée. 
 
Comme avec les suggestions de contournement plus générales ci-dessous, il est recommandé de migrer vos journaux pour utiliser le mode Propre à la ressource dès que possible. Si vous ne pouvez pas le faire immédiatement, une alternative temporaire est d'isoler les journaux ADF dans leur propre espace de travail afin de minimiser les risques d'impact de ceux-ci sur d'autres types de journaux collectés dans vos espaces de travail. 
 
#### <a name="workarounds"></a>Solutions de contournement
À court terme, jusqu'à ce que tous les services Azure soient activés en mode Propre à la ressource, pour tous les services non encore pris en charge le mode Propre à la ressource, il est recommandé de séparer les types de données détaillés publiés par ces services dans des espaces de travail distincts afin de réduire les risques d’atteinte de limite.  
 
À long terme, les diagnostics Azure migreront vers tous les services Azure prenant en charge le mode Propre à la ressource. Nous vous recommandons de passer à ce mode dès que possible pour réduire le risque potentiel d’atteindre cette limitation de 500 colonnes.  


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de diagnostic Azure](diagnostic-logs-overview.md)

