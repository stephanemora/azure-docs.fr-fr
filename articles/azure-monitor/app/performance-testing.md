---
title: Test des performances et de charge avec Azure Application Insights | Microsoft Docs
description: Configurer des tests de performances et de charge avec Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669860"
---
# <a name="performance-testing"></a>Tests de performances

> [!NOTE]
> Le service de test de charge basé sur le cloud a été déprécié. Vous pouvez trouver plus d’informations sur la dépréciation, la disponibilité du service et d’autres services [ici](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights vous permet de générer des tests de charge pour vos sites web. Comme pour les [tests de disponibilité](monitor-web-app-availability.md), vous pouvez envoyer des requêtes de base ou des [requêtes à plusieurs étapes](availability-multistep.md) à partir d’agents de test Azure dans le monde. Les tests de performances vous permettent de simuler jusqu’à 20 000 utilisateurs simultanés pendant 60 minutes maximum.

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure

Pour créer un test de performances, vous devez tout d’abord créer une ressource Application Insights. Si vous en avez déjà créé une, passez à la section suivante.

Dans le portail Azure, sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights** et créez une ressource Application Insights.

## <a name="configure-performance-testing"></a>Configurer un test de performances

S’il s’agit de votre première fois, sélectionnez **Définir l’organisation** et choisissez une organisation Azure DevOps comme source de vos tests de performances.

Sous **Configurer**, accédez à **Tests de performances**, puis cliquez sur **Nouveau** pour créer un test.

![Fill at least the URL of your website](./media/performance-testing/new-performance-test.png)

Pour créer un test de performances de base, sélectionnez un type de test **Test manuel**, et renseignez les paramètres souhaités pour votre test.

|Paramètre| Valeur maximale
|----------|------------|
| Charge utilisateur | 20 000 |
| Durée (en minutes)  | 60 |  

Une fois votre test créé, cliquez sur **Exécuter le test**.

Une fois le test terminé, vous verrez des résultats similaires à ce qui suit :

![Résultats du test](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Configurer un test web Visual Studio

Les capacités de test de performances avancées d’Application Insights sont conçues en plus des projets de test de performances et de charge de Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Étapes suivantes

* [Tests web à plusieurs étapes](availability-multistep.md)
* [Tests ping d’URL](monitor-web-app-availability.md)
