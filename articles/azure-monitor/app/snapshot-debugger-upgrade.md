---
title: Mise à niveau du Débogueur de capture instantanée Azure Application Insights pour les applications .NET | Microsoft Docs
description: Guide pratique pour mettre à niveau le Débogueur de capture instantanée vers la dernière version sur Azure App Services ou par le biais de packages NuGet
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899847"
---
# <a name="upgrading-the-snapshot-debugger"></a>Mise à niveau du Débogueur de capture instantanée

Pour assurer une sécurité optimale de vos données, Microsoft abandonne les protocoles TLS 1.0 et TLS 1.1, qui se sont révélés vulnérables aux attaquants les plus déterminés. Si vous utilisez une version antérieure de l’extension de site, elle nécessitera une mise à niveau pour continuer à fonctionner. Ce document décrit les étapes nécessaires pour mettre à niveau votre Débogueur de capture instantanée vers la dernière version. Il existe deux chemins de mise à niveau principaux selon que vous avez activé le Débogueur de capture instantanée à l’aide d’une extension de site ou que vous avez utilisé un SDK/NuGet ajouté à votre application. Ces deux chemins de mise à niveau sont décrits ci-dessous. 

## <a name="upgrading-the-site-extension"></a>Mise à niveau de l’extension de site

Si vous avez activé le Débogueur de capture instantanée à l’aide de l’extension de site, vous pouvez facilement effectuer la mise à niveau en effectuant les étapes suivantes :

1. Connectez-vous au portail Azure.
2. Accédez à votre ressource pour laquelle Application Insights et le Débogueur de capture instantanée sont activés. Par exemple, pour une application web, accédez à la ressource App Service :

   ![Capture d’écran d’une ressource App Service individuelle nommée DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Une fois que vous avez accédé à votre ressource, cliquez sur Application Insights dans le panneau Vue d’ensemble :

   ![Capture d’écran de trois boutons. Bouton Centrer avec le nom Application Insights sélectionné](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Un nouveau panneau s’ouvre avec les paramètres actuels. Vous pouvez les conserver en l’état, sauf si vous voulez en profiter pour modifier vos paramètres. Le bouton **Appliquer** en bas du panneau n’est pas activé par défaut et vous devez activer/désactiver l’un des paramètres pour activer le bouton. Vous n’avez pas besoin de changer les paramètres réels. Au lieu de cela, vous pouvez changer le paramètre, puis le rétablir immédiatement. Nous vous recommandons d’activer/de désactiver le paramètre de Profiler, puis de sélectionner **Appliquer**.

   ![Capture d’écran de la page Configuration d’Application Insights App Service avec le bouton Appliquer surligné en rouge](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Une fois que vous cliquez sur **Appliquer**, vous devez confirmer les changements apportés.

    > [!NOTE]
    > Le site sera redémarré dans le cadre du processus de mise à niveau.

   ![Capture d’écran de l’invite d’application de la supervision d’App Service. La zone de texte affiche le message : « Nous allons maintenant appliquer les changements de vos paramètres d’application et installer nos outils pour lier votre ressource Application Insights à l’application web. Cette opération redémarre le site. Voulez-vous continuer ?](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Cliquez sur **Oui** pour appliquer les changements. Pendant le processus, une notification s’affiche, indiquant que les changements sont appliqués :

   ![Capture d’écran du message Appliquer les changements - Mise à niveau des extensions qui s’affiche en haut à droite](./media/snapshot-debugger-upgrade/updating-extensions.png)

Une fois l’opération terminée, une notification **« Les changements sont appliqués. »** s’affiche.

   ![Capture d’écran d’un message indiquant que les changements sont appliqués](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Le site a maintenant été mis à niveau et est prêt à être utilisé.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Mise à niveau du Débogueur de capture instantanée à l’aide d’un SDK/NuGet

Si l’application utilise une version de `Microsoft.ApplicationInsights.SnapshotCollector` antérieure à la version 1.3.1, elle devra être mise à niveau vers une [version plus récente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pour continuer à fonctionner.
