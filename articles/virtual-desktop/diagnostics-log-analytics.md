---
title: Analytique des journaux d'activité Windows Virtual Desktop - Azure
description: Comment utiliser l’analytique des journaux d’activité avec la fonctionnalité de diagnostic de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76a5e12eee7a325a73b3c17dba6c775b6984b89a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195920"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Utiliser Log Analytics pour la fonctionnalité de diagnostic

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop utilise [Azure Monitor](../azure-monitor/overview.md) pour la supervision et les alertes, comme de nombreux autres services Azure. Les administrateurs peuvent ainsi identifier les problèmes par le biais d’une interface unique. Le service crée des journaux d’activité pour les actions des utilisateurs et des administrateurs. Chaque journal d’activité se divise en les catégories suivantes :  

- Activités de gestion :
    - Effectue le suivi de la réussite des tentatives de modification des objets Windows Virtual Desktop à l’aide d’API ou de PowerShell. Par exemple, une personne peut-elle créer un pool d’hôtes avec PowerShell ?
- Flux : 
    - Les utilisateurs parviennent-ils à s’abonner aux espaces de travail ? 
    - Les utilisateurs voient-ils toutes les ressources publiées dans le client Bureau à distance ?
- Connexions : 
    - Moment auquel les utilisateurs lancent et finalisent les connexions au service. 
- Inscription de l’hôte : 
    - L’hôte de session a-t-il été correctement inscrit auprès du service lors de la connexion ?
- Erreurs : 
    - Les utilisateurs rencontrent-ils des problèmes avec des activités spécifiques ? Cette fonctionnalité peut générer une table qui effectue le suivi des données d’activité tant que les informations sont jointes aux activités.
- Points de contrôle :  
    - Étapes spécifiques dans la durée de vie d’une activité qui ont été atteintes. Par exemple, au cours d’une session, un utilisateur a fait l’objet d’un équilibrage de charge sur un hôte particulier, puis a été connecté pendant une connexion, et ainsi de suite.

Les connexions qui n’atteignent pas Windows Virtual Desktop ne figureront pas dans les résultats de diagnostic, car le service de rôle de diagnostics fait partie de Windows Virtual Desktop. Des problèmes de connexion à Windows Virtual Desktop peuvent survenir lorsque l’utilisateur rencontre des problèmes de connectivité au réseau.

Azure Monitor vous permet d’analyser les données Windows Virtual Desktop et de consulter les compteurs de performances des machines virtuelles, le tout au sein du même outil. Cet article met l’accent sur l’activation des diagnostics pour votre environnement Windows Virtual Desktop. 

>[!NOTE] 
>Pour savoir comment superviser vos machines virtuelles dans Azure, consultez [Supervision de machines virtuelles Azure avec Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md). Veillez également à [passer en revue les seuils des compteurs de performances](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) pour mieux comprendre votre expérience utilisateur sur l’hôte de session.

## <a name="before-you-get-started"></a>Avant de commencer

Avant de pouvoir utiliser Log Analytics, vous devez créer un espace de travail. Pour ce faire, suivez les instructions de l’un des deux articles suivants :

- Si vous préférez utiliser le portail Azure, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/learn/quick-create-workspace.md).
- Si vous préférez PowerShell, consultez [Créer un espace de travail Log Analytics avec PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

Après avoir créé votre espace de travail, suivez les instructions de la section [Connecter des ordinateurs Windows à Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) pour obtenir les informations suivantes :

- L’ID de l’espace de travail
- La clé primaire de votre espace de travail

Ces informations vous seront nécessaires plus loin dans le processus de configuration.

Veillez à passer en revue la gestion des autorisations pour Azure Monitor afin d’activer l’accès aux données pour les personnes qui supervisent et gèrent votre environnement Windows Virtual Desktop. Pour plus d’informations, consultez [Prise en main des rôles, des autorisations et de la sécurité dans Azure Monitor](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Envoyer (push) les données de diagnostic à votre espace de travail

Vous pouvez envoyer (push) les données de diagnostic de vos objets Windows Virtual Desktop à Log Analytics pour votre espace de travail. Vous pouvez configurer cette fonctionnalité immédiatement au moment où vous créez vos objets.

Pour configurer Log Analytics pour un nouvel objet :

1. Connectez-vous au portail Azure et accédez à **Windows Virtual Desktop**. 

2. Accédez à l’objet (par exemple, un pool d’hôtes, un groupe d’applications ou un espace de travail) pour lequel vous souhaitez capturer des journaux et des événements. 

3. Sélectionnez **Paramètres de diagnostic** dans le menu sur le côté gauche de l’écran. 

4. Sélectionnez **Ajouter un paramètre de diagnostic** dans le menu sur le côté gauche de l’écran. 
   
    Les options affichées dans la page Paramètres de diagnostic varient selon le type d’objet que vous modifiez.

    Par exemple, quand vous activez les diagnostics pour un groupe d’applications, vous voyez les options permettant de configurer les points de contrôle, les erreurs et la gestion. Dans le cas des espaces de travail, ces catégories configurent un flux pour suivre le moment auquel les utilisateurs s’abonnent à la liste des applications. Pour en savoir plus sur les paramètres de diagnostic, consultez [Créer un paramètre de diagnostic pour collecter les journaux et les métriques des ressources dans Azure](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >N’oubliez pas d’activer les diagnostics pour chaque objet Azure Resource Manager que vous souhaitez superviser. Les données sont disponibles pour les activités une fois les diagnostics activés. La première configuration peut prendre quelques heures.  

5. Entrez un nom pour la configuration de vos paramètres, puis sélectionnez **Envoyer à Log Analytics**. Le nom que vous utilisez ne doit pas contenir d’espaces et doit être conforme aux [conventions de nommage Azure](../azure-resource-manager/management/resource-name-rules.md). Dans le cadre des journaux, vous pouvez sélectionner toutes les options que vous souhaitez ajouter à votre analytique des journaux, telles que les points de contrôle, les erreurs, la gestion, etc.

6. Sélectionnez **Enregistrer**.

>[!NOTE]
>Log Analytics vous donne la possibilité d’effectuer un streaming des données vers [Event Hubs](../event-hubs/event-hubs-about.md) ou de les archiver dans un compte de stockage. Pour en savoir plus sur cette fonctionnalité, consultez [Envoyer en streaming des données de supervision Azure vers un hub d’événements](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) et [Archiver des journaux de ressource Azure dans un compte de stockage](../azure-monitor/platform/resource-logs-collect-storage.md). 

## <a name="how-to-access-log-analytics"></a>Comment accéder à Log Analytics

Vous pouvez accéder aux espaces de travail Log Analytics sur le portail Azure ou sur Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Accéder à Log Analytics sur un espace de travail Log Analytics

1. Connectez-vous au portail Azure.

2. Recherchez **Espace de travail Log Analytics**. 

3. Sous Services, sélectionnez **Espaces de travail Log Analytics**. 
   
4. Dans la liste, sélectionnez l’espace de travail que vous avez configuré pour votre objet Windows Virtual Desktop.

5. Une fois dans votre espace de travail, sélectionnez **Journaux**. Vous pouvez filtrer votre liste de menu avec la fonction **Rechercher**. 

### <a name="access-log-analytics-on-azure-monitor"></a>Accéder à Log Analytics sur Azure Monitor

1. Se connecter au portail Azure

2. Recherchez et sélectionnez **Surveiller**. 

3. Sélectionnez **Journaux d’activité**.

4. Suivez les instructions de la page de journalisation pour définir l’étendue de votre requête.  

5. Vous êtes prêt à interroger les diagnostics. Toutes les tables de diagnostics ont un préfixe « WVD ».

## <a name="cadence-for-sending-diagnostic-events"></a>Cadence d’envoi des événements de diagnostic

Les événements de diagnostic sont envoyés à Log Analytics lorsqu’ils sont terminés.

Les rapports Log Analytics ne portent que sur les états intermédiaires suivants des activités de connexion :

- Démarré : lorsqu’un utilisateur sélectionne et se connecte à une application ou à un ordinateur de bureau dans le client Bureau à distance.
- Connecté : lorsque l’utilisateur se connecte avec succès à la machine virtuelle sur laquelle l’application ou le bureau est hébergé.
- Terminé : lorsque l’utilisateur ou le serveur déconnecte la session dans laquelle l’activité a eu lieu.

## <a name="example-queries"></a>Exemples de requêtes

Les exemples de requêtes suivants montrent comment la fonctionnalité de diagnostic génère un rapport pour les activités les plus fréquentes dans votre système.

Pour obtenir la liste des connexions établies par vos utilisateurs, exécutez cette applet de commande :

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Pour afficher l’activité de flux de vos utilisateurs :

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Pour rechercher toutes les connexions d’un seul utilisateur : 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

Pour déterminer le nombre de fois qu’un utilisateur s’est connecté par jour :

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Pour rechercher la durée de session par utilisateur :

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Pour rechercher les erreurs liées à un utilisateur spécifique :

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Pour déterminer si une erreur spécifique s’est produite :

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Pour rechercher l’occurrence d’une erreur pour tous les utilisateurs :

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>La table la plus importante pour la résolution des problèmes est WVDErrors. Utilisez cette requête pour déterminer les problèmes qui se produisent pour les activités utilisateur, telles que les connexions ou les flux, quand un utilisateur s’abonne à la liste des applications ou des postes de travail. La table indique les erreurs de gestion ainsi que les problèmes d’inscription de l’hôte.
>
>Pendant la phase de préversion publique, si vous avez besoin d’aide pour résoudre un problème, veillez à fournir l’ID de corrélation (CorrelationID) de l’erreur dans votre demande d’aide. En outre, assurez-vous que la valeur d’erreur de service indique toujours ServiceError = "false". Une valeur « false » signifie que le problème peut être résolu par une tâche d’administration de votre côté. Si ServiceError a pour valeur « true », vous devez faire remonter le problème à Microsoft.

## <a name="next-steps"></a>Étapes suivantes 

Pour examiner les scénarios d’erreur courants que la fonctionnalité de diagnostic peut identifier pour vous, consulter [Identifier et diagnostiquer les problèmes](diagnostics-role-service.md#common-error-scenarios).
