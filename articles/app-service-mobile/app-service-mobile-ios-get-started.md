---
title: Créer une application iOS sur Azure App Service Mobile Apps | Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser des backends Azure Mobile App pour le développement iOS en Objective-C ou Swift
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9140de50e23447f53fd1fa204a2a67c324672397
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449146"
---
# <a name="create-an-ios-app"></a>Créer une application iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investit dans des services nouveaux et intégrés, essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent surveiller l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics**, et interagir avec des utilisateurs à l’aide du service **Push**. Les développeurs peuvent également utiliser **Auth** pour authentifier leurs utilisateurs, ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud. Découvrez [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started) dès aujourd’hui.
>

## <a name="overview"></a>Vue d'ensemble

Ce didacticiel présente l’ajout d’[App Service Mobile Apps](app-service-mobile-value-prop.md), un service principal cloud, à une application iOS. La première étape consiste à créer un nouveau serveur principal mobile sur Azure. Téléchargez ensuite un exemple d’application iOS *Todo list* simple qui stocke les données dans Azure.

Pour suivre ce didacticiel, vous avez besoin d’un Mac et d’un [compte Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et configurer le projet client et de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>Exécuter l’application iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
