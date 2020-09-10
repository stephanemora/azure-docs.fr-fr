---
title: Nous retirerons les machines virtuelles Azure Classic le 1er mars 2023
description: Cet article fournit une vue d’ensemble de haut niveau du retrait des machines virtuelles Classic
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: de590168ecf4febe3c18442a6dc6c1c2eba4dc51
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297601"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrez vos ressources IaaS vers Azure Resource Manager avant le 1er mars 2023 

En 2014, nous avons lancé IaaS sur Azure Resource Manager et nous en avons depuis amélioré les fonctionnalités. Étant donné qu’[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) intègre désormais des fonctionnalités IaaS complètes et d’autres avancées, nous avons déconseillé la gestion des machines virtuelles IaaS via [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) le 28 février 2020, et cette fonctionnalité sera entièrement supprimée le 1er mars 2023. 

Aujourd’hui, environ 90 % des machines virtuelles IaaS utilisent Azure Resource Manager. Si vous utilisez des ressources IaaS via Azure Service Manager (ASM), commencez à planifier votre migration dès maintenant, et achevez-la pour le 1er mars 2023 afin de tirer parti d’[Azure Resource Manager](../azure-resource-manager/management/index.yml).

Les machines virtuelles Classic suivent la [stratégie de cycle de vie moderne](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pour leur mise hors service.

## <a name="how-does-this-affect-me"></a>Dans quelle mesure cela m’affecte-t-il ? 

- Depuis le 28 février 2020, les clients qui n’utilisaient pas de machine virtuelle IaaS via Azure Service Manager (ASM) au mois de février 2020 ne peuvent plus créer de machines virtuelles Classic. 
- Le 1er mars 2023, les clients pourront plus démarrer des machines virtuelles IaaS à l’aide d’Azure Service Manager et celles qui seront toujours en cours d’exécution ou allouées seront arrêtées et libérées. 
- Le 1er mars 2023, les abonnements qui n’auront pas migré vers Azure Resource Manager seront informés du calendrier de suppression des machines virtuelles Classic restantes.  

Les services et fonctionnalités Azure suivants ne seront **PAS** affectés par cette mise hors service : 
- Cloud Services 
- Comptes de stockage **non** utilisés par des machines virtuelles classiques 
- Réseaux virtuels **non** utilisés par des machines virtuelles Classic. 
- Autres ressources Classic

## <a name="what-actions-should-i-take"></a>Que dois-je faire ? 

- Commencez dès aujourd’hui la planification de votre migration vers Azure Resource Manager. 

- Dressez la liste de toutes les machines virtuelles affectées. 
    - Le type de machines virtuelles « Machines virtuelles (classique) » dans le [panneau des machines virtuelles dans le portail Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) désigne toutes les machines virtuelles affectées au sein de l’abonnement. 
    - Vous pouvez également interroger Azure Resource Graph à l’aide du [portail](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) ou de [PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) pour afficher la liste de toutes les machines virtuelles marquées (classiques) et leurs informations associées pour les abonnements sélectionnés. 
    - Nous avons envoyé des e-mails aux propriétaires d’abonnements avec la liste de tous les abonnements contenant ces machines virtuelles (classiques). Utilisez-les pour dresser cette liste. 

- [Apprenez-en davantage](./windows/migration-classic-resource-manager-overview.md) sur la migration de vos machines virtuelles [Linux](./linux/migration-classic-resource-manager-plan.md) et [Windows](./windows/migration-classic-resource-manager-plan.md) Classic vers Azure Resource Manager.

- Pour plus d’informations, voir [Questions fréquemment posées sur la migration de Classic vers Azure Resource Manager](./migration-classic-resource-manager-faq.md)

- Nous vous recommandons de commencer la planification à l’aide de l’[outil de migration de prise en charge de plateforme](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) afin de migrer vos machines virtuelles existantes en 3 étapes simples : « confirmer, préparer et valider ». 
    - L’outil est conçu pour migrer vos machines virtuelles sans temps d’arrêt ou avec un temps d’arrêt minimal. 
    - La première étape, « Confirmer » n’a aucune incidence sur votre déploiement existant. Elle produit la liste de tous les scénarios non pris en charge pour la migration. 
    - Parcourez la [liste de solutions de contournement](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview#unsupported-features-and-configurations) pour corriger votre déploiement et le préparer pour la migration. 
    - Idéalement, une fois toutes les erreurs de confirmation corrigées, vous ne devriez pas rencontrer de problèmes lors des étapes de préparation et de validation. Une fois la validation réussie, votre déploiement est migré de façon dynamique vers Azure Resource Manager, et peut désormais être géré à l’aide de nouvelles API exposées par Azure Resource Manager. 
    - Si cet outil n’est pas adapté pour votre migration, vous pouvez explorer une [autre offre de calcul](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) pour cette migration. Étant donné qu’il existe de nombreuses offres de calcul Azure très différents les unes des autres, nous ne pouvons pas fournir pour cette offre de chemin de migration pris en charge par la plateforme.  
- Pour toute question technique, tout problème et l’ajout d’abonnements à la liste verte, [contacter le support](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Effectuez la migration dès que possible pour éviter tout impact négatif sur votre activité et pour tirer parti des performances améliorées, de la sécurité ainsi que des nouvelles fonctionnalités fournies par Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Quelles sont les ressources qui me sont fournies pour cette migration ?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html) : Support de Microsoft et de la communauté pour la migration

- [Prise en charge de la migration Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}) : Équipe dédiée pour l’assistance technique pendant la migration

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack) : L’équipe Microsoft Fast Track peut fournir une assistance technique aux clients éligibles qui effectuent la migration. 

- Si votre entreprise ou organisation a conclu un partenariat avec Microsoft et/ou si vous collaborez avec un représentant Microsoft, par exemple un Cloud Solution Architect (CSA) ou un Technical Account Manager (TAM), contactez-le pour obtenir des ressources supplémentaires en vue de la migration. 

