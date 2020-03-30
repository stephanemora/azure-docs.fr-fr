---
title: Protéger vos machines et applications
description: Ce document traite des recommandations de Security Center qui peuvent vous aider à protéger vos machines virtuelles, vos ordinateurs, vos applications web et vos environnements App Service.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79236749"
---
# <a name="protect-your-machines-and-applications"></a>Protéger vos machines et applications
Quand Azure Security Center identifie des failles de sécurité potentielles, des suggestions sont émises pour vous guider tout au long du processus de configuration des contrôles nécessaires afin de renforcer et protéger vos ressources.

Cet article explique la page **Calcul et applications** de la section sur la sécurité des ressources de Security Center.

Pour obtenir la liste complète des recommandations que vous pouvez voir dans cette page, consultez [Recommandations relatives au calcul et aux applications](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Voir la sécurité de vos ressources de calcul et d’applications

[![Tableau de bord de Security Center](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Pour voir l’état de vos ressources de calcul et d’applications, dans le volet gauche de Security Center, sélectionnez **Calcul et applications**. Les onglets suivants sont disponibles :

* **Vue d’ensemble** : liste les recommandations pour toutes les ressources de calcul et d’applications, ainsi que leur état de sécurité actuel 

* [**Machines virtuelles et serveurs**](#vms-and-computers) : liste les recommandations pour vos machines virtuelles et ordinateurs, ainsi que l’état de sécurité actuel de chacun d’eux

* [**VM Scale Sets**](#vmscale-sets) : liste les recommandations pour vos groupes de machines virtuelles identiques 

* [**Services cloud**](#cloud-services) : liste les recommandations pour vos rôles web et de travail supervisés par Security Center

* [**App services**](#app-services) : liste les recommandations pour vos environnements App Service et l’état de sécurité actuel de chacun d’eux

* [**Conteneurs**](#containers) : liste les recommandations pour vos conteneurs et l’évaluation de sécurité de leur configuration

* **Ressources de calcul** : liste les recommandations pour vos ressources de calcul, comme des clusters Service Fabric et des hubs d’événements

### <a name="whats-in-each-tab"></a>Que contient chaque onglet ?

Chaque onglet comporte plusieurs sections, et dans chaque section, vous pouvez accéder à des détails supplémentaires sur l’élément affiché.

Sous chaque onglet, des recommandations pour les ressources pertinentes dans votre environnement supervisé sont également affichées. La première colonne liste la recommandation, la deuxième indique le nombre total de ressources affectées et la troisième indique la gravité du problème.

Chaque recommandation dispose d’un ensemble d’actions pouvant être effectuées après les avoir sélectionnées. Par exemple, si vous sélectionnez **Mises à jour système manquantes**, le nombre de machines virtuelles et d’ordinateurs ayant des correctifs manquants et la gravité de la mise à jour manquante apparaissent.

> [!NOTE]
> Les recommandations de sécurité sont les mêmes que celles de la page **Recommandations**, mais ici, elles sont filtrées selon le type de ressource spécifique que vous avez sélectionné. Pour plus d’informations sur l’application de recommandations, consultez l’article [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Machines virtuelles et serveurs
La section Machines virtuelles et ordinateurs vous fournit une vue d’ensemble de toutes les recommandations de sécurité pour vos machines virtuelles et ordinateurs. Quatre types d’ordinateurs sont inclus :

![Ordinateur extérieur à Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Ordinateur ne relevant pas d’Azure.

![Machine virtuelle Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Machine virtuelle Azure Resource Manager.

![Machine virtuelle Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Machine virtuelle Azure Classic.

![Machines virtuelles identifiées à partir de l’espace de travail](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Machines virtuelles identifiées uniquement à partir de l’espace de travail qui fait partie de l’abonnement affiché. Cela inclut les machines virtuelles d’autres abonnements qui dépendent de l’espace de travail dans cet abonnement, et les machines virtuelles qui ont été installées avec l’agent direct Operations Manager et qui n’ont pas d’ID de ressource.

L’icône qui s’affiche sous chaque recommandation vous aide à identifier rapidement la machine virtuelle et l’ordinateur qui requièrent votre attention, ainsi que le type de recommandation. Vous pouvez également utiliser les filtres pour rechercher dans la liste par **Type de ressource** et **Gravité**.

Pour explorer au niveau du détail les suggestions de sécurité pour chaque machine virtuelle, cliquez sur celle-ci.
Ce panneau comporte des informations détaillées sur la sécurité de la machine virtuelle ou de l’ordinateur. L’action recommandée et le niveau de gravité de chaque problème sont affichés en bas.

[![Services cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Groupes de machines virtuelles identiques
Security Center détecte automatiquement si vous avez des groupes identiques et vous recommande d’y installer Microsoft Monitoring Agent.

Pour installer Microsoft Monitoring Agent : 

1. Sélectionnez la suggestion **Installer l’agent de surveillance sur les groupes de machines virtuelles identiques**. Vous obtenez la liste des jeux identiques non contrôlés.

1. Sélectionnez un groupe identique défectueux. Suivez les instructions pour installer l’agent de surveillance à l’aide d’un espace de travail rempli existant, ou créez-en un. Veillez à définir l’espace de travail [niveau tarifaire](security-center-pricing.md) s’il ne l’est pas.

   ![Installer MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Pour définir de nouveaux groupes identiques afin d’installer automatiquement Microsoft Monitoring Agent :
1. Accédez à Azure Policy et cliquez sur **Définitions**.

1. Recherchez la stratégie **Déployer un agent Log Analytics pour des groupes de machines virtuelles identiques Windows**, puis cliquez sur celle-ci.

1. Cliquez sur **Affecter**.

1. Définir l’**Étendue** et l’**Espace de travail Log Analytics**, puis cliquez sur **Assigner**.

Si vous souhaitez paramétrer tous les groupes identiques existants pour installer Microsoft Monitoring Agent, dans Azure Policy, accédez à **Correction**, puis appliquez la stratégie existante aux groupes identiques existants.





### <a name="cloud-services"></a><a name="cloud-services"></a>Services cloud
Pour les services cloud, une suggestion est créée lorsque la version du système d’exploitation est obsolète.

![Services cloud](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Si vous avez une recommandation, suivez les étapes mentionnées de la recommandation permettant de mettre à jour le système d’exploitation. Quand une mise à jour est disponible, vous recevez une alerte (rouge ou orange, en fonction de la gravité du problème). Pour obtenir une explication complète de cette recommandation, cliquez sur **Mettre à jour la version du système d’exploitation** sous la colonne **DESCRIPTION**.






### <a name="app-services"></a><a name="app-services"></a>App Services
Pour voir les informations App Service, vous devez être au niveau tarifaire Standard de Security Center et activer App Service dans votre abonnement. Pour obtenir des instructions sur l’activation de cette fonctionnalité, consultez [Protéger App Service avec Azure Security Center](security-center-app-services.md).

Sous **App Services**, vous trouverez la liste de vos environnements App Service et le résumé de l'intégrité basé sur l’évaluation effectuée par Security Center.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Trois types de services d’application sont affichés :

![Environnement App Services](./media/security-center-virtual-machine-recommendations/ase.png) Environnement App Services

![Application web](./media/security-center-virtual-machine-recommendations/web-app.png) Application web

![Application de fonction](./media/security-center-virtual-machine-recommendations/function-app.png) Application de fonction

Si vous sélectionnez une application web, une vue récapitulative s’ouvre avec trois onglets :

   - **Recommandations** : basées sur les évaluations effectuées par Security Center et qui ont échoué.
   - **Évaluations passées** : liste des évaluations effectuées par Security Center et qui ont réussi.
   - **Évaluations non disponibles** : liste des évaluations qui n’ont pas été exécutées en raison d’une erreur ou la recommandation n’est pas pertinente pour l’App Service en question.

   Sous **Recommandations** se trouve la liste des recommandations pour l’application web sélectionnée, avec le niveau de gravité pour chaque recommandation.

   ![Recommandations App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Sélectionnez une suggestion pour voir sa description et la liste des ressources défectueuses, saines et non analysées.

   - La colonne **Évaluations passées** montre la liste des évaluations réussies. L’état de gravité de ces évaluations est toujours vert.

   - Sélectionnez une évaluation réussie dans la liste pour obtenir une description de l’évaluation et la liste des ressources défectueuses, saines et non analysées. Il existe un onglet pour les ressources défectueuses, mais cette liste est toujours vide car l’évaluation a réussi.





### <a name="containers"></a><a name="containers"></a>Conteneurs

Lorsque vous ouvrez l’onglet **Conteneurs**, selon votre environnement, vous pouvez voir l’un des trois types de ressources suivants :

![Hôte de conteneur](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Hôtes de conteneur - Machines virtuelles exécutant Docker 

![Service Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Clusters Azure Kubernetes Service (AKS)

![Registre de conteneurs](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registres ACR (Azure Container Registry) : ils s’affichent uniquement si vous êtes dans le niveau tarifaire Standard et si le bundle Azure Container Registry est activé.

Pour obtenir des instructions sur l’utilisation des fonctionnalités de sécurité des conteneurs, consultez [Superviser la sécurité de vos conteneurs](monitor-container-security.md).

Les avantages du bundle Azure Container Registry sont expliqués [ici](azure-container-registry-integration.md)

Les avantages du bundle Kubernetes Services sont expliqués [ici](azure-kubernetes-service-integration.md)

[![Onglet Conteneurs](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Pour voir les recommandations relatives à une ressource spécifique dans la liste, cliquez sur cette ressource.

#### <a name="visibility-into-container-registries"></a>Visibilité dans les registres de conteneurs

Par exemple, si vous cliquez sur le registre ACR asc-demo dans la liste de la capture d’écran ci-dessus, vous obtenez cette page de détails :

[![Recommandations pour un registre ACR spécifique](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Visibilité dans les conteneurs hébergés sur des machines IaaS Linux

Lorsque vous cliquez sur l’une des machines virtuelles exécutant Docker, vous voyez s’afficher une page de détails relatifs aux conteneurs sur la machine, tels que la version de Docker et le nombre d’images exécutées sur l’hôte.

![Recommandations pour une machine virtuelle exécutant Docker](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Recommandations de sécurité issues du test d’évaluation CIS pour Docker

Security Center analyse vos configurations Docker afin d’en détecter les erreurs en fournissant la liste de toutes les règles ayant échoué qui ont été évaluées. Security Center fournit des instructions pour vous aider à résoudre ces problèmes rapidement sans perdre de temps. Security Center évalue continuellement les configurations Docker pour vous indiquer leur état le plus récent.

![onglet Conteneur](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les articles suivants :

* [Liste complète des recommandations en matière de sécurité d’Azure Security Center](recommendations-reference.md)
* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)