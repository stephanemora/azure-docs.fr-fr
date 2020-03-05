---
title: Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service | Microsoft Docs
description: Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671424"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service

Le Débogueur de capture instantanée fonctionne actuellement pour les applications ASP.NET et ASP.NET Core s’exécutant sur Azure App Service sur des plans de service Windows.

## <a id="installation"></a> Activer le Débogueur de capture instantanée
Pour activer le Débogueur de capture instantanée pour une application, suivez les instructions ci-dessous. Si vous exécutez un autre type de service Azure, voici les instructions à suivre pour activer le Débogueur de capture instantanée sur d’autres plateformes prises en charge :
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Services Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles Azure et Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Machines virtuelles ou physiques locales](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Si vous utilisez une préversion de .NET Core, suivez les instructions pour [Activer le débogueur de capture instantanée pour d’autres environnements](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) pour d’abord inclure le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) avec l’application, puis effectuez le reste des instructions ci-dessous. 

Le Débogueur de capture instantanée Application Insights est préinstallé dans le cadre de l’exécution d’App Services, mais vous devez l’activer pour obtenir des captures instantanées pour votre application App Service. Une fois que vous avez déployé une application, même si vous avez inclus le SDK Application Insights dans le code source, suivez les étapes ci-dessous pour activer le débogueur de capture instantanée.

1. Accédez au volet **App Services** dans le portail Azure.
2. Accédez au volet **Paramètres > Application Insights**.

   ![Activer App Insights sur le portail App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Suivez les instructions dans le volet pour créer une ressource ou sélectionnez une ressource App Insights existante pour superviser votre application. Vérifiez également que les deux commutateurs du Débogueur de capture instantanée sont définis sur **On** (Activé).

   ![Ajouter l’extension de site Application Insights][Enablement UI]

4. Le Débogueur de capture instantanée est maintenant activé à l’aide d’un paramètre d’application App Services.

    ![Paramètre d’application pour le Débogueur de capture instantanée][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Désactiver le Débogueur de capture instantanée

Suivez les mêmes étapes que pour **Activer le Débogueur de capture instantanée**, mais basculez les deux commutateurs du Débogueur de capture instantanée sur **Off** (Désactivé).
Nous vous recommandons d’avoir le Débogueur de capture instantanée activé sur toutes vos applications afin de faciliter le diagnostic des exceptions d’application.

## <a name="next-steps"></a>Étapes suivantes

- Générez du trafic vers votre application pouvant déclencher une exception. Attendez ensuite 10 à 15 minutes le temps que des captures instantanées soient envoyées à l’instance Application Insights.
- Consultez [Captures instantanées](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) dans le portail Azure.
- Pour obtenir de l’aide sur la résolution des problèmes liés au débogueur de capture instantanée, consultez [Résolution des problèmes liés au Débogueur de capture instantanée](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

