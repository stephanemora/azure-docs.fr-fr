---
title: Présentation des journaux de diagnostic Azure
description: Découvrez les journaux de diagnostic Azure et comment les utiliser pour comprendre les événements qui se produisent au sein d’une ressource Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 890f2224a4053ec8cad65b44b85eab0e31be3b64
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519389"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Collecter et utiliser des données de journaux à partir de vos ressources Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Présentation des journaux de diagnostics Azure Monitor

Les **journaux de diagnostic Azure Monitor** sont des journaux émis par un service Azure qui fournissent des informations riches et fréquentes sur le fonctionnement de ce service. Azure Monitor propose deux types de journaux de diagnostic :
* **Journaux de locataire** : ces journaux proviennent des services au niveau du locataire qui existent en dehors d’un abonnement Azure, tels que les journaux Azure Active Directory.
* **Journaux de ressources** : ces journaux proviennent des services Azure qui déploient des ressources au sein d’un abonnement Azure, tels que les groupes de sécurité réseau ou les comptes de stockage.

    ![Comparaison entre les journaux de diagnostic des ressources et les autres types de journaux d’activité](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Le contenu de ces journaux d’activité varie en fonction du service Azure et du type de ressource. Les compteurs de règles du groupe de sécurité réseau et les audits de coffres de clés sont deux exemples de types de journaux de diagnostic.

Ces journaux d’activité sont différents du [journal d’activité](activity-logs-overview.md). Le journal d’activité fournit un aperçu des opérations qui ont été effectuées sur les ressources de votre abonnement à l’aide de Resource Manager (par exemple, la création d’une machine virtuelle ou la suppression d’une application logique). Il s’intéresse aux opérations effectuées au niveau de l’abonnement. Les journaux de diagnostic des ressources, quant à eux, donnent un aperçu des opérations qui ont été effectuées au sein d’une ressource (par exemple, l’obtention d’un secret à partir d’un coffre de clés).

Ces journaux d’activité diffèrent également des journaux de diagnostic de système d’exploitation invité. Les journaux de diagnostic de système d’exploitation invité sont collectés par un agent exécuté sur une machine virtuelle ou un autre type de ressource pris en charge. Les journaux de diagnostic des ressources ne nécessitent aucun agent et capturent les données relatives à la ressource à partir de la plateforme Azure, alors que les journaux de diagnostic du système d’exploitation invité capturent les données provenant du système d’exploitation et des applications exécutées sur la machine virtuelle.

Toutes les services ne prennent pas en charge les journaux de diagnostic décrits ici. [Cet article contient une section répertoriant les services prenant en charge les journaux de diagnostic](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Ce que vous pouvez faire avec les journaux de diagnostic
Voici ce que vous pouvez faire avec les journaux de diagnostic :

![Positionnement logique des journaux de diagnostic](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Enregistrez-les dans un [**compte de stockage**](../../azure-monitor/platform/archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **paramètres de diagnostic des ressources**.
* [Diffusez-les en streaming sur **Event Hubs**](diagnostic-logs-stream-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (comme PowerBI) les ingère.
* Analysez-les avec [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), les données sont écrites immédiatement dans Azure Monitor, et il est inutile d’écrire les données dans le stockage avant.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hubs qui n’est pas dans le même abonnement que celui générant des journaux d’activité. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements.

> [!NOTE]
>  Il n’est pas possible actuellement d’archiver les journaux de flux du réseau sur un compte de stockage situé derrière un réseau virtuel sécurisé.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic

Les journaux de diagnostic des ressources sont configurés à l’aide des paramètres de diagnostic des ressources. Les journaux de diagnostic des locataires sont configurés à l’aide des paramètres de diagnostic des locataires. **Paramètres de diagnostic** pour un contrôle de service :

* Où les journaux de diagnostic et les métriques sont envoyés (compte de stockage, Event Hubs et/ou Azure Monitor).
* Les catégories de journal envoyées et les données de mesure également envoyées.
* La durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage
    - Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. Sinon, la valeur peut être n’importe quel nombre de jours compris entre 1 et 365.
    - Si des stratégies de rétention sont définies, mais que le stockage des journaux d’activité dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou Log Analytics sont sélectionnées), les stratégies de rétention n’ont aucun effet.
    - Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux d’activité de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’activité d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux d’activité de votre compte de stockage peut prendre jusqu’à 24 heures.

Ces paramètres peuvent être facilement configurés à partir des paramètres de diagnostics du portail Azure, à l’aide des commandes Azure PowerShell et de l’interface CLI, ou à l’aide de l’[API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Activer la collecte des journaux de diagnostic

La collecte des journaux de diagnostic peut être activée [dans le cadre de la création d’une ressource dans un modèle de Gestionnaire des ressources](./../../azure-monitor/platform/diagnostic-logs-stream-template.md) ou après la création d’une ressource via la page de cette ressource dans le portail. Vous pouvez également activer la collecte à tout moment via les commandes de l’interface de ligne de commande ou d’Azure PowerShell, ou via l’API REST Azure Monitor.

> [!TIP]
> Ces instructions peuvent ne pas s’appliquer directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les étapes spécifiques qui peuvent concerner certains types de ressources.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Activer la collecte des journaux de diagnostic dans le portail

Vous pouvez activer la collecte des journaux de diagnostic des ressources dans le portail Azure après la création d’une ressource, en allant vers une ressource spécifique ou en navigant vers Azure Monitor. Pour activer cette option via Azure Monitor :

1. Dans le [portail Azure](https://portal.azure.com), naviguez jusqu’à Azure Monitor, puis cliquez sur **Paramètres de diagnostic**

    ![Section Surveillance d’Azure Monitor](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. Vous pouvez également filtrer la liste par type ou groupe de ressources, puis cliquez sur la ressource pour laquelle vous souhaitez définir un paramètre de diagnostic.

3. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur « Activer les diagnostics ».

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   S’il existe des paramètres existants sur la ressource, vous verrez une liste de paramètres déjà configurés sur cette ressource. Cliquez sur « Ajouter le paramètre de diagnostic ».

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. Donnez un nom à votre définition, cochez les cases pour chaque destination vers laquelle vous souhaitez envoyer des données et configurez la ressource utilisée pour chaque destination. Si vous le souhaitez, vous pouvez définir un nombre de jours pour conserver ces journaux d’activité à l’aide des curseurs **Rétention (jours)** (uniquement applicable à la destination du compte de stockage). Si la valeur zéro est appliquée à la rétention, les journaux d’activité sont stockés pour une durée indéfinie.

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource et les journaux de diagnostic sont envoyés vers les destinations spécifiées dès la génération de nouvelles données d’événements.

Les paramètres de diagnostic de locataire peuvent uniquement être configurés dans le panneau de portail correspondant au service de locataire ; ces paramètres n’apparaissent pas dans le panneau de paramètres de diagnostic Azure Monitor. Par exemple, vous configurez les journaux d’audit Azure Active Directory en cliquant sur les **paramètres d’exportation de données** dans le panneau des journaux d’audit.

![Paramètres de diagnostic AAD](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Activer la collecte des journaux de diagnostic des ressources via PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour activer la collecte des journaux de diagnostic des ressources via Azure PowerShell, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L’ID de compte de stockage est l’ID de ressource du compte de stockage auquel vous souhaitez envoyer les journaux d’activité.

Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

L’ID de règle Service Bus est une chaîne au format suivant : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

Actuellement, vous ne pouvez pas configurer les paramètres de diagnostic de locataire à l’aide d’Azure PowerShell.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Activer la collecte des journaux de diagnostic des ressources par l’intermédiaire d’Azure CLI

Pour activer la collecte des journaux de diagnostic des ressources par l’intermédiaire d’Azure CLI, utilisez la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

Pour activer le stockage des journaux de diagnostic dans un compte de stockage :

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

L’argument `--resource-group` est obligatoire seulement si `--storage-account` n’est pas un ID d’objet.

Pour activer le streaming des journaux de diagnostic vers un hub d’événements :

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

L’ID de règle est une chaîne au format suivant : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics :

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

L’argument `--resource-group` est obligatoire seulement si `--workspace` n’est pas un ID d’objet.

À l’aide d’une commande, vous pouvez ajouter des catégories supplémentaires au journal de diagnostic par l’adjonction de dictionnaires au tableau JSON transmis en tant que paramètre `--logs`. Vous pouvez combiner les paramètres `--storage-account`, `--event-hub` et `--workspace` pour activer plusieurs options de sortie.

Actuellement, vous ne pouvez pas configurer les paramètres de diagnostic de locataire à l’aide de l’interface CLI.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Activer la collecte des journaux de diagnostic des ressources via l’API REST

Pour modifier les paramètres de diagnostic à l’aide de Azure Monitor REST API, consultez [ce document](https://docs.microsoft.com/rest/api/monitor/).

Actuellement, vous ne pouvez pas configurer les paramètres de diagnostic de locataire à l’aide de l’API REST Azure Monitor.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gérer les paramètres de diagnostic des ressources dans le portail

Assurez-vous que toutes vos ressources sont configurées avec des paramètres de diagnostic. Accédez à **Moniteur** dans le portail et ouvrez **Paramètres de diagnostic**.

![Panneau Journaux de diagnostic dans le portail](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

Vous devrez peut-être cliquer sur « Tous les services » pour trouver la section Monitor.

Dans cette section, vous pouvez afficher et filtrer toutes les ressources qui prennent en charge les paramètres de diagnostic pour voir si les diagnostics y sont activés. Vous pouvez également explorer pour voir si plusieurs paramètres sont définis sur une ressource et vérifier quel compte de stockage, espace de noms des concentrateurs d’événements et/ou espace de travail Log Analytics vers lesquels sont diffusées les données.

![Résultats des Journaux de diagnostic dans le portail](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

L’ajout d’un paramètre de diagnostic permet d’afficher le panneau Paramètres de diagnostic, où vous pouvez activer, désactiver ou modifier vos paramètres de diagnostic pour la ressource sélectionnée.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Services, catégories et schémas pris en charge pour les journaux de diagnostic

[Consultez cet article](../../azure-monitor/platform/diagnostic-logs-schema.md) pour obtenir la liste complète des services pris en charge et des catégories de journaux et des schémas utilisés par ces services.

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analyser les journaux du stockage Azure avec Azure Monitor](collect-azure-metrics-logs.md)
