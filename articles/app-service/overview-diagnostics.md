---
title: Présentation des diagnostics Azure App Service | Microsoft Docs
description: Découvrez comment résoudre les problèmes avec votre application avec les diagnostics App Service.
keywords: app service, azure app service, diagnostics, prise en charge, application web, dépannage, auto-assistance
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539709"
---
# <a name="azure-app-service-diagnostics-overview"></a>Présentation des diagnostics Azure App Service

Quand vous exécutez une application web, vous souhaitez anticiper tout problème, des erreurs 500 à l’arrêt de votre site. Diagnostics App Service est une manière intelligente et interactive pour vous aider à résoudre les problèmes de votre application sans aucune configuration. Lorsque vous rencontrez des problèmes avec votre application, les diagnostics App Service souligne que se passe-t-il pour vous guider vers les bonnes informations aux plus facilement et rapidement résoudre les problèmes et résoudre le problème.

Bien que cette fonctionnalité est particulièrement utile lorsque vous rencontrez des problèmes avec votre application dans les dernières 24 heures, tous les graphiques de diagnostics sont toujours disponibles pour vous permet d’analyser.

Les diagnostics App Service fonctionnent non seulement pour votre application sur Windows, mais également pour les applications exécutées sous [Linux/dans des conteneurs](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), sous [l’environnement App Service](https://docs.microsoft.com/azure/app-service/environment/intro) et dans [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Ouvrir les diagnostics App Service

Pour accéder aux diagnostics App Service, accédez à votre application web app Service ou d’un environnement App Service dans le [Azure portal](https://portal.azure.com). Dans le volet de navigation de gauche, cliquez sur **Diagnostiquer et résoudre les problèmes**.

Pour Azure Functions, accédez à votre application de fonction et dans le volet de navigation supérieur, cliquez sur **fonctionnalités de la plateforme**, puis sélectionnez **diagnostiquer et résoudre les problèmes** à partir de la **gestion des ressources** section.

Dans la page d’accueil de diagnostics App Service, vous pouvez choisir la catégorie qui décrit le mieux le problème avec votre application en utilisant les mots clés dans chaque vignette de page d’accueil. En outre, cette page est dans lequel vous trouverez **outils de Diagnostic** pour les applications Windows. Consultez [des outils de Diagnostic (uniquement pour l’application de Windows)](#diagnostic-tools-only-for-windows-app).

![Page d'accueil](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interface interactive

Une fois que vous sélectionnez une catégorie de la page d’accueil mieux adaptée à un problème de votre application, interface interactive de diagnostics de App Service, génie, peut vous aider à diagnostiquer et résoudre le problème avec votre application. Vous pouvez utiliser les raccourcis de vignette fournies par génie pour afficher le rapport de diagnostic complet de la catégorie de problème qui vous intéressent. Les raccourcis de vignette vous offrent un moyen direct de l’accès aux métriques de diagnostic.

![Raccourcis de vignette](./media/app-service-diagnostics/tile-shortcuts-2.png)

Après avoir cliqué sur ces vignettes, vous pouvez voir une liste des rubriques relatives au problème décrit dans la vignette. Ces rubriques fournissent des extraits de notables d’informations à partir de l’intégralité du rapport. Vous pouvez cliquer sur ces rubriques pour étudier les problèmes supplémentaires. En outre, vous pouvez cliquer sur **afficher le rapport complet** pour explorer toutes les rubriques sur une seule page.

![Rubriques](./media/app-service-diagnostics/application-logs-insights-3.png)

![Afficher le rapport complet](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Rapport de diagnostic

Une fois que vous choisissez d’examiner le problème en cliquant sur une rubrique, vous pouvez afficher plus de détails sur la rubrique souvent complété avec des graphiques et des fichiers markdown. Rapport de diagnostic peut être un outil puissant pour localiser le problème avec votre application.

![Rapport de diagnostic](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Contrôle d’intégrité

Si vous ignorez quel est le problème avec votre application ou que vous ne savez pas où démarrer la résolution de vos problèmes, le contrôle d’intégrité est un bon point de départ. Le contrôle d’intégrité analyse vos applications pour vous donner une vue d’ensemble rapide et interactive qui souligne ce qui est sain et quel est le problème, vous indiquant où rechercher pour examiner le problème. Son interface intelligente et interactive fournit des recommandations au fil du processus de résolution des problèmes. Contrôle d’intégrité est intégré avec l’expérience de génie pour les applications Windows et l’application web vers le bas diagnostic rapport pour les applications Linux.

### <a name="health-checkup-graphs"></a>Graphiques de bilan de santé

Il existe quatre différents graphiques dans le contrôle d’intégrité.

- **demandes et erreurs :** Un graphique qui affiche le nombre de requêtes effectuées sur les dernières 24 heures, ainsi que des erreurs de serveur HTTP.
- **performances de l’application :** Un graphique qui affiche les temps de réponse sur les dernières 24 heures pour différents groupes de centile.
- **Utilisation du processeur :** Un graphique qui affiche la pourcentage global utilisation du processeur par instance pendant les dernières 24 heures.  
- **utilisation de la mémoire :** Un graphique qui affiche l’utilisation globale de pourcentage de mémoire physique par instance pendant les dernières 24 heures.

![Contrôle d’intégrité](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Étudier les problèmes de code d’application (uniquement pour l’application de Windows)

Dans la mesure où de nombreux problèmes d’application sont liés à des problèmes au niveau du code, les diagnostics App Service s’intègrent à [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) pour mettre en évidence les exceptions et les problèmes de dépendance qui peuvent être corrélés au temps d’arrêt sélectionné. Application Insights doit être activé séparément.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Pour afficher les exceptions d’Application Insights et les dépendances, sélectionnez le **application web vers le bas** ou **application web lente** raccourcis de vignette.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Étapes de dépannage (uniquement pour l’application de Windows)

Si un problème est détecté avec une catégorie de problème spécifique dans les dernières 24 heures, vous pouvez afficher le rapport de diagnostic complet et les diagnostics App Service peut vous inviter à afficher les conseils de dépannage supplémentaires et les étapes suivantes pour une expérience plus interactive.

![Application Insights et résolution des problèmes et étapes suivantes](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Outils de diagnostic (uniquement pour l’application de Windows)

Outils de diagnostics incluent des outils de diagnostic plus avancées qui aident à vous vérifiez si une application code problèmes, lenteur, chaînes de connexion et bien plus encore. et outils proactive qui vous aident à atténuer les problèmes avec l’utilisation du processeur, les demandes et la mémoire.

### <a name="proactive-cpu-monitoring"></a>La surveillance proactive du processeur

La surveillance proactive du processeur vous fournit un moyen simple et proactive pour effectuer une action quand votre processus d’application ou enfant pour votre application consomme des ressources processeur. Vous pouvez définir vos propres règles de seuil processeur temporairement atténuer un problème de processeur élevé jusqu'à trouve la véritable cause du problème inattendu.

![La surveillance proactive du processeur](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>La réparation automatique proactive

Comme la surveillance proactive du processeur, la réparation automatique proactive offre une approche simple et proactive de limitation d’un comportement inattendu de votre application. Vous pouvez définir vos propres règles basées sur le nombre de demandes, lenteur des demandes, limite de mémoire et code d’état HTTP pour déclencher des actions de prévention. Cet outil peut être utilisé pour atténuer temporairement un comportement inattendu jusqu'à trouve la véritable cause du problème. Pour plus d’informations sur la réparation automatique proactive, visitez [annonce le nouveau automatique réparation expérience dans les diagnostics de service d’application](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![La réparation automatique proactive](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>Changer d’analyse

Dans un environnement de développement rapides et, parfois, il peut être difficile d’effectuer le suivi de toutes les modifications apportées à votre application et de laisser pinpoint seul sur une modification qui a provoqué un comportement non intègre. Analyse des modifications peut vous aider à affiner les modifications apportées à votre application pour faciliter la résolution de problèmes d’expérience. Analyse des modifications est incorporé dans un rapport de diagnostic, tel que **Application subit un incident** afin de pouvoir l’utiliser en même temps que d’autres mesures.

![Modifier la page par défaut analysis](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Vue de comparaison](./media/app-service-diagnostics/diff-view-12.png)

Analyse des modifications doit être activée avant d’utiliser la fonctionnalité. Pour plus d’informations sur l’analyse de la modification, visitez [annonce de la nouvelle expérience d’analyse de modification dans les Diagnostics de Service d’application](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).