---
title: Protection de vos machines et de vos applications dans Azure Security Center | Microsoft Docs
description: Ce document traite des recommandations de Security Center qui peuvent vous aider à protéger vos machines virtuelles, vos ordinateurs, vos applications web et vos environnements App Service.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1692e111d48a6e4574b2b114c0de84d9bc9f3203
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299829"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Protection de vos machines et de vos applications dans Azure Security Center
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires. Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles et ordinateurs, applications, mise en réseau, SQL et Identité et accès.

Cet article traite des recommandations qui s’appliquent aux machines et applications.

## <a name="monitoring-security-health"></a>Surveillance de l’intégrité de la sécurité
Vous pouvez surveiller l’état de sécurité de vos ressources dans le tableau de bord **Security Center - Vue d’ensemble**. La section **Ressources** indique le nombre de problèmes identifiés et l’état de sécurité de chaque type de ressource.

Vous pouvez consulter une liste de tous les problèmes en sélectionnant **Recommandations**. Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

Pour obtenir la liste complète des recommandations Compute et App services, consultez [Recommandations](security-center-virtual-machine-recommendations.md).

Pour continuer, sélectionnez **Compute et applications** sous **Ressources**, ou dans le menu principal de Security Center.
![Tableau de bord Security Center][1]

## <a name="monitor-compute-and-app-services"></a>Surveiller Compute et App Services
Sous **Compute**, vous trouverez quatre onglets :

- **Vue d’ensemble** : la surveillance et les recommandations identifiées par Security Center.
- **Machines virtuelles et ordinateurs** : liste de vos machines virtuelles et ordinateurs, et état actuel de leur sécurité.
- **Services cloud** : liste des rôles web et de travail contrôlés par Security Center.
- **App Services (préversion)**: liste de vos environnements App Service et état actuel de leur sécurité.
Pour continuer, sélectionnez **Compute et applications** sous **Ressources**, ou dans le menu principal de Security Center.

![Calcul][2]

Chaque onglet peut contenir plusieurs sections, et dans chaque section vous pouvez sélectionner une option afin d’afficher plus de détails sur les étapes recommandées pour résoudre le problème en question.

### <a name="monitoring-recommendations"></a>Recommandations concernant la surveillance
Cette section indique le nombre total de machines virtuelles et d’ordinateurs qui ont été initialisés pour l’approvisionnement automatique et leurs états actuels. Cet exemple comprend une recommandation, **Problèmes d’intégrité de l’agent d’analyse**. Sélectionnez cette recommandation.

![Problèmes d’intégrité de l’agent de surveillance][3]

**Problèmes d’intégrité de l’agent de surveillance** s’ouvre. Les machines virtuelles et les ordinateurs que le Security Center ne peut pas surveiller correctement s’affichent ici. Pour plus d’informations, sélectionnez une machine virtuelle ou un ordinateur. **MONITORING STATE** fournit une raison pour laquelle Security Center ne peut pas surveiller. Consultez le [guide de dépannage de Security Center](security-center-troubleshooting-guide.md) pour obtenir une liste de valeurs, de descriptions et d’étapes de résolution de **MONITORING STATE**.

### Machines virtuelles et ordinateurs non surveillés <a name="unmonitored-vms-and-computers"></a>
Une machine virtuelle ou un ordinateur n’est pas surveillé par Security Center si la machine n’exécute pas l’extension Microsoft Monitoring Agent. Un agent local peut être déjà installé sur une machine, par exemple l’agent direct OMS ou l’agent SCOM. Les machines sur lesquelles sont installés ces agents sont considérées comme non surveillées, car ces agents ne sont pas entièrement pris en charge par Security Center. Pour tirer pleinement parti de toutes les fonctionnalités de Security Center, vous devez utiliser l’extension Microsoft Monitoring Agent.

Vous pouvez installer l’extension sur la machine virtuelle ou l’ordinateur non surveillé, en plus de l’agent local déjà installé. Configurez les deux agents de la même manière en les connectant au même espace de travail. Ainsi, Security Center pourra interagir avec l’extension Microsoft Monitoring Agent et collecter des données. Consultez la section [Activer l’extension de machine virtuelle](../log-analytics/log-analytics-quick-collect-azurevm.md) pour obtenir des instructions sur la façon d’installer l’extension Microsoft Monitoring Agent.

Consultez la section [Problèmes d’intégrité de l’agent de surveillance](security-center-troubleshooting-guide.md#mon-agent) pour en savoir plus sur la raison pour laquelle Security Center ne peut pas surveiller correctement les machines virtuelles et ordinateurs initialisés pour l’approvisionnement automatique.

### <a name="recommendations"></a>Recommandations
Cette section contient un ensemble de recommandations pour chaque machine virtuelle et ordinateur, les rôles web et de travail, les applications web Azure App Service et l’environnement Azure App Service surveillés par Security Center. La première colonne indique la recommandation. La deuxième colonne précise le nombre total de ressources qui sont concernées par cette recommandation. La troisième colonne indique la gravité du problème, comme illustré dans la capture d’écran suivante :

![Recommandations][4]

Chaque recommandation dispose d’un ensemble d’actions pouvant être effectuées après les avoir sélectionnées. Par exemple, si vous sélectionnez **Mises à jour système manquantes**, le nombre de machines virtuelles et d’ordinateurs ayant des correctifs manquants et la gravité de la mise à jour manquante apparaissent, comme illustré dans la capture d’écran suivante :

![Appliquer des mises à jour système][5]

**Appliquer les mises à jour système** contient un résumé des mises à jour critiques dans un format graphique, l’un pour Windows et l’autre pour Linux. La deuxième partie contient un tableau avec les informations suivantes :

- **NOM** : nom de la mise à jour manquante.
- **NO. DE MACHINES VIRTUELLES ET D’ORDINATEURS** : nombre total de machines virtuelles et d’ordinateurs sur lesquels cette mise à jour n’a pas été installée.
- **GRAVITÉ DE LA MISE À JOUR** : donne le niveau de gravité de chaque recommandation :

    - **Critique** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
    - **Importante** : certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
    - **Modérée** : existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.


- **ÉTAT**: état actuel de la recommandation :

    - **Ouverte** : la recommandation n’a pas encore été prise en compte.
    - **En cours** : la recommandation est actuellement appliquée à ces ressources ; aucune action de votre part n’est nécessaire.
    - **Résolue** : la recommandation a déjà été achevée. (Une fois problème résolu, l’entrée a été grisée).

Pour afficher des informations détaillées sur les recommandations, cliquez sur le nom de la mise à jour manquante dans la liste.

![Détails de la recommandation][6]

> [!NOTE]
> Les recommandations de sécurité ci-dessus sont les mêmes que celles figurant sous la mosaïque **Recommandations**. Pour plus d’informations sur la résolution des recommandations, consultez l’article [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Machines virtuelles et ordinateurs
La section Machines virtuelles et ordinateurs vous fournit une vue d’ensemble de toutes les recommandations relatives aux machines virtuelles et aux ordinateurs. Chaque colonne représente un ensemble de recommandations, comme illustré dans la capture d’écran suivante :

![Recommandations relatives aux machines virtuelles et aux ordinateurs][7]

Cette liste contient quatre types d’icônes :

![Ordinateur extérieur à Azure][8] Ordinateur ne relevant pas d’Azure.

![Machine virtuelle Azure Resource Manager][9] Machine virtuelle Azure Resource Manager.

![Machine virtuelle Azure Classic][10] Machine virtuelle Azure Classic.

![Machines virtuelles identifiées à partir de l’espace de travail][11] Machines virtuelles identifiées uniquement à partir de l’espace de travail qui fait partie de l’abonnement affiché. Cela inclut les machines virtuelles d’autres abonnements qui se rapportent à l’espace de travail associé à cet abonnement, ainsi que les machines virtuelles qui ont été installées avec l’agent SCOM Direct et qui ne possèdent pas d’ID de ressource.

L’icône qui s’affiche sous chaque recommandation vous aide à identifier rapidement la machine virtuelle et l’ordinateur qui requièrent votre attention, ainsi que le type de recommandation. Vous pouvez également utiliser l’option Filtre pour sélectionner les options qui s’afficheront sur cet écran.

![Filtrer][12]

Dans l’exemple précédent, une machine virtuelle a une recommandation critique concernant la protection de point de terminaison. Sélectionnez la machine virtuelle pour obtenir plus d’informations :

![Recommandation critique][13]

Ce panneau comporte des informations détaillées sur la sécurité de la machine virtuelle ou de l’ordinateur. L’action recommandée et le niveau de gravité de chaque problème sont affichés en bas.

### <a name="cloud-services"></a>Services cloud
Pour les services cloud, une recommandation est créée lorsque la version du système d’exploitation est obsolète, comme illustré dans la capture d’écran suivante :

![Services cloud][14]

Si vous avez une recommandation (ce qui n’est pas le cas dans l’exemple précédent), vous devez suivre les étapes de la recommandation pour mettre à jour la version du système d’exploitation. Lorsqu’une mise à jour est disponible, vous recevez une alerte (rouge ou orange en fonction de la gravité du problème). Lorsque vous sélectionnez cette alerte dans les lignes WebRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS) ou WorkerRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS), vous obtenez des informations détaillées sur cette recommandation, comme illustré dans la capture d’écran suivante :

![WorkerRole1][15]

Pour voir une explication plus normative concernant cette recommandation, cliquez sur **Mettre à jour la version du système d’exploitation** sous la colonne **DESCRIPTION**.

![Mettre à jour la version du système d’exploitation][16]

### <a name="app-services-preview"></a>App Services (préversion)

> [!NOTE]
> La surveillance d’App Service est uniquement disponible en préversion, au niveau Standard du Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>
>

Sous **App Services**, vous trouverez la liste de vos environnements App Service et le résumé de l'intégrité basé sur l’évaluation effectuée par Security Center.

![App Services][17]

Cette liste contient trois types d’icônes :

![Environnement App Services][18] Environnement App Services.

![Application web][19] Application web.

![Application de fonction][24] Application de fonction.

1. Sélectionnez une application web. Une vue récapitulative s’ouvre avec trois onglets :

  - **Recommandations** : basées sur les évaluations effectuées par Security Center et qui ont échoué.
  - **Évaluations passées** : liste des évaluations effectuées par Security Center et qui ont réussi.
  - **Évaluations non disponibles** : liste des évaluations qui n’ont pas été exécutées en raison d’une erreur ou la recommandation n’est pas pertinente pour l’App Service en question.

  Sous **Recommandations** se trouve la liste des recommandations pour l’application web sélectionnée, avec le niveau de gravité pour chaque recommandation.

  ![Vue Summary (Résumé)][20]

2. Sélectionnez une recommandation pour obtenir une description de la recommandation et la liste des ressources défectueuses, saines et non analysées.

  ![Description de la recommandation][21]

  Sous **Évaluations passées** se trouve une liste d’évaluations réussies.  L’état de gravité de ces évaluations est toujours vert.

  ![Évaluations réussies][22]

3. Sélectionnez une évaluation réussie dans la liste pour obtenir une description de l’évaluation et la liste des ressources défectueuses, saines et non analysées. Il existe un onglet pour les ressources défectueuses, mais cette liste est toujours vide car l’évaluation a réussi.

    ![Ressources saines][23]



## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :


* [Comprendre les recommandations d’Azure Security Center pour les machines virtuelles](security-center-virtual-machine-recommendations.md)
* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
