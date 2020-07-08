---
title: Métriques d’Azure Service Bus dans Azure Monitor | Microsoft Docs
description: Cet article explique comment utiliser Azure Monitor pour surveiller les entités Service Bus (files d'attente, rubriques et abonnements).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 57b791e67157908447956a14fae99545843f3bc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340279"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Métriques Azure Service Bus dans Azure Monitor

Les métriques Service Bus vous donnent l’état des ressources dans votre abonnement Azure. Avec un ensemble complet de données de métriques, vous pouvez évaluer l’intégrité globale des ressources de Service Bus, non seulement au niveau de l’espace de noms, mais également au niveau de l’entité. Ces statistiques peuvent être importantes, car elles vous aident à surveiller l’état de Service Bus. Les métriques peuvent également vous aider à résoudre les problèmes constituant des causes premières sans avoir à contacter le support technique Azure.

Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre divers services Azure. Pour plus d’informations, consultez [Analyse dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) et [Récupérer des métriques Azure Monitor avec .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) sur GitHub.

> [!IMPORTANT]
> Quand il n’y a plus eu d’interaction avec une entité pendant 2 heures, les métriques commencent à afficher « 0 » en tant que valeur jusqu’à ce que l’entité ne soit plus inactive.

## <a name="access-metrics"></a>Accéder aux mesures

Azure Monitor propose plusieurs méthodes d’accès aux mesures. Vous pouvez accéder aux métriques via le [portail Azure](https://portal.azure.com), ou bien utiliser les API Azure Monitor (REST et .NET) et des solutions d’analyse comme les journaux Azure Monitor et Event Hubs. Pour plus d’informations, consultez [Mesures dans Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Les métriques sont activées par défaut, et vous pouvez accéder aux 30 derniers jours de données. Si vous souhaitez conserver des données sur une période plus longue, vous pouvez archiver les données de mesures dans un compte de stockage Azure. Cette valeur est configurée dans les [paramètres de diagnostic](../azure-monitor/platform/diagnostic-settings.md) dans Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Accéder aux métriques dans le portail

Vous pouvez surveiller les mesures au fil du temps dans le [portail Azure](https://portal.azure.com). L’exemple suivant montre comment afficher les demandes réussies et les demandes entrantes au niveau du compte :

![][1]

Vous pouvez également accéder aux métriques directement via l’espace de noms. Pour ce faire, sélectionnez votre espace de noms, puis cliquez sur **Métriques**. Pour afficher les métriques filtrées à l’étendue de l’entité, sélectionnez l’entité, puis cliquez sur **Métriques**.

![][2]

Pour les mesures prenant en charge des dimensions, vous devez filtrer avec la valeur de dimension souhaitée.

## <a name="billing"></a>Facturation

Les métriques et alertes Azure Monitor sont facturés par alerte. Ces frais sont disponibles sur le portail lorsque l’alerte est configurée et avant de l’enregistrer. 

Les solutions supplémentaires ingèrent les données de métriques sont facturées directement par ces solutions. Par exemple, vous êtes facturé par Stockage Azure si vous archivez des données de mesures pour un compte de stockage Azure. Vous êtes également facturé par Log Analytics si vous diffusez des données de métriques vers Log Analytics pour une analyse avancée.

Les métriques suivantes vous donnent une vue d’ensemble de l’intégrité de votre service. 

> [!NOTE]
> Nous déprécions plusieurs métriques lors de leur déplacement sous un autre nom. Vous devrez peut-être mettre à jour vos références. Les métriques marquées avec le mot-clé « dépréciée » ne seront pas gérées à l’avenir.

Toutes les valeurs de métriques sont envoyées à Azure Monitor toutes les minutes. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de métriques sont présentées. L’intervalle de temps pris en charge pour toutes les métriques de Service Bus est de 1 minute.

## <a name="request-metrics"></a>Métriques de demande

Compte le nombre de requêtes d’opérations de données et de gestion.

| Nom de métrique | Description |
| ------------------- | ----------------- |
| Demandes entrantes| Le nombre de requêtes effectuées auprès de Service Bus sur une période spécifiée. <br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Requêtes ayant réussi|Le nombre de requêtes réussies effectuées auprès de Service Bus sur une période spécifiée.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Erreurs de serveur|Le nombre de requêtes non traitées en raison d’une erreur sur Service Bus sur une période spécifiée.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Erreurs d’utilisateur (voir la sous-section suivante)|Le nombre de demandes non traitées en raison d’erreurs utilisateur sur une période spécifiée.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Requêtes limitées|Le nombre de demandes qui ont été limitées car l’utilisation a été dépassée.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|

### <a name="user-errors"></a>Erreurs d’utilisateur

Les deux types d’erreurs suivants sont classées dans la catégorie des erreurs d’utilisateur :

1. Erreurs côté client (erreurs 400 dans HTTP).
2. Erreurs qui se produisent pendant le traitement des messages, par exemple [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métriques de message

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Messages entrants|Le nombre d’événements ou de messages envoyés à Service Bus sur une période spécifiée.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Messages sortants|Le nombre d’événements ou de messages reçus à partir de Service Bus sur une période spécifiée.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
| Messages| Nombre de messages dans une file d’attente/rubrique. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Dimension : EntityName |
| ActiveMessages| Nombre de messages actifs dans une file d’attente/rubrique. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Dimension : EntityName |
| Messages de lettres mortes| Nombre de messages de lettres mortes dans une file d’attente/rubrique. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/>Dimension : EntityName |
| Messages planifiés| Nombre de messages planifiés dans une file d’attente/rubrique. <br/><br/> Unité : Count <br/> Type d’agrégation : Average  <br/> Dimension : EntityName |

> [!NOTE]
> Les valeurs des métriques suivantes sont des valeurs à un moment donné. Les messages entrants consommés immédiatement après ce moment peuvent ne pas apparaître dans ces métriques. 
> - Messages
> - Messages actifs 
> - Messages de lettres mortes 
> - Messages planifiés 

## <a name="connection-metrics"></a>Métriques de connexion

| Nom de métrique | Description |
| ------------------- | ----------------- |
|ActiveConnections|Le nombre de connexions actives sur un espace de noms ainsi que sur une entité.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Connexions ouvertes |Le nombre de connexions ouvertes.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|
|Connexions fermées |Le nombre de connexions fermées.<br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimension : EntityName|

## <a name="resource-usage-metrics"></a>Métriques d’utilisation des ressources

> [!NOTE] 
> Les métriques suivantes sont disponibles uniquement avec le niveau **Premium**. 
> 
> Les principales métriques à surveiller en cas d'interruption pour un espace de noms du niveau Premium sont les suivantes : **Utilisation du processeur par espace de noms** et **Taille de la mémoire par espace de noms**. [Configurer des alertes](../azure-monitor/platform/alerts-metric.md) pour ces métriques à l'aide d'Azure Monitor.
> 
> Vous pouvez également surveiller la métrique suivante : **Requêtes limitées**. Mais cela ne doit pas poser de problème tant que l'espace de noms reste dans les limites de sa mémoire, de son processeur et des connexions réparties. Pour plus d'informations, consultez [Limitation au niveau Premium d'Azure Service Bus](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier).

| Nom de métrique | Description |
| ------------------- | ----------------- |
|Utilisation du processeur par espace de noms|Le pourcentage d’utilisation du processeur de l’espace de noms.<br/><br/> Unité : Pourcentage <br/> Type d’agrégation : Maximale <br/> Dimension : EntityName|
|Utilisation de la taille mémoire par espace de noms|Le pourcentage d’utilisation de mémoire de l’espace de noms.<br/><br/> Unité : Pourcentage <br/> Type d’agrégation : Maximale <br/> Dimension : EntityName|

## <a name="metrics-dimensions"></a>Dimensions de mesures

Azure Service Bus prend en charge les dimensions suivantes pour les mesures dans Azure Monitor. L’ajout de dimensions à vos métriques est facultatif. Si vous n’ajoutez pas de dimensions, les métriques sont spécifiées au niveau de l’espace de noms. 

|Nom de la dimension|Description|
| ------------------- | ----------------- |
|EntityName| Service Bus prend en charge les entités de messagerie sous l’espace de noms.|

## <a name="set-up-alerts-on-metrics"></a>Configurer des alertes relatives à des mesures

1. Dans l’onglet **Métriques** de la page **Espace de noms Service Bus**, sélectionnez **Configurer les alertes**. 

    ![Page Métriques - menu Configurer les alertes](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Choisissez l’option **Sélectionner la cible**, puis effectuez les actions suivantes dans la page **Sélectionner une ressource** : 
    1. Sélectionnez **Espace de noms Service Bus** pour le champ **Filtrer par type de ressource**. 
    2. Sélectionnez votre abonnement pour le champ **Filtrer par abonnement**.
    3. Sélectionnez l’**Espace de noms Service Bus** dans la liste. 
    4. Sélectionnez **Terminé**. 
    
        ![Sélectionnez un espace de noms](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Sélectionnez **Ajouter des critères**, puis effectuez les actions suivantes dans la page **Configurer la logique du signal** :
    1. Sélectionnez **Métriques** comme **Type de signal**. 
    2. Sélectionnez un signal. Par exemple : **Erreurs de service**. 

        ![Sélectionnez Erreurs de serveur](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Sélectionnez **Supérieur à** sous **Condition**.
    2. Sélectionnez **Total** pour **Agrégation de temps**. 
    3. Entrez **5** pour **Seuil**. 
    4. Sélectionnez **Terminé**.    

        ![Spécifier la condition](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Dans la page **Créer une règle**, développez **Définir les détails de l’alerte**, puis effectuez les actions suivantes :
    1. Entrez un **nom** pour l’alerte. 
    2. Entrez une **description** pour l’alerte.
    3. Sélectionnez la **gravité** pour l’alerte. 

        ![Détails de l’alerte](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Dans la page **Créer une règle**, développez **Définir un groupe d’actions**, sélectionnez **Nouveau groupe d’actions**, puis effectuez les actions suivantes dans la page **Ajouter un groupe d’actions**. 
    1. Entrez le nom du groupe d’actions.
    2. Entrez le nom court du groupe d’actions. 
    3. Sélectionnez votre abonnement. 
    4. Sélectionnez un groupe de ressources. 
    5. Pour cette procédure pas à pas, entrez **Envoyer un message électronique** comme **NOM D’ACTION**.
    6. Sélectionnez **E-mail/SMS/Push/Voix** comme **TYPE D’ACTION**. 
    7. Sélectionnez **Modifier les détails**. 
    8. Dans la page **E-mail/SMS/Push/Voix**, effectuez les actions suivantes :
        1. Sélectionnez **E-mail**. 
        2. Saisissez l’**adresse de messagerie**. 
        3. Sélectionnez **OK**.

            ![Détails de l’alerte](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Dans la page **Ajouter un groupe d’actions**, sélectionnez **OK**. 
1. Dans la page **Créer une règle**, sélectionnez **Créer une règle d’alerte**. 

    ![Bouton Créer une règle d’alerte](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez la [vue d’ensemble d’Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


