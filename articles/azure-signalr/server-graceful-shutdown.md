---
title: Arrêter votre serveur d’applications de manière appropriée
description: Cet article fournit des informations sur l’arrêt approprié du serveur d’applications SignalR
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201669"
---
# <a name="server-graceful-shutdown"></a>Arrêt approprié du serveur
Microsoft Azure SignalR Service fournit deux modes pour arrêter un serveur de manière appropriée. 

L’utilisation de cette fonctionnalité présente un avantage clé : éviter à votre client de subir des pertes de connexion inattendues. 

À la place, vous pouvez soit attendre la fermeture de vos connexions clientes en fonction de votre logique métier, soit migrer la connexion cliente vers un autre serveur sans perdre de données. 

## <a name="how-it-works"></a>Fonctionnement

En général, il existe quatre étapes dans un processus d’arrêt approprié :

1. **Mettre le serveur hors connexion**

    Cela signifie qu’aucune autre connexion cliente n’est routée vers ce serveur.

2. **Déclencher des crochets `OnShutdown`**

    Vous pouvez inscrire des crochets d’arrêt pour chaque hub que vous avez sur votre serveur.
    Ils sont appelés conformément à l’ordre inscrit, dès que nous recevons une réponse **FINACK** en provenance de notre instance Azure SignalR Service, ce qui signifie que le serveur a été mis hors connexion dans Azure SignalR Service.

    Vous pouvez diffuser des messages ou effectuer des travaux de nettoyage à cette étape. Une fois que tous les crochets d’arrêt auront été exécutés, nous passerons à la phase suivante.

3. **Attendre la fin de toutes les connexions clientes**. Selon le mode que vous choisissez, il peut s’agir de :

    **Mode ayant la valeur WaitForClientsToClose**

    Azure SignalR Service contient les clients existants.

    Vous devrez peut-être diffuser un message de fermeture à tous les clients, par exemple, puis laisser vos clients décider quand se fermer/se reconnecter.

    Lisez [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) pour accéder à des exemples d’utilisation qui montrent comment nous diffusons un message de « sortie » afin de déclencher la fermeture du client dans le crochet d’arrêt.

    **Mode ayant la valeur MigrateClients**

    Azure SignalR Service tente de rerouter la connexion cliente du serveur vers un autre serveur valide. 
    
    Dans ce scénario, `OnConnectedAsync` et `OnDisconnectedAsync` sont déclenchés sur le nouveau et l’ancien serveur, respectivement, avec `IConnectionMigrationFeature` défini dans `HttpContext`, ce qui permet de déterminer si la connexion cliente a été migrée en entrée ou en sortie. Cela peut être utile en particulier pour les scénarios avec état.

    La connexion cliente est immédiatement migrée après la remise du message actuel, ce qui signifie que le message suivant est routé vers le nouveau serveur.

4. **Arrêter les connexions au serveur**

    Une fois que toutes les connexions clientes ont été fermées/migrées, ou que le délai d’expiration (30 s par défaut) est dépassé,

    Le kit SDK SignalR Server poursuit le processus d’arrêt jusqu’à cette phase, puis ferme toutes les connexions au serveur.

    Les connexions clientes sont quand même supprimées, si elles n’ont pas pu être fermées/migrées. Par exemple, il n’existe aucun serveur cible approprié, ou le message du client au serveur est incomplet.

## <a name="sample-codes"></a>Exemples de code.

Ajoutez les options suivantes avec `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>Configurez `OnConnected` et `OnDisconnected` tout en définissant un mode d’arrêt approprié pour `MigrateClients`.

Nous avons introduit « IConnectionMigrationFeature » pour indiquer si une connexion a été migrée en entrée ou en sortie.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```