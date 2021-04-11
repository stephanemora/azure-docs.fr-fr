---
title: Nous allons mettre hors service les machines virtuelles Azure (classiques) le 1er mars 2023
description: Cet article fournit une vue d’ensemble de la mise hors service des machines virtuelles créées avec le modèle de déploiement Classique.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668196"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrez vos ressources IaaS vers Azure Resource Manager avant le 1er mars 2023 

En 2014, nous avons lancé IaaS (Infrastructure as a Service) sur [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Nous en avons depuis lors amélioré les fonctionnalités. Comme Azure Resource Manager intègre désormais des fonctionnalités IaaS complètes et d’autres avancées, nous avons déprécié la gestion des machines virtuelles IaaS via [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) le 28 février 2020. Cette fonctionnalité sera entièrement mise hors service le 1er mars 2023. 

Aujourd’hui, environ 90 % des machines virtuelles IaaS utilisent Azure Resource Manager. Si vous utilisez des ressources IaaS via ASM, commencez à planifier votre migration dès maintenant. Terminez-la le 1er mars 2023 pour tirer parti d’[Azure Resource Manager](../azure-resource-manager/management/index.yml).

Les machines virtuelles créées en utilisant le modèle de déploiement Classique suivent la [politique de cycle de vie moderne](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pour la mise hors service.

## <a name="how-does-this-affect-me"></a>Dans quelle mesure suis-je affecté ? 

- Depuis le 28 février 2020, les clients qui n’utilisaient pas de machines virtuelles IaaS via ASM en février 2020 ne peuvent plus créer de machines virtuelles (classiques). 
- Le 1er mars 2023, les clients ne seront plus en mesure de démarrer des machines virtuelles IaaS en utilisant ASM. Celles qui seront toujours en cours d’exécution ou allouées seront arrêtées et désallouées. 
- Le 1er mars 2023, les abonnements qui n’auront pas migré vers Azure Resource Manager seront informés du calendrier de suppression des machines virtuelles classiques restantes.  

Cette mise hors service n’affecte *pas* les services et fonctionnalités Azure suivants : 
- [Azure Cloud Services (classique)](../cloud-services/cloud-services-choose-me.md)
- Comptes de stockage *non* utilisés par des machines virtuelles classiques 
- Réseaux virtuels *non* utilisés par des machines virtuelles classiques 
- Autres ressources Classic

## <a name="what-resources-are-available-for-this-migration"></a>Quelles ressources sont disponibles pour cette migration ?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html) : Support de Microsoft et de la communauté pour la migration.

- [Prise en charge de la migration Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}) : Équipe de support dédiée pour l’assistance technique pendant la migration. Les clients sans support technique peuvent utiliser la [fonction de support gratuit](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) fournie spécifiquement pour cette migration. 

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack) : Fast Track peut aider les clients éligibles à planifier et à exécuter cette migration. [Désignez-vous](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) pour le programme de migration du DC.  

- Si votre entreprise/organisation a conclu un partenariat avec Microsoft ou si vous collaborez avec des représentants Microsoft, par exemple un architecte de solution cloud (CSA) ou un responsable technique de compte (TAM), contactez-les afin d’obtenir des ressources supplémentaires pour la migration.

## <a name="what-actions-should-i-take"></a>Que dois-je faire ? 

Commencez dès aujourd’hui la planification de votre migration vers Azure Resource Manager. 

1. Dressez la liste de toutes les machines virtuelles affectées : 

   - Les machines virtuelles de type **Machines virtuelles (classiques)** dans le [volet des machines virtuelles du portail Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) sont toutes les machines virtuelles affectées au sein de l’abonnement. 
   - Vous pouvez également interroger Azure Resource Graph en utilisant le [portail](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) ou [PowerShell](../governance/resource-graph/concepts/work-with-data.md) pour afficher la liste de toutes les machines virtuelles (classiques) marquées et les informations associées pour les abonnements sélectionnés. 
   - Le 8 février et le 2 septembre 2020, nous avons envoyé des e-mails avec l’objet « Démarrer la planification de la migration de vos machines virtuelles IaaS vers Azure Resource Manager » aux propriétaires d’abonnements. L’e-mail fournit une liste de tous les abonnements et machines virtuelles (classiques). Utilisez-les pour dresser cette liste. 

1. [Découvrez plus d’informations](./migration-classic-resource-manager-overview.md) sur la migration de vos machines virtuelles [Linux](./migration-classic-resource-manager-plan.md) et [Windows](./migration-classic-resource-manager-plan.md) (classiques) vers Azure Resource Manager. Pour plus d’informations, consultez [Questions fréquemment posées sur la migration de Classique vers Azure Resource Manager](./migration-classic-resource-manager-faq.md).

1. Nous vous recommandons de commencer la planification en utilisant l’[outil de migration de prise en charge de plateforme](./migration-classic-resource-manager-overview.md) pour migrer vos machines virtuelles existantes en 3 étapes simples : confirmer, préparer et valider. L’outil est conçu pour migrer vos machines virtuelles sans temps d’arrêt ou avec un temps d’arrêt minimal. 

   - La première étape, « Confirmer » n’a aucun impact sur votre déploiement existant : elle produit la liste de tous les scénarios non pris en charge pour la migration. 
   - Parcourez la [liste des solutions de contournement](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) pour corriger votre déploiement et le préparer pour la migration. 
   - Idéalement, une fois toutes les erreurs de confirmation corrigées, vous ne devez pas rencontrer de problèmes lors des étapes de préparation et de validation. Une fois la validation réussie, votre déploiement est migré de façon dynamique vers Azure Resource Manager, et il peut alors être géré via de nouvelles API exposées par Azure Resource Manager. 

   Si cet outil n’est pas adapté pour votre migration, vous pouvez explorer d’[autres offres de calcul](/azure/architecture/guide/technology-choices/compute-decision-tree) pour cette migration. Comme il existe de nombreuses offres de calcul Azure très différentes les unes des autres, nous ne pouvons pas fournir un parcours de migration pris en charge par la plateforme.  

1. Pour toute question technique, tout problème et l’ajout d’abonnements à la liste verte, [contactez le support](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Effectuez la migration dès que possible pour éviter tout impact négatif sur votre activité et pour tirer parti des performances améliorées, de la sécurité ains que des nouvelles fonctionnalités d’Azure Resource Manager. 
