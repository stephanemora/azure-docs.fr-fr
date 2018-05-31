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
ms.openlocfilehash: 08991565d56ffbf7d798944f108a1b86e4463c58
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32176284"
---
# <a name="monitoring-usage-and-estimated-costs"></a>Surveillance de l’utilisation et de l’estimation des coûts

Dans le hub Monitor du portail Azure, la page **Utilisation et estimation des coûts** explique l’utilisation des principales fonctionnalités de surveillance comme les [alertes, mesures, notifications](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) et [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Pour les clients ayant souscrit aux plans de tarification avant avril 2018, cela inclut également l’utilisation de Log Analytics acheté via l’offre Insights et Analytics.

Sur cette page, les utilisateurs peuvent afficher l’utilisation de leurs ressources au cours des 31 derniers jours, agrégée par abonnement. Les options d’exploration vous permettent de voir les tendances au cours de cette période de 31 jours. De nombreuses données doivent être rassemblées pour fournir cette estimation. Vous devez donc attendre patiemment le chargement de la page.

Cet exemple illustre l’utilisation de la surveillance et l’estimation des coûts associés :

![Capture d’écran du portail d’utilisation et d’estimation des coûts](./media/monitoring-usage-and-estimated-costs/001.png)

Sélectionnez le lien dans la colonne d’utilisation mensuelle pour ouvrir un graphique illustrant les tendances d’utilisation au cours des 31 derniers jours :

![Capture d’écran du graphique en barres « Inclus par nœud »](./media/monitoring-usage-and-estimated-costs/002.png)

Voici un autre exemple similaire d’utilisation et de résumé des coûts. Cet exemple montre un abonnement dans le nouveau modèle de tarification basée sur l’utilisation d’avril 2018. Notez l’absence de facturation en fonction des nœuds. L’ingestion et la rétention des données pour Log Analytics et Application Insights sont maintenant répertoriées dans un nouveau compteur commun.

![Capture d’écran du portail - Utilisation et estimation des coûts - Tarification avril 2018](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>Nouveau modèle de tarification

Un [nouveau modèle tarifaire de surveillance a été publié](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) en avril 2018.  Il inclut une tarification en fonction de la consommation, adaptée au cloud. Vous payez uniquement ce que vous utilisez, sans engagement par nœud. Les détails du nouveau modèle de tarification sont disponibles pour [alertes, mesures, notifications](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) et [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Pour les clients ayant intégré Log Analytics ou Application Insights après le 2 avril 2018, le nouveau modèle de tarification est la seule option. Pour les clients qui ont déjà utilisé ces services, la transition vers le nouveau modèle de tarification est facultative.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Évaluation de l’impact du nouveau modèle de tarification
Le nouveau modèle de tarification aura un impact différent sur chaque client en fonction des modèles d’utilisation de la surveillance. Pour les clients qui utilisaient Log Analytics ou Application Insights avant le 2 avril 2018, la page **Utilisation et estimation des coûts** dans Azure Monitor estime les modifications de coûts en cas de transition vers le nouveau modèle de tarification. Elle propose une méthode pour effectuer la transition d’un abonnement vers le nouveau modèle. Pour la plupart des clients, le nouveau modèle de tarification sera avantageux. Pour les clients utilisant généralement une grande quantité de données ou se trouvant dans des régions avec des coûts plus élevés, cela peut ne pas être le cas.

Pour afficher l’estimation des coûts des abonnements que vous avez choisis sur la page **Utilisation et estimation des coûts**, cliquez sur la bannière bleue en haut de la page. Il est préférable de procéder abonnement par abonnement, car c’est à ce niveau que le nouveau modèle de tarification peut être adopté.

![Capture d’écran de l’utilisation de la surveillance et de l’estimation des coûts dans le nouveau modèle de tarification](./media/monitoring-usage-and-estimated-costs/004.png)

La nouvelle page présente une version identique de la page précédente avec une bannière verte :

![Capture d’écran de l’utilisation de la surveillance et de l’estimation des coûts dans le modèle de tarification actuel](./media/monitoring-usage-and-estimated-costs/005.png)

La page affiche également un autre ensemble de compteurs qui correspondent au nouveau modèle de tarification. Cette liste est un exemple :

- Insight & Analytics\Dépassement par nœud
- Insight & Analytics\Inclus par nœud
- Application Insights\Données de dépassement de base
- Application Insights\Données incluses

Le nouveau modèle de tarification n’a pas d’allocations de données incluses en fonction des nœuds. Par conséquent, ces compteurs d’ingestion des données sont combinés dans un nouveau compteur d’ingestion des données commun appelé **Services partagés\Ingestion des données**. 

Une autre modification est apportée aux données ingérées dans Log Analytics ou Application Insights dans des régions avec des coûts plus élevés. Les données de ces régions avec des coûts élevés seront affichées dans les nouveaux compteurs régionaux. Un exemple est **Data Ingestion (US West Central)** (Ingestion des données (États-Unis Centre-Ouest)).

> [!NOTE]
> L’estimation des coûts par abonnement ne tient pas compte des droits par nœud au niveau du compte de l’abonnement Operations Management Suite (OMS). Pour une explication plus détaillée du nouveau modèle de tarification dans ce scénario, consultez votre responsable de compte.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Nouveau modèle de tarification et droits applicables à l’abonnement à Operations Management Suite

Les clients ayant acheté Microsoft Operations Management Suite E1 et E2 peuvent bénéficier des droits d’ingestion des données par nœud pour [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) et [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing#the-price-plans). Pour que ces droits applicables aux espaces de travail Log Analytics ou aux ressources Application Insights puissent être reçus dans un abonnement donné : 

- Le modèle tarifaire de cet abonnement doit rester dans le modèle tarifaire antérieur à avril 2018.
- Les espaces de travail Log Analytics doivent utiliser le niveau tarifaire « Par nœud (OMS) ».
- Les ressources Application Insights doivent utiliser le plan tarifaire « Entreprise ».

En fonction du nombre de nœuds de la suite que votre organisation a achetés, le transfert de certains abonnements vers le nouveau modèle tarifaire pourrait se révéler avantageux, mais cette approche doit faire l’objet d’une étude approfondie. En règle générale, il convient de rester simplement dans le modèle antérieur à avril 2018 comme décrit ci-dessus.

> [!WARNING]
> Si votre organisation a acheté Microsoft Operations Management Suite E1 et E2, il est généralement préférable de conserver vos abonnements dans le modèle tarifaire antérieur à avril 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Modifications lorsque vous passez au nouveau modèle de tarification

Le nouveau modèle tarifaire ramène les options tarifaires Log Analytics et Application Insights à un seul niveau (ou plan). Le transfert d’un abonnement vers le nouveau modèle tarifaire :

- Remplace le niveau tarifaire de chaque abonnement à Log Analytics par un nouveau niveau Par Go (appelé « pergb2018 » dans Azure Resource Manager)
- Associe le plan De base à toute ressource Application Insights figurant dans le plan Entreprise

L’estimation des coûts montre les effets de ces modifications.

> [!WARNING]
> Remarque importante : Si vous utilisez Azure Resource Manager ou PowerShell pour déployer [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) ou [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) dans un abonnement, vous êtes passé au nouveau modèle tarifaire. Si vous spécifiez un plan/niveau tarifaire autre que « pergb2018 » pour Log Analytics ou « De base » pour Application Insights, l’opération réussit, **mais elle utilise uniquement le plan/niveau tarifaire valide** au lieu de se solder par un échec en raison de la spécification d’un plan/niveau tarifaire non valide. 
>

## <a name="moving-to-the-new-pricing-model"></a>Transition vers le nouveau modèle de tarification

Si vous avez décidé d’adopter le nouveau modèle de tarification pour un abonnement, sélectionnez l’option **Sélection d’un modèle de tarification** en haut de la page **Utilisation et estimation des coûts** :

![Capture d’écran de l’utilisation de la surveillance et de l’estimation des coûts dans le nouveau modèle de tarification](./media/monitoring-usage-and-estimated-costs/006.png)

La page **Sélection d’un modèle de tarification** s’ouvre. Elle affiche la liste de tous les abonnements que vous avez consultés sur la page précédente :

![Capture d’écran de sélection d’un modèle de tarification](./media/monitoring-usage-and-estimated-costs/007.png)

Pour basculer un abonnement vers le nouveau modèle de tarification, il vous suffit de cocher la case et de sélectionner **Enregistrer**. Vous pouvez revenir à l’ancien modèle de tarification de la même façon. Notez que les autorisations du propriétaire ou du contributeur de l’abonnement sont requises pour changer le modèle de tarification.
