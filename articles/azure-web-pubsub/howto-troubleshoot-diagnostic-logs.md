---
title: Comment résoudre des problèmes à l’aide des journaux de diagnostic du service Azure Web PubSub
description: Découvrez comment obtenir les journaux de diagnostic et les utiliser pour résoudre des problèmes.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 119591c4a337cf622b95fa4ed70a7508d8acb99e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963079"
---
# <a name="how-to-troubleshoot-with-diagnostic-logs"></a>Comment résoudre des problèmes à l’aide de journaux de diagnostic

Ce guide pratique vous présente les options permettant d’obtenir les journaux de diagnostic et la façon de les utiliser pour résoudre des problèmes.

## <a name="whats-the-diagnostic-logs"></a>Que sont les journaux de diagnostic ?

Les journaux de diagnostic fournissent une vue plus détaillée des informations de connectivité et de messagerie à l’instance du service Azure Web PubSub. Ils peuvent être utilisés pour l’identification des problèmes, le suivi des connexions, le suivi des messages et l’analyse.

Il existe deux types de journaux : le journal de connectivité et le journal de messagerie.

### <a name="connectivity-logs"></a>Journaux de connectivité

Les journaux de connectivité fournissent des informations détaillées sur les connexions au hub Azure Web PubSub. Par exemple, des informations de base (identifiant utilisateur, ID de connexion, etc.) et des informations sur l’événement (connexion, déconnexion, abandon, etc.). C’est la raison pour laquelle le journal de connectivité est utile pour résoudre les problèmes liés à la connexion. 

### <a name="messaging-logs"></a>Journaux de messagerie

Les journaux de messagerie fournissent des informations de suivi pour les messages du hub Azure Web PubSub reçus et envoyés via le service Azure Web PubSub. Par exemple, l’ID de suivi et le type de message du message. En général, le message est enregistré lorsqu’il arrive au service ou en part. Les journaux de messagerie sont donc utiles pour résoudre les problèmes liés aux messages. 

## <a name="capture-diagnostic-logs-with-azure-web-pubsub-service-live-trace-tool"></a>Capturer les journaux de diagnostic avec l’outil de suivi dynamique du service Azure Web PubSub 

L’outil de suivi dynamique du service Azure Web PubSub permet de collecter des journaux de diagnostic en temps réel, ce qui est utile pour effectuer un suivi de l’environnement de développement du client. L’outil de suivi dynamique peut capturer à la fois les journaux de connectivité et les journaux de messagerie.

> [!NOTE]
> Les journaux de diagnostic en temps réel capturés par l’outil de suivi dynamique sont facturés comme des messages (trafic sortant).

> [!NOTE]
> L’instance du service Azure Web PubSub créée au niveau de service gratuit a une limite quotidienne de messages (trafic sortant).

### <a name="launch-the-live-trace-tool"></a>Lancer l’outil de suivi dynamique

1. Accédez au portail Azure. 
1. Dans la page **Paramètres de diagnostic** de votre instance du service Azure Web PubSub, sélectionnez **Ouvrir l’outil de suivi dynamique**. 

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="Lancer l’outil de suivi dynamique.":::

### <a name="capture-the-diagnostic-logs"></a>Capturer les journaux de diagnostic

L’outil de suivi dynamique offre certaines fonctionnalités fondamentales pour vous aider à capturer les journaux de diagnostic à des fins de dépannage.

* **Capturer** : Commencez à capturer les journaux de diagnostic en temps réel de l’instance Azure Web PubSub à l’aide de l’outil de suivi dynamique.
* **Effacer** : Effacez les journaux de diagnostic en temps réel capturés.
* **Filtre de journal** : L’outil de suivi dynamique vous permet de filtrer les journaux de diagnostic en temps réel capturés à l’aide d’un mot clé spécifique. Le séparateur commun (par exemple, espace, virgule, point-virgule, etc.) sera traité comme faisant partie du mot-clé. 
* **État** : L’état indique si l’outil de suivi dynamique est connecté ou déconnecté de l’instance spécifique.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="Capturer les journaux de diagnostic avec l’outil de suivi dynamique.":::

Les journaux de diagnostic en temps réel capturés par l’outil de suivi dynamique contiennent des informations détaillées pour la résolution des problèmes. 

| Nom | Description |
| ------------ |  ------------------------ | 
| Temps | Heure de l’événement de journal |
| Niveau du journal | Niveau d’événement du journal (trace/débogage/informations/avertissement/erreur) |
| Nom de l'événement | Nom d’opération de l’événement |
| Message | Message détaillé de l’événement de journal |
| Exception | Exception d’exécution du service Azure Web PubSub |
| Hub | Nom du hub défini par l’utilisateur |
| ID de connexion | Identité de la connexion |
| ID d'utilisateur | Identité de l’utilisateur |
| IP | Adresse IP du client | 

Une fois que le service Azure Web PubSub sera en disponibilité générale, l’outil de suivi dynamique permettra également d’exporter les journaux dans un format spécifique et de les partager avec d’autres personnes à des fins de dépannage. 

## <a name="capture-diagnostic-logs-with-azure-monitor"></a>Capturer les journaux de diagnostic avec Azure Monitor

L’intégration à [Azure Monitor](https://azure.microsoft.com/services/monitor/), [Stockage Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) et [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) pour capturer les journaux de diagnostic n’est pas prise en charge dans la préversion publique.

## <a name="troubleshoot-with-the-diagnostic-logs"></a>Résoudre les problèmes à l’aide des journaux de diagnostic

En cas de problème de croissance ou de perte inattendues de la connexion, vous pouvez tirer parti des journaux de diagnostic pour résoudre les problèmes. Les problèmes courants sont souvent liés à des modifications inattendues de la quantité des connexions, où les connexions atteignent les limites de connexion et entraînent un échec d’autorisation.

### <a name="unexpected-connection-number-changes"></a>Modifications inattendues du nombre de connexions

#### <a name="unexpected-connection-dropping"></a>Abandon de connexion inattendu

Si une connexion est perdue, les journaux de diagnostic enregistrent cet événement de déconnexion avec `ConnectionAborted` ou `ConnectionEnded` dans `operationName`.

La différence entre `ConnectionAborted` et `ConnectionEnded` est que `ConnectionEnded` est une déconnexion attendue déclenchée par le côté client ou serveur. Tandis que `ConnectionAborted` est généralement un événement d’abandon de connexion inattendu dont le motif est fourni dans `message`.

Les motifs d’abandon sont répertoriés dans le tableau suivant :

| Motif | Description |
| ------- | ------- |
| Le nombre de connexions atteint la limite | Le nombre de connexions atteint la limite de votre niveau tarifaire actuel. Envisagez une montée en puissance de l’unité de service
| Rechargement du service, reconnexion | Le service Azure Web PubSub se recharge. Vous devez implémenter votre propre mécanisme de reconnexion ou vous reconnecter manuellement au service Azure Web PubSub |
| Erreur temporaire de serveur interne | Une erreur temporaire se produit dans le service Azure Web PubSub, qui doit être récupérée automatiquement

#### <a name="unexpected-connection-growing"></a>Croissance inattendue des connexions

Pour résoudre les problèmes de croissance inattendue des connexions, la première chose à faire est de filtrer les connexions supplémentaires. Vous pouvez ajouter un ID d’utilisateur de test unique à votre connexion de client de test. Ensuite, vérifiez les journaux de diagnostic. Si vous constatez que plusieurs connexions de client ont le même ID d’utilisateur de test ou la même adresse IP, il est probable que le côté client crée et établit plus de connexions que prévu. Vérifiez votre côté client.

### <a name="authorization-failure"></a>Échec de l’autorisation

Si l’erreur 401 Non autorisé est retournée pour des demandes de clients, vérifiez vos journaux de diagnostic. Si vous rencontrez l’erreur `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`, cela signifie qu’aucune des audiences dans votre jeton d’accès n’est valide. Essayez d’utiliser les audiences valides suggérées dans le journal.

### <a name="throttling"></a>Limitation

Si vous constatez que vous ne pouvez pas établir de connexions client au service Azure Web PubSub, vérifiez vos journaux de diagnostic. Si vous rencontrez `Connection count reaches limit` dans le journal de diagnostic, cela signifie que vous établissez un trop grand nombre de connexions au service Azure Web PubSub, et que la limite du nombre de connexions est atteinte. Envisagez d’effectuer un scale-up de votre instance du service Azure Web PubSub. Si vous rencontrez `Message count reaches limit` dans le journal de diagnostic, cela signifie que vous utilisez le niveau gratuit et épuisez le quota de messages. Si vous souhaitez envoyer davantage de messages, envisagez de mettre à niveau votre instance du service Azure Web PubSub au niveau standard pour envoyer des messages supplémentaires. Pour plus d’informations, voir [Tarification du service Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub/).