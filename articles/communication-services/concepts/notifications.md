---
title: Notifications dans Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Envoyez des notifications aux utilisateurs d’applications reposant sur Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 88a5ad66cb401715790bcff8c773e6d7881b103d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487693"
---
# <a name="communication-services-notifications"></a>Notifications Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Les bibliothèques de client Azure Communication Services Chat et Calling créent un canal de messagerie en temps réel qui permet l’envoi (push) des messages de signalisation aux clients connectés de manière efficace et fiable. Cela vous permet de créer des fonctionnalités de communication riches en temps réel dans vos applications sans avoir à implémenter une logique d’interrogation HTTP compliquée. Toutefois, sur les applications mobiles, ce canal de signalisation reste connecté uniquement quand votre application est active au premier plan. Si vous souhaitez que vos utilisateurs reçoivent des appels entrants ou des messages de conversation lorsque votre application est en arrière-plan, vous devez utiliser des notifications Push.

Les notifications Push vous permettent d’envoyer des informations à partir de votre application vers les appareils mobiles des utilisateurs. Vous pouvez utiliser des notifications Push pour afficher une boîte de dialogue, émettre un signal sonore ou afficher l’interface utilisateur des appels entrants. Azure Communication Services fournit des intégrations avec [Azure Event Grid](../../event-grid/overview.md) et [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) qui vous permettent d’ajouter des notifications Push à vos applications.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Déclencher des notifications Push via Azure Event Grid

Azure Communication Services s’intègre à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pour fournir des notifications d’événements en temps réel de manière fiable, scalable et sécurisée. Vous pouvez tirer parti de cette intégration pour créer un service de notification qui fournisse des notifications Push mobiles à vos utilisateurs en créant un abonnement Event Grid qui déclenche une [fonction Azure](../../azure-functions/functions-overview.md) ou un webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Schéma montrant comment Communication Services s’intègre à Event Grid.":::

Apprenez-en davantage sur la [gestion des événements dans Azure Communication Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Envoyer des notifications Push via Azure Notification Hubs

Vous pouvez connecter un hub de notification Azure à votre ressource Communication Services afin d’envoyer automatiquement des notifications Push à l’appareil mobile d’un utilisateur lorsqu’il reçoit un appel entrant. Vous devez utiliser ces notifications Push pour réveiller votre application à partir de l’arrière-plan et afficher l’interface utilisateur permettant à l’utilisateur d’accepter ou de refuser l’appel.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Schéma montrant comment Communication Services s’intègre à Azure Notification Hubs.":::

Communication Services utilise Azure Notification Hub en tant que service direct pour communiquer avec les différents services de notification Push spécifiques à la plateforme à l’aide de l’API [Envoi direct](/rest/api/notificationhubs/direct-send). Cela vous permet de réutiliser vos ressources et configurations Azure Notification Hub existantes pour fournir des notifications d’appel fiables et à faible latence à vos applications.

> [!NOTE]
> Seules les notifications Push d’appel sont prises en charge.

### <a name="notification-hub-provisioning"></a>Provisionnement des hubs de notification

Pour envoyer des notifications Push aux appareils clients à l’aide de Notification Hubs, [créez un hub de notification](../../notification-hubs/create-notification-hub-portal.md) dans le même abonnement que votre ressource Communication Services. Vous devez configurer le hub de notification Azure pour le système Platform Notification System que vous souhaitez utiliser. Pour savoir comment obtenir des notifications Push dans votre application cliente provenant de Notification Hubs, consultez [Prise en main de Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) et sélectionnez votre plateforme de client cible dans la liste déroulante en haut de la page.

> [!NOTE]
> Les plateformes APNs et FCM sont prises en charge.
La plateforme APNs doit être configurée avec le mode d’authentification par jeton. Le mode d’authentification par certificat n’est pas pris en charge pour le moment.

Une fois que votre hub de notification est configuré, vous pouvez l’associer à votre ressource Communication Services en fournissant une chaîne de connexion pour le hub à l’aide du client Azure Resource Manager ou par le biais du portail Azure. La chaîne de connexion doit contenir des autorisations `Send`. Nous vous recommandons de créer une autre stratégie d’accès avec des autorisations `Send` uniquement, spécifiquement pour votre hub. Découvrez plus en détail les [stratégies de sécurité et d’accès de Notification Hubs](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Utilisation du client Azure Resource Manager pour lier votre hub de notification

Pour vous connecter à Azure Resource Manager, exécutez la commande suivante et connectez-vous à l’aide de vos informations d’identification.

```console
armclient login
```

 Une fois connecté, exécutez la commande suivante pour provisionner le hub de notification :

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Utilisation du portail Azure pour lier votre hub de notification

Dans le portail, accédez à votre ressource Azure Communication Services. Dans la ressource Communication Services, sélectionnez Notifications Push dans le menu de gauche de la page Communication Services et connectez le hub de notification que vous avez provisionné précédemment. Vous devez fournir vos chaîne de connexion et ID de ressource ici :

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Capture d’écran montrant les paramètres de notifications Push dans le portail Azure.":::

> [!NOTE]
> Si la chaîne de connexion du hub de notification Azure est mise à jour, la ressource Communication Services doit également être mise à jour.
Toute modification relative à la façon dont le hub est lié est reflétée dans le plan de données (par exemple lors de l’envoi d’une notification) pendant une période maximale de ``10`` minutes. Cela s’applique également lorsque le hub est lié pour la première fois **si** des notifications ont été envoyées avant.

### <a name="device-registration"></a>Enregistrement de l’appareil

Reportez-vous au [guide de démarrage rapide des appels vocaux](../quickstarts/voice-video-calling/getting-started-with-calling.md) pour découvrir comment inscrire votre handle d’appareil auprès de Communication Services.

### <a name="troubleshooting-guide-for-push-notifications"></a>Guide de résolution des problèmes pour les notifications Push

Quand vous ne voyez pas de notifications Push sur votre appareil, il existe trois emplacements où elles ont pu être supprimées :

- Azure Notification Hubs n’a pas accepté la notification d’Azure Communication Services
- Le système Platform Notification System (par exemple APNs et FCM) n’a pas accepté la notification d’Azure Notification Hubs
- Le système Platform Notification System n’a pas remis la notification à l’appareil.

Le premier emplacement où une notification peut être supprimée (Azure Notification Hubs n’a pas accepté les notifications d’Azure Communication Services) est décrit ci-dessous. Pour les deux autres emplacements, consultez [Diagnostiquer les problèmes de perte des notifications dans Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Pour savoir si votre ressource Communication Services envoie des notifications à Azure Notification Hubs, examinez la métrique `incoming messages` dans les [métriques Azure Notification Hub](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs) liées.

Voici quelques erreurs de configuration courantes qui peuvent constituer la raison pour laquelle Azure Notification Hub n’accepte pas les notifications de votre ressource Communication Services.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Le hub de notification Azure n’est pas lié à la ressource Communication Services

Il se peut que vous n’ayez pas lié le hub de notification Azure à la ressource Communication Services. Vous pouvez consulter la section [Provisionnement des hubs de notification](#notification-hub-provisioning) pour voir comment les lier.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>Le hub de notification Azure lié n’est pas configuré

Vous devez configurer le hub de notification lié avec les informations d’identification du système Platform Notification System pour la plateforme (par exemple, iOS ou Android) que vous souhaitez utiliser. Pour plus d’informations sur la façon de procéder, consultez [Configurer les notifications Push dans un hub de notification](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>Le hub de notification Azure lié n’existe pas

Le hub de notification Azure lié à votre ressource Communication Services n’existe plus. Vérifiez que le hub de notification lié existe toujours.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>La plateforme APNs Azure Notification Hub est configurée avec le mode d’authentification par certificat

Si vous souhaitez utiliser la plateforme APNs avec le mode d’authentification par certificat, celui-ci n’est pas pris en charge pour le moment. Vous devez configurer la plateforme APNs avec le mode d’authentification par jeton comme indiqué dans [Configurer les notifications Push dans un hub de notification](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>La chaîne de connexion liée n’a pas d’autorisation `Send`

La chaîne de connexion que vous avez utilisée pour lier le hub de notification à la ressource Communication Services doit disposer de l’autorisation `Send`. Pour plus d’informations sur la façon de créer une chaîne de connexion ou d’afficher la chaîne de connexion actuelle à partir de votre hub de notification Azure, vous pouvez consulter [Stratégies de sécurité et d’accès Notification Hubs](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>La chaîne de connexion liée ou l’ID de ressource du hub de notification Azure ne sont pas valides

Veillez à configurer la ressource Communication Services avec la chaîne de connexion correcte et l’ID de ressource du hub de notification Azure

#### <a name="the-linked-connection-string-is-regenerated"></a>La chaîne de connexion liée est regénérée

Si vous avez regénéré la chaîne de connexion de votre hub de notification Azure lié, vous devez mettre à jour la chaîne de connexion avec la nouvelle dans votre ressource Communication Services en [liant à nouveau le hub de notification](#notification-hub-provisioning).

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](../../event-grid/overview.md).
* Pour en savoir plus sur les concepts d’Azure Notification Hub, consultez [Documentation Azure Notification Hubs](../../notification-hubs/index.yml)
