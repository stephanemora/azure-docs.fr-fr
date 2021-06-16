---
title: Questions courantes sur les clusters managés Service Fabric
description: Foire aux questions sur les clusters managés Service Fabric, notamment les capacités, les cas d’usage et les scénarios courants.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 5/10/2021
ms.custom: references_regions
ms.openlocfilehash: f79146e1271436e29dcd7f911c250cab58c28707
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111956755"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur les clusters Service Fabric managés

Voici quelques questions fréquentes (FAQ) et des réponses sur les clusters managés Service Fabric.

## <a name="general"></a>Général

### <a name="what-are-service-fabric-managed-clusters"></a>Que sont les clusters managés Service Fabric ?

Les clusters managés Service Fabric sont une évolution du modèle de ressource de cluster Service Fabric qui est conçu pour faciliter le déploiement et la gestion des clusters. Un cluster managé Service Fabric utilise le modèle d’encapsulation Azure Resource Manager, de sorte qu’un utilisateur n’a besoin de définir et de déployer qu’une seule ressource de cluster par rapport aux nombreuses ressources indépendantes qu’il doit déployer aujourd’hui (groupe de machines virtuelles identiques, équilibreur de charge, IP, etc.).

### <a name="what-regions-are-supported"></a>Quelles sont les régions prises en charge ?

Les clusters managés Service Fabric sont pris en charge dans toutes les régions du cloud public.

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Puis-je effectuer une migration sur place de mon cluster Service Fabric existant vers une ressource de cluster managé ?

Non. Vous devrez créer une ressource de cluster Service Fabric pour utiliser le nouveau type de ressource de cluster managé Service Fabric.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Les clusters managés Service Fabric engendrent-ils un coût supplémentaire ?

Non. Aucun coût supplémentaire n’est associé à un cluster managé Service Fabric au-delà du coût des ressources sous-jacentes en matière de calcul, de stockage et de réseau qui sont nécessaires pour le cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Existe-t-il un nouveau contrat SLA introduit par la ressource de cluster managé Service Fabric ?

Le contrat SLA ne change pas par rapport au modèle actuel de ressources Service Fabric.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Quelle est la différence entre les clusters des niveaux tarifaires De base et Standard ?

Un cluster de niveau tarifaire De base signifie que la plupart des configurations sont fournies par le fournisseur de ressources Service Fabric. Les clusters de niveau tarifaire De base sont destinés à être utilisés pour les tests et les environnements de préproduction. Un cluster de niveau tarifaire Standard permet aux utilisateurs de configurer le cluster en fonction de leurs besoins. Pour plus d’informations, consultez [SKU de cluster managé Service Fabric](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Déploiement et gestion de clusters

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>J’exécute des extensions de script personnalisées sur mon groupe de machines virtuelles identiques. Puis-je continuer à le faire avec une ressource Service Fabric managée ?

Oui, vous pouvez spécifier des extensions de machine virtuelle sur des types de nœuds de cluster managé. Pour plus d’informations, consultez [Ajouter une extension de groupe identique à un type de nœud de cluster managé Service Fabric](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Je souhaite avoir un équilibreur de charge interne uniquement, est-ce possible ?

Non. Il n’est actuellement pas possible d’avoir un équilibreur de charge interne uniquement. Nous vous recommandons de verrouiller les règles de groupe de sécurité réseau (NSG) pour bloquer tout trafic entrant/sortant indésirable.

### <a name="can-i-autoscale-my-cluster"></a>Puis-je mettre automatiquement mon cluster à l’échelle ?

La mise à l'échelle automatique n'est actuellement pas prise en charge.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Puis-je déployer mon cluster sur plusieurs zones de disponibilité ?

Oui, les clusters managés Service Fabric couvrant les zones de disponibilité sont pris en charge dans les régions Azure qui prennent en charge les zones de disponibilité. Pour plus d'informations, consultez [Clusters managés Service Fabric dans les zones de disponibilité](.\service-fabric-cross-availability-zones.md).

### <a name="can-i-deploy-stateless-node-types-on-a-service-fabric-managed-cluster"></a>Puis-je déployer des types de nœud sans état sur un cluster managé Service Fabric ? 

Oui, les clusters managés Service Fabric prennent en charge les types de nœud sans état pour tous les types de nœuds secondaires. Pour plus d'informations, consultez [Types de nœud sans état sur un cluster managé Service Fabric](./how-to-managed-cluster-stateless-node-type.md).

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Puis-je choisir entre des mises à niveau automatiques et manuelles pour mon runtime de cluster ?

Oui, vous pouvez choisir des mises à niveau automatiques ou manuelles. Pour plus d'informations, consultez [Mises à niveau de clusters](./service-fabric-cluster-upgrade.md).

## <a name="applications"></a>Applications

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Existe-t-il une expérience de développement locale pour les clusters managés Service Fabric ?

L’expérience de développement locale reste inchangée par rapport aux clusters Service Fabric existants. Pour plus d’informations, consultez [Configurer votre environnement de développement](./service-fabric-get-started.md) qui vous en apprendra davantage sur l’expérience de développement local.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Puis-je déployer mes applications en tant que ressource Azure Resource Manager ?

Oui. Une prise en charge a été ajoutée pour déployer des applications en tant que ressource Azure Resource Manager (en plus du déploiement utilisant PowerShell et l’interface CLI). Pour commencer, consultez [Déployer une application de cluster managé Service Fabric à l’aide d’un modèle ARM](how-to-managed-cluster-app-deployment-template.md).

### <a name="can-i-deploy-applications-with-managed-identities"></a>Puis-je déployer des applications avec identités managées ?

 Oui, vous pouvez déployer des applications avec identités managées sur un cluster managé Service Fabric. Pour plus d'informations, consultez [Identités managées par les applications](.\concepts-managed-identity.md).