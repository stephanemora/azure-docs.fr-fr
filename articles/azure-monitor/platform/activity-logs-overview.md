---
title: Présentation du journal d’activité Azure
description: Découvrez le journal d’activités Azure et comment vous pouvez l’utiliser pour comprendre les événements qui se produisent dans votre abonnement Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: be2f5fdebc5ae4f779fe6e3da74ad7ea583226ef
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995657"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Surveiller l’activité d’abonnement avec le journal d’activité Azure

Le **Journal d’activité Azure** est un journal d’abonnement qui fournit un aperçu de tous les événements relatifs aux abonnements qui se sont produits dans Azure. Cela inclut une plage de données, à partir de données opérationnelles d’Azure Resource Manager pour les mises à jour des événements de l’état d’intégrité du service. Le journal d’activité était précédemment appelé « journaux d’activité d’audit » ou « journaux d’activité des opérations », car la catégorie administrative indique les événements de plan de contrôle pour vos abonnements. Avec le journal d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur des ressources dans votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas d’opérations de lecture (GET) ni d’opérations pour les ressources qui utilisent le modèle Classic/« RDFE ».

![Journaux d’activité et autres types de journaux d’activité](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)

Figure 1 : Journaux d’activité et autres types de journaux d’activité

Le journal d’activité est différent des [journaux de diagnostic](diagnostic-logs-overview.md). Les journaux d’activité fournissent des données sur les opérations effectuées sur une ressource à partir de l’extérieur (le « plan de contrôle »). Les journaux de diagnostic sont émis par une ressource et fournissent des informations sur le fonctionnement de cette ressource (le « plan de données »).

> [!WARNING]
> Le journal d’activité Azure est principalement utilisé pour les activités qui se produisent dans Azure Resource Manager. Il ne suit pas les ressources à l’aide du modèle Classic/RDFE. Certains types de ressources Classic ont un fournisseur de ressources proxy dans Azure Resource Manager (par exemple, Microsoft.ClassicCompute). Si vous interagissez avec un type de ressource Classic par le biais d’Azure Resource Manager à l’aide de ces fournisseurs de ressources proxy, les opérations s’affichent dans le journal d’activité. Si vous interagissez avec un type de ressource Classic en dehors de proxys Azure Resource Manager, vos actions sont uniquement enregistrées dans le journal des opérations. Le journal des opérations est accessible dans une section distincte du portail.
>
>

Vous pouvez extraire des événements de votre journal d’activité à l’aide du portail Azure, de l’interface de ligne de commande, des applets de commande PowerShell et de l’API REST Azure Monitor.

> [!NOTE]
> Les [alertes les plus récentes](../../azure-monitor/platform/alerts-overview.md) offrent actuellement une expérience améliorée de création et de gestion des règles d’alerte du journal d’activité.  [Plus d’informations](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="categories-in-the-activity-log"></a>Catégories dans le journal d’activité
Le journal d’activité contient plusieurs catégories de données. Pour plus d’informations sur les schémas de ces catégories, [consultez cet article](../../azure-monitor/platform/activity-log-schema.md). Il s’agit des actions suivantes :
* **Administrative** : cette catégorie contient l’enregistrement de toutes les opérations de création, mise à jour, suppression et action effectuées par le biais du gestionnaire de ressources. Les exemples de types d’événements que vous pouvez voir dans cette catégorie incluent « créer une machine virtuelle » et « supprimer un groupe de sécurité réseau ». Toute mesure prise par un utilisateur ou une application utilisant le gestionnaire de ressources est modélisée comme une opération sur un type de ressources en particulier. Si le type d’opération est Écrire, Supprimer ou Action, les enregistrements de début et de réussite ou d’échec de cette opération sont enregistrés dans la catégorie Administrative. La catégorie Administrative inclut également toute modification apportée à un contrôle d’accès basé sur un rôle dans un abonnement.
* **État d’intégrité du service** : cette catégorie contient l’enregistrement de tout incident de l’état d’intégrité du service qui se sont produits dans Azure. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « SQL Azure dans la région USA Est rencontre des temps d’arrêt. » Les événements d’intégrité du service se présentent sous cinq types : action requise, récupération assistée, incident, maintenance, informations ou sécurité et n’apparaissent que si une ressource de votre abonnement est affectée par l’événement.
* **Resource Health** : cette catégorie contient l’enregistrement de tout événement d’intégrité de la ressource survenu dans vos ressources Azure. Par exemple, cette catégorie peut comporter le type d’événement suivant : « L’état d’intégrité de la machine virtuelle est passé à Indisponible ». Les événements d’intégrité de ressource peuvent représenter l’un des quatre états d’intégrité : Disponible, Indisponible, Détérioré et Inconnu. En outre, les événements d’intégrité de ressource peuvent être initiés par la plateforme ou initiés par l’utilisateur.
* **Alerte** : cette catégorie contient l’enregistrement de toutes les activations des alertes Azure. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « % du processeur sur myVM a été supérieur à 80 pour les 5 dernières minutes. » Une variété de systèmes Azure possèdent un concept d’alertes : vous pouvez définir une règle quelconque et recevoir une notification lorsque les conditions correspondent à cette règle. Chaque fois qu’un type d’alerte Azure pris en charge « s’active » ou si les conditions sont remplies pour générer une notification, un enregistrement de l’activation est également envoyé à cette catégorie du journal d’activité.
* **Mise à l’échelle automatique** : cette catégorie contient l’enregistrement de tous les événements liés au fonctionnement du moteur de mise à l’échelle selon les paramètres d’échelle automatique définis dans votre abonnement. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « Échec de l’action de monter en puissance de la mise à l’échelle automatique. » À l’aide de la mise à l’échelle automatique, vous pouvez automatiquement augmenter ou diminuer la taille des instances dans un type de ressource pris en charge basé sur l’heure du jour et/ou les données de charge (métriques) à l’aide d’un paramètre de mise à l’échelle automatique. Lorsque les conditions sont remplies pour monter ou descendre en puissance, les événements de démarrage réussis ou échoués sont enregistrés dans cette catégorie.
* **Recommandation** : cette catégorie contient les événements de recommandation d’Azure Advisor.
* **Sécurité** : Cette catégorie contient l’enregistrement de toutes les alertes générées par Azure Security Center. Voici un exemple du type d’événement que vous pouvez voir dans cette catégorie : « Suspicious double extension file executed. » (Fichier à extension double suspect exécuté.).
* **Policy** : cette catégorie contient les enregistrements de toutes les opérations d’action à effet effectuées par Azure Policy. Cette catégorie pourrait par exemple contenir les types d’événements Audit et Deny (Refus). Chaque action effectuée par Policy est modélisée en tant qu’opération sur une ressource.

## <a name="event-schema-per-category"></a>Schéma d’événements par catégorie

[Consultez cet article pour comprendre le schéma d’événements de journal d’activité par catégorie.](../../azure-monitor/platform/activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Ce que vous pouvez faire avec le journal d’activité

Voici ce que vous pouvez faire avec le journal d’activité :

![Journal d’activité Azure](./media/activity-logs-overview/Activity_Log_Overview_v3.png)


* Interrogez-le et affichez-le dans le **portail Azure**.
* [Créer une alerte basée sur un événement du journal d’activité](../../azure-monitor/platform/activity-log-alerts.md)
* [Stream pour un **Event Hub** ](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) pour l’ingestion par un service tiers ou d’une solution d’analytique personnalisées, telles que Power BI.
* Analysez-le dans Powerbi à l’aide de la [ **pack de contenu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Enregistrez-le dans un **compte de stockage** pour l’archivage ou l’inspection manuelle](../../azure-monitor/platform/archive-activity-log.md). Vous pouvez spécifier la durée de rétention (en jours) à l’aide du **Profil de journal**.
* Interrogez-le via l’applet de commande PowerShell, l’interface de ligne de commande ou l’API REST.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Interroger le journal d’activité dans le portail Azure

> [!NOTE] 
> Le journal d’activité stocke les journaux dans le serveur principal pendant 90 jours. Si vous souhaitez conserver les données allant au-delà de cela, configurez un **profil de journal** comme décrit ci-dessous. 

À l’intérieur du portail Azure, vous pouvez afficher votre journal d’activité à plusieurs endroits :
* Le **Journal d’activité** auquel vous avez accès en recherchant le Journal d’activité sous **Tous les services** dans le volet de navigation de gauche.
* **Monitor** s’affiche par défaut dans le volet de navigation de gauche. Le journal d’activité est une section d’Azure Monitor.
* La plupart des **ressources**, par exemple, le panneau de configuration d’une machine virtuelle. Le journal d’activité est une section présente dans la plupart des panneaux de ressources. Quand vous cliquez dessus, les événements associés à la ressource sont automatiquement affichés.

Dans le portail Azure, vous pouvez filtrer le journal d’activités à l’aide des champs suivants :
* Intervalle de temps : heure de début et de fin des événements.
* Catégorie : catégorie d’événement comme décrit ci-dessus.
* Abonnement : un ou plusieurs noms d’abonnements Azure.
* Groupe de ressources : un ou plusieurs groupes de ressources au sein de ces abonnements.
* Ressource (nom) : nom d’une ressource.
* Type de ressource : type de ressource, par exemple, Microsoft.Compute/virtualmachines.
* Nom de l’opération : nom d’une opération Azure Resource Manager, par exemple, Microsoft.SQL/servers/Write.
* Gravité : niveau de gravité de l’événement (information, avertissement, erreur, critique).
* Événement lancé par : « appelant » ou utilisateur ayant effectué l’opération.
* Recherche libre : zone de recherche textuelle libre qui permet de rechercher une chaîne dans les champs de tous les événements.

Après avoir défini un ensemble de filtres, vous pouvez épingler une requête à votre tableau de bord Azure afin de toujours avoir un œil sur certains événements.

Pour encore plus de puissance, vous pouvez cliquer sur l’icône **Journaux d’activité** pour afficher les données du journal d’activité dans la [solution Collecter et analyser les journaux d’activités](../../azure-monitor/platform/collect-activity-logs.md). Le panneau Journal d’activité offre des fonctionnalités de base pour filtrer et parcourir les journaux d’activité, tandis que la fonctionnalité des journaux d’activité Azure Monitor vous permet d’interroger et de visualiser vos données, ainsi que d’ajouter un tableau croisé dynamique.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exporter le journal d’activité avec un profil de journal

Un **profil de journal** contrôle comment votre journal d’activité est exporté. À l’aide d’un profil de journal, vous pouvez configurer :

* L’emplacement où le journal d’activité doit être envoyé (compte de stockage ou Event Hubs).
* Les catégories d’événements (Write, Delete, Action) qui doivent être envoyées. *La signification de « catégorie » est différente dans les événements de profil de journal et de journal d’activité. Dans le profil de journal, « catégorie » désigne le type d’opération (Write, Delete, Action). Dans un événement de journal d’activité, la propriété « catégorie » représente la source ou le type d’événement (par exemple, Administration, ServiceHealth, Alert, etc.).*
* Les régions (emplacements) qui doivent être exportées. Veillez à inclure la catégorie « global », car de nombreux événements du journal d’activité sont des événements globaux.
* Durée pendant laquelle le journal d’activité doit être conservé dans un compte de stockage.
    - Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. Sinon, la valeur peut être n’importe quel nombre de jours compris entre 1 et 365.
    - Si des stratégies de rétention sont définies, mais que le stockage des journaux d’activité dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou Log Analytics sont sélectionnées), les stratégies de rétention n’ont aucun effet.
    - Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux d’activité de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’activité d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux d’activité de votre compte de stockage peut prendre jusqu’à 24 heures.

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hub qui n’est pas dans le même abonnement que celui générant des journaux d’activité. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements.

Ces paramètres peuvent être configurés via l’option « Exporter » dans le panneau Journal d’activité dans le portail. Ils peuvent également être configurés par programme [à l’aide de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), d’applets de commande PowerShell ou de l’interface de ligne de commande. Un abonnement ne peut avoir qu’un seul profil de journal.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configuration des profils de journal à l’aide du portail Azure

Vous pouvez diffuser en continu le journal d’activité vers un Event Hub ou le stocker dans un compte de stockage à l’aide de l’option « Export vers Event Hub » dans le portail Azure.

1. Accédez au **Journal d’activité** à l’aide du menu sur le côté gauche du portail.

    ![Accéder au journal d’activité dans le portail](./media/activity-logs-overview/activity-logs-portal-navigate-v2.png)
2. Cliquez sur le bouton **Exporter vers Event Hub** en haut du panneau.

    ![Bouton Exporter dans le portail](./media/activity-logs-overview/activity-logs-portal-export-v2.png)
3. Dans le panneau qui s’affiche, vous pouvez sélectionner :
   * les régions pour lesquelles vous souhaitez exporter des événements
   * le compte de stockage pour lequel vous souhaitez enregistrer les événements
   * le nombre de jours pendant lesquels vous souhaitez conserver ces événements dans le stockage. Un paramètre de 0 jour conserve les journaux d’activité indéfiniment.
   * l’espace de noms Service Bus dans lequel vous souhaitez qu’un hub d’événements soit créé pour diffuser ces événements.

     ![Panneau Exporter le journal d’activité](./media/activity-logs-overview/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliqués à votre abonnement.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configuration des profils de journal à l’aide des applets de commande Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

#### <a name="get-existing-log-profile"></a>Obtention du profil de journal existant

```powershell
Get-AzLogProfile
```

#### <a name="add-a-log-profile"></a>Ajout d’un profil de journal

```powershell
Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| Name |Oui |Nom de votre profil de journal. |
| StorageAccountId |Non  |ID de ressource du compte de stockage dans lequel le journal d’activité doit être enregistré. |
| serviceBusRuleId |Non  |ID de règle Service Bus pour l’espace de noms Service Bus dans lequel vous souhaitez que des concentrateurs d’événements soient créés. Est une chaîne au format suivant : `{service bus resource ID}/authorizationrules/{key name}`. |
| Location |Oui |Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. |
| RetentionInDays |Oui |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux d’activité seront stockés pour une durée indéfinie (pour toujours). |
| Category |Non  |Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |

#### <a name="remove-a-log-profile"></a>Supprimer un profil de journal

```powershell
Remove-AzLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Configurer les profils de journal à l’aide d’Azure CLI

#### <a name="get-existing-log-profile"></a>Obtention du profil de journal existant

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

La propriété `name` doit être le nom de votre profil de journal.

#### <a name="add-a-log-profile"></a>Ajout d’un profil de journal

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Pour obtenir la documentation complète sur la création d’un profil de moniteur avec l’interface CLI, consultez la [Référence des commandes CLI](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Supprimer un profil de journal

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le journal d’activité (autrefois appelé journal d’audit)](../../azure-resource-manager/resource-group-audit.md)
* [Stream the Azure Activity Log to Event Hubs (Diffuser en continu le journal d’activités Azure vers Event Hubs)](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
