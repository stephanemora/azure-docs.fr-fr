---
title: La mise à niveau de débogueur de captures instantanées Application Insights Azure pour les applications .NET | Microsoft Docs
description: Comment mettre à niveau le débogueur de capture instantanée vers la dernière version sur Azure App Services, ou via les packages Nuget
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632372"
---
# <a name="upgrading-the-snapshot-debugger"></a>Le débogueur de capture instantanée de la mise à niveau

Pour fournir la meilleure sécurité possible pour vos données, Microsoft se déplace en dehors de TLS 1.0 et TLS 1.1, qui se sont vulnérables aux pirates déterminés. Si vous utilisez une version antérieure de l’extension de site, elle nécessitera une mise à niveau pour continuer à travailler. Ce document décrit les étapes nécessaires pour mettre à niveau votre débogueur de capture instantanée vers la dernière version. Il existe deux chemins de mise à niveau principales en fonction de si vous avez activé le débogueur de capture instantanée à l’aide d’une extension de site ou si vous avez utilisé un kit de développement logiciel/Nuget ajouté à votre application. Les deux chemins de mise à niveau sont décrites ci-dessous. 

## <a name="upgrading-the-site-extension"></a>La mise à niveau de l’extension de site

Si vous avez activé le débogueur de capture instantanée à l’aide de l’extension de site, vous pouvez facilement mettre à niveau à l’aide de la procédure suivante :

1. Connectez-vous au portail Azure.
2. Accédez à votre ressource avec Application Insights et débogueur de capture instantanée est activée. Par exemple, pour une application Web, accédez à la ressource App Service :

   ![Capture d’écran de la ressource App Service individuelle nommée DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Une fois que vous avez accédé à votre ressource, cliquez sur Application Insights dans le panneau de vue d’ensemble :

   ![Capture d’écran de trois boutons. Bouton Centrer avec nom Application Insights est sélectionné.](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Un nouveau panneau s’ouvre avec les paramètres actuels. Sauf si vous souhaitez en profiter pour modifier vos paramètres, vous pouvez les laisser en l’état. Le **appliquer** bouton en bas du panneau n’est pas activé par défaut et vous devrez activer/désactiver un des paramètres pour activer le bouton. Vous n’êtes pas obligé de modifier des paramètres réels, au lieu de cela vous pouvez modifier le paramètre, puis la modifier de nouveau immédiatement. Nous vous recommandons de basculer le Profiler définissant, puis en sélectionnant **appliquer**.

   ![Capture d’écran de Application Insights application page Configuration de Service avec le bouton Appliquer mis en surbrillance en rouge](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Une fois que vous cliquez sur **appliquer**, vous devrez confirmer les modifications.

    > [!NOTE]
    > Le site sera redémarré dans le cadre du processus de mise à niveau.

   ![Capture d’écran de d’App Service s’appliquent à invite de surveillance. Zone de texte affiche le message : « Nous maintenant appliquer des modifications à vos paramètres d’application et installe nos outils pour lier votre ressource Application Insights à l’application web. Cette opération redémarre le site. Voulez-vous continuer ?](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Cliquez sur **Oui** pour appliquer les modifications. Au cours du processus, une notification s’affiche indiquant que les modifications sont appliquées :

   ![Capture d’écran d’appliquer les modifications - message d’extensions qui s’affiche dans le coin supérieur droit de la mise à jour](./media/snapshot-debugger-upgrade/updating-extensions.png)

Une fois terminée, un **« Les modifications sont appliquées »** notification s’affiche.

   ![Capture d’écran de message indiquant que les modifications sont appliquées.](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Le site a maintenant été mis à niveau et est prêt à utiliser.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Débogueur de capture instantanée à l’aide du Kit de développement logiciel/Nuget de la mise à niveau

Si l’application utilise une version de `Microsoft.ApplicationInsights.SnapshotCollector` inférieur à la version 1.3.1, elle devra être mis à niveau vers un [version plus récente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pour continuer à travailler.
