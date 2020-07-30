---
title: Intégration de Notification Hubs à App Service Mobile Apps
description: Découvrez comment Azure Notification Hubs fonctionne avec Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 647c5f40c7b02b8d9b488ce0812f27c0c0dde1a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022138"
---
# <a name="integration-with-app-service-mobile-apps"></a>Intégration d’App Service Mobile Apps

Pour offrir une expérience transparente et unifiée dans l’ensemble des services Azure, [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) intègre la prise en charge des notifications Push à l’aide de Notification Hubs. [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) offre une plateforme de développement d’applications mobiles hautement évolutive pour les développeurs d’entreprise et les intégrateurs système. Disponible pour tous, elle fournit un ensemble complet de fonctionnalités pour les développeurs d’applications mobiles.

Les développeurs Mobile Apps peuvent utiliser Notification Hubs avec le flux de travail suivant :

1. Récupération du handle PNS de l’appareil
2. Enregistrez l’appareil avec Notification Hubs grâce à l’API d’enregistrement de Kit de développement logiciel (SDK) client Mobile Apps adapté.

    > [!NOTE]
    > Notez que Mobile Apps supprime tous les mots clés des inscriptions pour des raisons de sécurité. Utilisez Notification Hubs directement depuis votre serveur principal pour associer des mots clés aux appareils.

3. Envoyez des notifications de votre serveur principal d’application avec Notification Hubs

Voici certains des avantages qu’offre cette intégration aux développeurs :

- **Kits de développement logiciel (SDK) clients Mobile Apps** : ces Kits de développement logiciel (SDK) multi-plateformes fournissent des API simples pour l’inscription et la communication avec le hub de notification lié à l’application mobile de manière automatique. Les développeurs n’ont ni à rechercher les informations d’identification de Notification Hubs ni à utiliser de service supplémentaire.
  - *Transmission de notifications Push à l’utilisateur* : les Kits de développement logiciel (SDK) balisent automatiquement l’appareil avec l’identifiant utilisateur authentifié auprès de Mobile Apps pour activer le scénario de transmission de notifications Push à l’utilisateur.
  - *Transmission de notifications Push à l’appareil* : les Kits de développement logiciel (SDK) utilisent automatiquement l’ID d’installation de Mobile Apps en tant que GUID pour l’inscription auprès de Notification Hubs, ce qui évite aux développeurs d’avoir à gérer plusieurs GUID de service.
- **Modèle d’installation** : Mobile Apps fonctionne avec le modèle d’émission le plus récent de Notification Hubs pour représenter l’ensemble des propriétés d’émission associées à un appareil dans une installation JSON alignée avec les services de notifications Push et facile à utiliser.
- **Flexibilité** : les développeurs peuvent toujours choisir d’utiliser Notification Hubs directement, même avec l’intégration effective.
- **Expérience intégrée dans le [portail Azure](https://portal.azure.com)**  : la fonctionnalité Notifications Push est représentée visuellement dans Mobile Apps. Les développeurs peuvent utiliser le hub de notification associé en toute simplicité via Mobile Apps.
