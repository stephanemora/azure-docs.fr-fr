---
title: Surveiller l’utilisation et l’estimation des coûts dans Azure Monitor | Microsoft Docs
description: Vue d’ensemble du processus d’utilisation de la page d’utilisation et d’estimation des coûts d’Azure Monitor
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: ce295c449b01de4fa99df9198805a6b0727c0d18
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>Surveillance de l’utilisation et de l’estimation des coûts

Dans le hub Monitor du portail Azure, la page **Utilisation et estimation des coûts** facilite la compréhension de l’utilisation des principales fonctionnalités de surveillance comme [alertes, mesures, notifications](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) et [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pour les clients ayant souscrit aux plans de tarification avant avril 2018, cela inclut également l’utilisation de Log Analytics acheté via l’offre Insights & Analytics.

Sur cette page, les utilisateurs peuvent consulter l’utilisation de ces ressources au cours des 31 derniers jours, agrégée par abonnement, avec des options d’exploration pour voir la tendance d’utilisation au cours de cette période. Pour rassembler ces informations et faire cette estimation, il faut beaucoup de données. Vous devez donc attendre patiemment le chargement de la page.
L’exemple suivant illustre la surveillance de l’utilisation et l’estimation des coûts consécutifs :

![Capture d’écran du portail d’utilisation et d’estimation des coûts](./media/monitoring-usage-and-estimated-costs/001.png)

Lorsque vous cliquez sur le lien dans la colonne d’utilisation mensuelle, un graphique illustrant les tendances d’utilisation au cours des 31 derniers jours s’ouvre :

![Capture d’écran Inclus par nœud 671,47 Go](./media/monitoring-usage-and-estimated-costs/002.png)

Voici une synthèse similaire de l’utilisation et des coûts, concernant un abonnement basé sur le nouveau modèle de tarification en fonction de la consommation d’avril 2018. Notez l’absence de tarification par nœud. Par ailleurs, l’ingestion et la rétention des données pour Log Analytics et Application Insights sont maintenant répertoriées dans un nouveau compteur commun.

![Capture d’écran du portail d’utilisation et d’estimation des coûts](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nouveau modèle de tarification

Un nouveau modèle de tarification de surveillance a été publié en avril 2018.  Il inclut une tarification en fonction de la consommation, adaptée au cloud. Vous payez uniquement ce que vous utilisez, sans engagement par nœud. Les détails du nouveau modèle de tarification sont disponibles pour [alertes, mesures, notifications](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) et [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

Pour les clients ayant intégré Log Analytics ou Application Insights après le 2 avril 2018, le nouveau modèle de tarification est la seule option. Pour les clients ayant déjà utilisé ces services, la transition vers le nouveau modèle de tarification est facultative.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Évaluation de l’impact du nouveau modèle de tarification

Le nouveau modèle de tarification aura un impact différent le mode de surveillance de l’utilisation choisi par chaque client. Pour les clients qui utilisaient déjà Log Analytics ou Application Insights avant le 2 avril 2018, la page **Utilisation et estimation des coûts** dans Azure Monitor permet d’estimer les modifications de coût si vous basculez vers le nouveau modèle de tarification et fournit une solution pour déplacer un abonnement dans le nouveau modèle. Pour la plupart des clients, le nouveau modèle de tarification est avantageux,ce qui n’est pas forcément le cas des clients utilisant généralement une grande quantité de données ou se trouvant dans des régions à coût plus élevé.

Pour afficher l’estimation des coûts des abonnements que vous avez sélectionnés sur la page **Utilisation et estimation des coûts**, cliquez sur la bannière bleue en haut de la page. Il est préférable de procéder abonnement par abonnement, car c’est à ce niveau que le nouveau modèle de tarification peut être adopté.

![Capture d’écran de sélection du modèle de tarification](./media/monitoring-usage-and-estimated-costs/004.png)

Vous voyez maintenant une version similaire de cette page avec une bannière verte :

![Capture d’écran de sélection du modèle de tarification](./media/monitoring-usage-and-estimated-costs/005.png)

Ici, vous voyez un autre ensemble de compteurs : les compteurs qui correspondent au nouveau modèle de tarification. Par exemple, les compteurs d’ingestion des données, tels que

1. Insight & Analytics\Dépassement par nœud
2. Insight & Analytics\Inclus par nœud
3. Application Insights\Données de dépassement de base
4. Application Insights\Données incluses

sont combinés dans un nouveau compteur d’ingestion des données nommé **Services partagés\Ingestion des données**, car le nouveau modèle de tarification ne comporte pas de droits relatifs aux données incluses par nœud.

Vous constaterez également que les données ingérées dans Log Analytics ou Application Insights dans des régions aux coûts plus élevés apparaissent avec les nouveaux compteurs régionaux pour refléter correctement cette information, par exemple **Ingestion des données (États-Unis Centre-Ouest)**.

> [!NOTE]
> L’estimation des coûts par abonnement ne tient pas compte des droits par nœud de niveau compte de l’abonnement à Operations Management Suite (OMS). Pour une explication plus détaillée du nouveau modèle de tarification dans ce scénario, consultez votre responsable de compte.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nouveau modèle de tarification et droits applicables à l’abonnement à Operations Management Suite

Les clients ayant acheté Microsoft Operations Management Suite E1 et E2 peuvent bénéficier des droits d’ingestion des données par nœud pour [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) et [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans). Pour que ces droits applicables aux espaces de travail Log Analytics ou aux ressources Application Insights puissent être reçus dans un abonnement donné, le modèle de tarification de cet abonnement doit rester dans le modèle de tarification antérieur à avril 2018, dans lequel le niveau tarifaire « Par nœud (OMS) » de Log Analytics et le plan tarifaire « Entreprise » d’Application Insights sont disponibles. En fonction du nombre de nœuds de la suite que votre organisation a achetés, le déplacement de certains abonnements vers le nouveau modèle de tarification peut toujours se révéler avantageux, mais cette approche doit faire l’objet d’une étude approfondie. 

## <a name="changes-when-moving-to-the-new-pricing-model"></a>Modifications lors de la transition vers le nouveau modèle de tarification

Lorsqu’un abonnement est basculé vers le nouveau modèle de tarification, le niveau tarifaire de chaque Log Analytics est remplacé par un niveau par Go et tout est migré vers le nouveau plan (appelé « pargo2018 » dans Azure Resource Manager). Ce basculement change aussi les ressources Application Insights dans le plan Entreprise en plan De base. Les effets de ces modifications apparaissent sur l’estimation des coûts décrite ci-dessus. 

## <a name="moving-to-the-new-pricing-model"></a>Transition vers le nouveau modèle de tarification

Si vous avez décidé d’adopter le nouveau modèle de tarification pour un abonnement, cliquez sur l’option **Sélection d’un modèle de tarification** en haut de la page **Utilisation et estimation des coûts** :

![Capture d’écran de la surveillance de l’utilisation et de l’estimation des coûts dans le nouveau modèle de tarification](./media/monitoring-usage-and-estimated-costs/006.png)

Cette action ouvre la page **Sélection d’un modèle de tarification**, qui répertorie chaque abonnement consulté sur la page précédente :

![Capture d’écran de sélection d’un modèle de tarification](./media/monitoring-usage-and-estimated-costs/007.png)

Pour basculer un abonnement vers le nouveau modèle de tarification, il suffit de cocher la case et de cliquer sur **Enregistrer**.  Vous pouvez revenir à l’ancien modèle de tarification de la même façon. Notez que les autorisations du propriétaire ou du collaborateur de l’abonnement sont requises pour changer le modèle de tarification.
