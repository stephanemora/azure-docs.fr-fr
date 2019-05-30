---
title: Les canaux de données de télémétrie dans Azure Application Insights | Microsoft Docs
description: Comment personnaliser les chaînes de données de télémétrie dans Azure Application Insights SDK pour.NET/.NET Core.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255804"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel dans Application Insights

TelemetryChannel fait partie intégrante de [Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md). Il gère la mise en mémoire tampon et la transmission de données de télémétrie au service Application Insights. Les versions de .NET et .NET Core de kits de développement logiciel ont deux TelemetryChannels intégrés - `InMemoryChannel` et `ServerTelemetryChannel`. Cet article décrit chaque canal en détail, notamment comment les utilisateurs peuvent personnaliser le comportement de canal.

## <a name="what-is-a-telemetrychannel"></a>Qu’est un TelemetryChannel ?

`TelemetryChannel` est responsable de la mise en mémoire tampon et en envoyant des éléments de télémétrie au service Application Insights, où il est stocké pour l’interrogation et l’analyse. Il est n’importe quelle classe qui implémente l’interface [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

Le `Send(ITelemetry item)` TelemetryChannel méthode est appelée après tous les `TelemetryInitializer`s et `TelemetryProcessor`s sont appelées. Cela signifie que tous les éléments supprimés par `TelemetryProcessor` n’atteint pas le canal. `Send()` ne pas envoient généralement les éléments instantanément au serveur principal. Ils sont généralement mis en mémoire tampon en mémoire et envoyées par lots, pour la transmission efficace.

[LiveMetrics](live-stream.md) a également un canal personnalisé, qui alimente la vidéo en flux continu de données de télémétrie. Ce canal est indépendant du canal de télémétrie standard, et ce document ne s’applique pas au canal utilisé par `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>TelemetryChannels intégrés

Kit de développement logiciel application Insights.NET/.NET Core est livré avec deux canaux intégrés :

* **InMemoryChannel** 
 `InMemoryChannel` est un canal léger, qui met en mémoire tampon éléments en mémoire jusqu'à ce qu’il est envoyé. Les éléments sont mis en mémoire tampon et vidés toutes les 30 secondes, ou chaque fois que les 500 éléments ont mis en mémoire tampon. Ce canal offre des garanties de fiabilité minimale comme il ne nouvelles tentatives d’envoi de télémétrie en cas de défaillances. Ce canal ne conserve les éléments sur le disque, par conséquent, tous les éléments non envoyés sont perdues définitivement à l’arrêt de l’application (correctement ou non). Il existe un `Flush()` méthode implémentée par ce canal, ce qui peut être utilisé pour forcer-vidage synchrone des éléments de données de télémétrie en mémoire. Cela convient bien pour les applications courte où un vidage synchrone est idéal.

    Ce canal est partie intégrante de la `Microsoft.ApplicationInsights` nuget package lui-même et est le canal par défaut le SDK utilise lorsque rien d’autre n’est configuré.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` est un canal plus avancé, ce qui a des stratégies de nouvelle tentative et la possibilité de stocker des données sur le disque local. Ce canal retente l’envoi de télémétrie, si des erreurs temporaires se produisent. Ce canal utilise également le stockage de disque local pour conserver les éléments sur le disque pendant les pannes réseau ou volumes de données de télémétrie haute. En raison de ces mécanismes de nouvelle tentative et le stockage sur disque local, ce canal est considéré comme plus fiable et il est recommandé pour tous les scénarios de production. Ce canal est la valeur par défaut pour [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) et [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) applications, qui sont configurées conformément à la documentation officielle liée. Ce canal est optimisé pour les scénarios de serveur de processus longs. Le [ `Flush()` ](#which-channel-should-i-use) méthode implémentée par ce canal n’est pas synchrone.

    Ce canal est fourni en tant que le package NuGet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`et est mis automatiquement lors de l’utilisation des packages NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Configuration TelemetryChannel

Canal de télémétrie peut être configuré en définissant souhaité `TelemetryChannel` sur actif `TelemetryConfiguration`. Pour les applications Asp.Net, configuration implique la `TelemetryChannel` sur `TelemetryConfiguration.Active`, ou la modification `ApplicationInsights.config`. Pour les applications ASP.NET Core, configuration implique l’ajout du canal souhaité pour le conteneur d’Injection de dépendance.

Voici un exemple où utilisateur consiste à configurer le `StorageFolder` pour le canal. `StorageFolder` est simplement un des paramètres configurables. La liste complète des paramètres de configuration est décrite [dans la section paramètres](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuration à l’aide de ApplicationInsights.Config pour les Applications ASP.NET

La section suivante à partir de [ApplicationInsights.config](configuration-with-applicationinsights-config.md) montre ServerTelemetryChannel configuré avec `StorageFolder` défini à un emplacement personnalisé.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Configuration dans le code pour les Applications ASP.NET

Le code suivant configure ServerTelemetryChannel avec `StorageFolder` défini à un emplacement personnalisé. Ce code doit être ajouté au début de l’application, généralement dans la méthode Application_Start() dans `Global.aspx.cs`

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuration dans le code pour les Applications ASP.NET Core

Modifier `ConfigureServices` méthode de `Startup.cs` classe comme indiqué ci-dessous.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> Il est important de noter que le canal à l’aide de la configuration `TelemetryConfiguration.Active` n’est pas recommandé pour les applications ASP.NET Core.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Configuration TelemetryChannel dans le code pour les Applications de Console.NET/.NET Core

Pour les applications de Console, le code est le même pour .NET et .NET Core et est indiqué ci-dessous.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Détails opérationnels de ServerTelemetryChannel

Le `ServerTelemetryChannel` met en mémoire tampon des éléments qui arrivent dans une mémoire tampon en mémoire. Il est sérialisé, compressée et stockée dans `Transmission` instance une fois que chaque 30 secondes ou lorsque 500 éléments sont mis en mémoire tampon. Un seul `Transmission` instance contient des éléments jusqu'à 500 et représente un lot de données de télémétrie est envoyé via un appel https unique au service Application Insights. Par défaut, il peut y avoir un maximum de 10 `Transmission`s envoyées en parallèle. Si les données de télémétrie en provenance des tarifs plus rapide ou si le serveur principal de réseau/Application Insights est lente, puis `Transmission`s sont stockés en mémoire. La capacité par défaut de cette mémoire tampon de Transmission en mémoire est de 5 Mo. Une fois que la capacité de mémoire dépasse, `Transmission`sont stockés sur le disque local pour jusqu'à 50 Mo. `Transmission`s sont stockés sur le disque local lorsqu’il existe également des problèmes réseau. Uniquement les éléments stockés dans le disque local après incident d’application, qui sont envoyés chaque fois que l’application est démarrée à nouveau.

## <a name="configurable-settings-in-channel"></a>Paramètres configurables dans le canal

La liste complète des paramètres configurables pour chaque canal se trouvent ici :

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Plus couramment utilisés pour les paramètres `ServerTelemetryChannel` sont répertoriées ci-dessous :

1. `MaxTransmissionBufferCapacity` -Quantité maximale de mémoire, en octets, utilisée par le canal pour les transmissions de mémoire tampon en mémoire. Une fois que cette capacité est atteinte, les nouveaux éléments seront stockées directement sur le disque local. La valeur par défaut est de 5 Mo. Définition d’une valeur plus élevée prospects à moindre utilisation du disque, mais il est important de noter que les éléments dans la mémoire seront perdues si l’application subit un incident.

2. `MaxTransmissionSenderCapacity` -Quantité maximale de `Transmission`s qui seront envoyées à Application Insights en même temps. La valeur par défaut est 10, mais il peut être configuré sur un nombre plus élevé. Cela est recommandé lorsqu’un volume considérable de données de télémétrie est généré, en général, lors de l’exécution de test de charge et/ou lorsque l’échantillonnage est désactivé.

3. `StorageFolder` -Le dossier utilisé par le canal pour stocker les éléments sur le disque en fonction des besoins. Dans Windows, % LocalAppData% ou %TEMP% est utilisé, si rien n’est configuré de manière explicite. Dans les environnements Non-Windows, utilisateur **doit** configurer un emplacement valide, sans lesquels télémétrie ne seront pas stockée sur le disque local.

## <a name="which-channel-should-i-use"></a>Quel canal dois-je utiliser ?

`ServerTelemetryChannel` est recommandé pour la plupart des scénarios de production des applications en cours d’exécution longue. Le `Flush()` implémentation de la méthode de `ServerTelemetryChannel` n’est pas synchrone, et `Flush()` ne garantit pas l’envoi de tous les éléments en attente à partir de la mémoire/disque. Si cette chaîne est utilisée dans les scénarios où l’application est sur le point d’arrêt, il est recommandé d’effectuer quelque temps après l’appel `Flush()` sur ce canal. La quantité exacte de retard nécessaire n’est pas prévisible, car elle dépend de facteurs tels que le nombre d’éléments ou `Transmissions` dans la mémoire, combien sont dans le disque, combien est transmis à soutenu, et si le canal se trouve au milieu de scénarios de temporisation exponentielle. S’il est nécessaire pour effectuer le vidage synchrone, puis `InMemoryChannel` est recommandé.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*ApplicationInsights canal offre de remise de données de télémétrie garantie ou quels sont les scénarios où les données de télémétrie peuvent être perdues ?*

* Réponse brève est qu'aucun des canaux intégrés offrent la garantie de type de transaction sur la remise de données de télémétrie vers le serveur principal. Bien que `ServerTelemetryChannel` sont plus avancée par rapport à `InMemoryChannel` pour la remise de données de télémétrie fiables, il est également une meilleure tentative possible pour envoyer la télémétrie et de télémétrie peut toujours se perdre dans plusieurs scénarios. Parmi les scénarios courants où les données de télémétrie sont perdue :

1. Éléments en mémoire sont perdues chaque fois que l’application subit un incident.
1. Données de télémétrie sont stockée sur le disque local pendant les pannes de réseau ou des problèmes avec le serveur principal d’Application Insights. Toutefois, les éléments antérieurs à 24 heures sont ignorées. Par conséquent, données de télémétrie sont perdues pendant une période prolongée de problèmes réseau.
1. Les emplacements de disque par défaut pour le stockage des données de télémétrie dans Windows sont % LocalAppData% ou % temp%. Ces emplacements sont généralement locales sur l’ordinateur. Si l’application migrée physiquement à partir d’un emplacement vers un autre, les données de télémétrie stockées à cet emplacement seront perdue.
1. Dans Azure Web Apps (Windows), l’emplacement du disque par défaut est « D:\local\LocalAppData ». Cet emplacement n’est pas conservé et seront effacé dans les redémarrages d’application, récupérez et ainsi de suite, de mise à l’échelle entraînant la perte de données de télémétrie stockée dans ces emplacements. Les utilisateurs peuvent remplacer le stockage à un emplacement persistant comme « D:\home », mais ces emplacements persistants en dessous sont pris en charge par le stockage étendu et peuvent être lentes.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel fonctionne dans les systèmes non Windows ?*

* Malgré le nom du package/espace de noms en cours WindowsServer, ce canal est pris en charge dans les systèmes non Windows avec l’exception suivante. Dans non-Windows, le canal ne crée pas un dossier de stockage local par défaut. Les utilisateurs doivent créer un dossier de stockage local et configurer le canal pour l’utiliser. Une fois que le stockage local est configuré, le canal fonctionne même dans Windows et les systèmes non Windows.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*Le SDK crée des stockage local temporaire ? Les données sont chiffrées au stockage ?*

* Kit de développement logiciel stocke les éléments de télémétrie dans le stockage local lors de problèmes réseau ou la limitation. Ces données ne sont pas chiffrées localement.
Pour les systèmes Windows, le Kit de développement crée automatiquement un dossier local temporaire dans le répertoire TEMP ou APPDATA et limite l’accès aux administrateurs et l’utilisateur actuel uniquement.
Pour Windows-Non, aucun stockage local n’est créé automatiquement par le SDK et, par conséquent, aucune donnée est stockée localement par défaut. Les utilisateurs peuvent créer un répertoire de stockage eux-mêmes et configurer le canal pour l’utiliser. Dans ce cas, l’utilisateur est responsable de que ce répertoire est sécurisé.
En savoir plus sur [protection des données et confidentialité](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
Comme chaque SDK Application Insights, les canaux sont également open source. Lire et contribuer au code, ou signaler des problèmes à [le référentiel GitHub officiel](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Étapes suivantes

* [Échantillonnage](../../azure-monitor/app/sampling.md)
* [Résolution des problèmes du Kit de développement logiciel](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
