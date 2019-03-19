---
title: Activer le débogueur de capture instantanée pour les applications .NET dans Azure App Service | Microsoft Docs
description: Activer le débogueur de capture instantanée pour les applications .NET dans Azure App Service
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57876222"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Activer le débogueur de capture instantanée pour les applications .NET dans Azure App Service

Débogueur de capture instantanée fonctionne actuellement pour les applications ASP.NET et ASP.NET Core qui s’exécutent sur Azure App Service sur les plans de service Windows.

## <a id="installation"></a> Activer le débogueur de capture instantanée
Pour activer le débogueur de capture instantanée pour une application, suivez les instructions ci-dessous. Si vous exécutez un autre type de service Azure, Voici des instructions pour activer le débogueur de capture instantanée sur d’autres plateformes prises en charge :
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Services Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure et Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles ou physiques en local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Débogueur de captures instantanées application Insights est préinstallé dans le cadre de l’exécution des Services d’application, mais vous devez l’activer une session sur des captures instantanées get pour votre application app Service. Une fois que vous avez déployé une application, même si vous avez inclus le SDK Application Insights dans le code source, suivez les étapes ci-dessous pour activer le débogueur de capture instantanée.

1. Accédez au volet **App Services** dans le portail Azure.
2. Accédez au volet **Paramètres > Application Insights**.

   ![Activer App Insights sur le portail App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Suivez les instructions dans le volet pour créer une ressource ou sélectionnez une ressource App Insights existante pour superviser votre application. Vérifiez également que les deux commutateurs pour le débogueur de capture instantanée sont **sur**.

   ![Ajouter l’extension de site Application Insights][Enablement UI]

4. Débogueur de capture instantanée est maintenant activé à l’aide d’un paramètre d’application App Services.

    ![Paramètre d’application pour le débogueur de capture instantanée][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Désactiver le débogueur de capture instantanée

Suivez les mêmes étapes que pour **activer un débogueur de capture instantanée**, mais basculer les deux commutateurs pour le débogueur de capture instantanée à **hors**.
Nous vous recommandons d’avoir le débogueur de capture instantanée est activée sur toutes vos applications afin de faciliter le diagnostic des exceptions d’application.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

