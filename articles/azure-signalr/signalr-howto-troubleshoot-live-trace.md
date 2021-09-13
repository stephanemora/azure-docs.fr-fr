---
title: Comment utiliser l’outil de trace dynamique pour le service Azure SignalR
description: Découvrez comment utiliser l’outil de trace dynamique pour le service Azure SignalR
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 5a1cf026759a8112901624d9db89d7c344930cb8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297425"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>Comment utiliser l’outil de trace dynamique pour le service Azure SignalR

L’outil de trace dynamique est une application web unique pour la capture et l’affichage des traces dynamiques dans le service Azure SignalR. Les traces dynamiques peuvent être collectées en temps réel sans aucune dépendance vis-à-vis d’autres services.
Vous pouvez activer et désactiver la fonctionnalité de trace dynamique en un seul clic. Vous pouvez également choisir n’importe quelle catégorie de journal qui vous intéresse.

> [!NOTE]
> Notez que les traces dynamiques sont comptabilisées comme messages sortants.

## <a name="launch-the-live-trace-tool"></a>Lancer l’outil de suivi dynamique

1. Accédez au portail Azure.
2. Activez la case à cocher **Activer la trace dynamique**.
3. Cliquez sur le bouton **Enregistrer** dans la barre d’outils et attendez que les modifications prennent effet.
4. Dans la page **Paramètres de diagnostic** de votre instance du service Azure Web PubSub, sélectionnez **Ouvrir l’outil de suivi dynamique**. 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="Capture d’écran montrant comment lancer l’outil de trace dynamique.":::

## <a name="capture-live-traces"></a>Capturer des traces dynamiques

L’outil de trace dynamique offre certaines fonctionnalités fondamentales vous permettant de capturer les traces dynamiques à des fins de dépannage.

* **Capturer** : Commencez à capturer les traces dynamiques en temps réel de l’instance Azure Web PubSub à l’aide de l’outil de trace dynamique.
* **Effacer** : Effacez les traces dynamiques en temps réel capturées.
* **Exporter** : Exportez les traces dynamiques dans un fichier. Le format de fichier actuellement pris en charge est CSV.
* **Filtre de journal** : L’outil de trace dynamique vous permet de filtrer les traces dynamiques en temps réel capturées à l’aide d’un mot clé spécifique. Le séparateur commun (par exemple, espace, virgule, point-virgule, etc.) sera traité comme faisant partie du mot-clé. 
* **État** : L’état indique si l’outil de suivi dynamique est connecté ou déconnecté de l’instance spécifique.

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="Capture d’écran montrant la capture de traces dynamiques avec l’outil de trace dynamique.":::

Les traces dynamiques en temps réel capturées par l’outil de trace dynamique contiennent des informations détaillées pour la résolution des problèmes. 

| Name | Description |
| ------------ |  ------------------------ | 
| Temps | Heure de l’événement de journal |
| Niveau du journal | Niveau d’événement du journal (trace/débogage/informations/avertissement/erreur) |
| Nom de l'événement | Nom d’opération de l’événement |
| Message | Message détaillé de l’événement de journal |
| Exception | Exception d’exécution du service Azure Web PubSub |
| Hub | Nom du hub défini par l’utilisateur |
| ID de connexion | Identité de la connexion |
| ID de connexion | Type de la connexion. Les valeurs autorisées sont `Server` (connexions entre le serveur et le service) et `Client` (connexions entre le client et le service)|
| ID d'utilisateur | Identité de l’utilisateur |
| IP | Adresse IP du client |
| Server Sticky | Mode de routage du client. Les valeurs autorisées sont `Disabled`, `Preferred` et `Required`. Pour plus d’informations, consultez [ServerStickyMode](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode). |
| Transport | Transport que le client peut utiliser pour envoyer des requêtes HTTP. Les valeurs autorisées sont `WebSockets`, `ServerSentEvents` et `LongPolling`. Pour plus d’informations, consultez [HttpTransportType](/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype). |

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a permis d’apprendre à utiliser l’outil de trace dynamique. Vous pouvez également apprendre à traiter les problèmes courants :
* Guides de dépannage : Consultez notre [guide de dépannage](./signalr-howto-troubleshoot-guide.md) pour savoir comment résoudre les problèmes courants liés aux traces dynamiques.
* Méthodes de résolution des problèmes : Consultez notre [présentation de méthodes de résolution des problèmes](./signalr-howto-troubleshoot-method.md), qui simplifient l’auto-diagnostic pour trouver la cause racine directement ou réduire le problème.