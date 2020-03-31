---
title: Importance des notifications Push dans vos applications mobiles avec Visual Studio App Center et Azure Notification Hubs
description: Découvrez les services comme Visual Studio App Center que vous pouvez utiliser pour collaborer avec les utilisateurs de vos applications mobiles.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235325"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Collaborer avec les utilisateurs de vos applications en envoyant des notifications Push

Que vous génériez des applications grand public ou professionnelles, vous souhaitez que les utilisateurs puissent utiliser activement votre application. Il vous arrive souvent de vouloir partager de nouvelles infos, des mises à jour de jeux, des offres intéressantes, des mises à jour de produits ou toute autre information pertinente avec vos utilisateurs. Pour améliorer la collaboration avec vos utilisateurs et qu’ils continuent d’utiliser votre application, vous avez besoin d’un moyen de communiquer avec eux en temps réel.

Les notifications Push offrent un moyen rapide et efficace de communiquer avec les utilisateurs de vos applications. Vous pouvez contacter les utilisateurs au bon moment et leur faire parvenir les informations souhaitées, généralement dans une fenêtre contextuelle ou une boîte de dialogue sur un appareil mobile, indépendamment de ce qu’ils sont en train de faire.

## <a name="value-of-push-notifications"></a>Valeur des notifications Push
Les notifications Push fournissent une valeur ajoutée aux utilisateurs et aux entreprises.

Les entreprises peuvent :
- Communiquer directement avec les utilisateurs en envoyant des messages sur la plateforme prise en charge au moment approprié.
- Renforcer la collaboration des utilisateurs en leur envoyant des mises à jour et des rappels en temps réel, en les encourageant à utiliser régulièrement l’application.
- Fidéliser les utilisateurs et relancer les utilisateurs inactifs qui ont téléchargé l’application, mais qui ne l’utilisent pas, pour qu’ils redeviennent actifs.
- Augmenter les taux de conversion en analysant le comportement de l’utilisateur, en segmentant les utilisateurs et en tirant parti des campagnes basées sur les notifications Push mobiles.
- Promouvoir les produits et services en envoyant des messages Push et des mises à jour en temps utile aux utilisateurs.
- Cibler les utilisateurs en envoyant des messages Push personnalisés.

Pour les utilisateurs des applications, les notifications Push :
- Présentent une valeur et des informations en temps réel.
- Rappellent aux utilisateurs d’utiliser l’application.

Utilisez les services suivants pour activer les notifications Push dans vos applications mobiles.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Avec [App Center Push](/appcenter/push/) vous pouvez envoyer des messages ciblés vers des utilisateurs iOS, Android et Windows sans avoir à gérer le processus d’envoi de notifications aux appareils à l’aide des services de notification Push. Basé sur Azure Notification Hubs, ce service élimine les complexités associées à la transmission manuelle des notifications en fournissant un tableau de bord puissant.

**Fonctionnalités clés**
- Envoyez des notifications Push à des appareils mobiles sur plusieurs plateformes.
- Utilisez des notifications pour envoyer des données à une application, afficher un message pour l’utilisateur ou déclencher une action par l’application.
- Utilisez des cibles de notification pour : 
    - Diffuser des messages à tous les appareils inscrits.
    - Envoyer des notifications à des audiences créées en fonction des informations sur l’appareil et des propriétés personnalisées.
    - Envoyer des notifications à des utilisateurs spécifiques.
    - Envoyer des notifications à des appareils spécifiques.
- Utiliser les données de télémétrie enrichies sur les notifications Push, les appareils et les erreurs disponibles dans le portail App Center.
- Bénéficier d’une prise en charge de plateforme pour iOS, Android, macOS, Xamarin, React Native, Unity et Cordova.

**Informations de référence**
- [S’inscrire sur Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Prise en main d’App Center Push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) offre un moteur Push facile à utiliser et mis à l’échelle. Vous pouvez l’utiliser pour envoyer des notifications vers n’importe quelle plateforme et à partir de n’importe quel service back end, dans le cloud ou en local.

**Fonctionnalités clés**
- Envoyez des notifications Push vers n’importe quelle plateforme à partir de n’importe quel service back-end, dans le cloud ou en local.
- Diffusez rapidement des notifications Push à des millions d’appareils mobiles avec un seul appel d’API.
- Personnalisez les notifications Push selon le client, la langue et le lieu.
- Définissez et notifiez dynamiquement des segments de clientèle.
- Effectuez une mise à l’échelle instantanée vers des millions d’appareils mobiles.
- Obtenez la prise en charge des plateformes iOS, Android, Windows, Kindle et Baidu.
        
**Informations de référence**
- [Azure portal](https://portal.azure.com) 
- [Bien démarrer avec Azure Notification Hubs](/azure/notification-hubs/)
- [Démarrages rapides](/azure/notification-hubs/create-notification-hub-portal)
- [Exemples](/azure/notification-hubs/samples)
