---
title: Créer une application iOS sur Azure App Service Mobile Apps | Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser des backends Azure Mobile App pour le développement iOS en Objective-C ou Swift
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: d0d6a3d9da2768c2d7b04bd9c4a7c24fba9eb65e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129006"
---
# <a name="create-an-ios-app"></a>Créer une application iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Présentation

Ce didacticiel présente l’ajout d’[App Service Mobile Apps](app-service-mobile-value-prop.md), un service principal cloud, à une application iOS. La première étape consiste à créer un nouveau serveur principal mobile sur Azure. Téléchargez ensuite un exemple d’application iOS *Todo list* simple qui stocke les données dans Azure.

Pour suivre ce didacticiel, vous avez besoin d’un Mac et d’un [compte Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Étape i : Créer un serveur principal d'applications mobiles Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Étape II : Configuration du projet principal

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Étape III : Télécharger et exécuter l'application iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
