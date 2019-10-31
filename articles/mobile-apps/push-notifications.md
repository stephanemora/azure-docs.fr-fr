---
title: Importance des notifications Push dans vos applications mobiles avec Visual Studio App Center et Azure Notification Hubs
description: Découvrez les services comme App Center qui vous permettent de collaborer avec les utilisateurs de vos applications mobiles.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795213"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Collaborer avec les utilisateurs de vos applications en envoyant des notifications Push 

Que vous génériez des applications grand public ou professionnelles, vous souhaitez que les utilisateurs puissent utiliser activement votre application. Il vous arrive souvent de vouloir partager de nouvelles infos, des mises à jour de jeux, des offres intéressantes, des mises à jour de produits ou toute information pertinente avec vos utilisateurs. Afin d’améliorer la collaboration avec vos utilisateurs et qu’ils continuent d’utiliser votre application, vous avez besoin d’un moyen de communiquer avec eux en temps réel.

Les notifications Push offrent un moyen rapide et efficace de communiquer avec les utilisateurs de vos applications. Vous pouvez contacter les utilisateurs au bon moment et leur faire parvenir les informations souhaitées, généralement dans une fenêtre contextuelle ou une boîte de dialogue sur un appareil mobile, indépendamment de ce qu’ils sont en train de faire.

## <a name="value-of-push-notifications"></a>Valeur des notifications Push
Les notifications push présentent un intérêt à la fois pour les utilisateurs finaux et l’entreprise.

Les entreprises peuvent :
- **Communiquer directement avec les utilisateurs** en leur envoyant des messages sur la plateforme prise en charge au moment approprié.
- **Renforcer la collaboration des utilisateurs** en leur envoyant des mises à jour et des rappels en temps réel, en les encourageant à utiliser régulièrement votre application.
- **Fidéliser les utilisateurs** et relancer les utilisateurs inactifs qui ont téléchargé l’application, mais qui ne l’utilisent pas, pour qu’ils redeviennent actifs.
- **Augmenter les taux de conversion** en analysant le comportement de l’utilisateur final, en segmentant les utilisateurs finaux et en tirant parti des campagnes basées sur les notifications Push mobiles.
- **Promouvoir les produits et services** en envoyant des messages Push et des mises à jour en temps utile aux utilisateurs.
- **Cibler les utilisateurs** en leur envoyant des messages Push personnalisés.

Pour les utilisateurs des applications, les notifications Push :
- **Présentent une valeur et des informations** en temps réel.
- **Rappellent aux utilisateurs** d’utiliser l’application.

Utilisez les services suivants pour activer les notifications Push dans vos applications mobiles.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Le service [App Center Push](/appcenter/push/) vous permet de collaborer avec vos utilisateurs en envoyant des messages ciblés vers des utilisateurs iOS, Android et Windows sans avoir à gérer le processus d’envoi de notifications aux appareils à l’aide des services de notification Push. Basé sur Azure Notification Hubs, ce service élimine les complexités associées à la transmission manuelle des notifications en fournissant un tableau de bord puissant.

**Fonctionnalités clés**
- **Envoyez des notifications Push à des appareils mobiles** sur plusieurs plateformes.
- Utilisez des notifications pour envoyer des données à une application, afficher un message pour l’utilisateur ou déclencher une action par l’application.
- Cibles des notifications : 
    - Envoyez des messages de diffusion à **tous les appareils inscrits**.
    - Envoyez des notifications à des **audiences** créées en fonction des informations sur l’appareil et des propriétés personnalisées.
    - Envoyez des notifications à des **utilisateurs spécifiques**.
    - Envoyez des notifications à des **appareils spécifiques**.
- La **télémétrie enrichie** de notification Push, d’appareil et d’erreur est disponible dans le portail App Center.
- **Prise en charge de plateformes** : iOS, Android, macOS, Xamarin, React Native, Unity, Cordova.

**Informations de référence**
- [S’inscrire auprès d’App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main d’App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
Le service [Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) fournit un moteur Push facile à utiliser et avec scale-out qui vous permet d’envoyer des notifications à n’importe quelle plateforme et à partir de n’importe quel service back-end (cloud ou local).

**Fonctionnalités clés**
- **Envoyez des notifications Push** vers n’importe quelle plateforme à partir de n’importe quel service back-end, dans le cloud ou en local.
- **Diffusez rapidement** des notifications Push à des millions d’appareils mobiles avec un seul appel d’API.
- Personnalisez les notifications Push selon le client, la langue et le lieu.
- Définissez et notifiez dynamiquement des **segments de clientèle**.
- **Effectuez une mise à l’échelle instantanée** vers des millions d’appareils mobiles.
- **Prise en charge de plateformes** : iOS, Android, Windows, Kindle, Baidu.
        
**Informations de référence**
- [Portail Azure](https://portal.azure.com) 
- [Bien démarrer avec Azure Notification Hubs](/azure/notification-hubs/)   
- [Démarrages rapides](/azure/notification-hubs/create-notification-hub-portal)
- [Exemples](/azure/notification-hubs/samples)
