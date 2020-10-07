---
title: Introduction
description: Découvrez les fonctionnalités et les avantages d’Azure VMware Solution pour déployer et gérer des charges de travail VMware dans Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: b71427402e08167e5868128ed1e553c69bef425d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574453"
---
# <a name="what-is-azure-vmware-solution"></a>Présentation d’Azure VMware Solution

Azure VMware Solution met à votre disposition des clouds privés dans Azure. Les clouds privés contiennent des clusters vSphere, générés à partir d’une infrastructure Azure complète dédiée. Vous pouvez mettre à l’échelle des clusters de cloud privé de trois à 16 hôtes, avec la possibilité d’avoir plusieurs clusters dans un même cloud privé. Tous les clouds privés sont provisionnés avec vCenter Server, vSAN, vSphere et NSX-T. Vous pouvez migrer des charges de travail à partir de vos environnements locaux, créer ou déployer de nouvelles machines virtuelles, et consommer des services Azure à partir de vos clouds privés.

Azure VMware Solution est une solution validée par VMware, qui teste et valide constamment les améliorations et les mises à niveau. L’infrastructure et les logiciels de cloud privé sont managés et tenus à jour par Microsoft, ce qui vous permet de vous concentrer sur le développement et l’exécution de charges de travail dans vos clouds privés.

Le diagramme suivant illustre la contiguïté entre les clouds privés et les réseaux virtuels dans Azure, les services Azure et les environnements locaux. L’accès réseau des clouds privés vers les services Azure ou les réseaux virtuels fournit une intégration pilotée par SLA des points de terminaison de service Azure. L’accès au cloud privé à partir d’environnements locaux utilise ExpressRoute Global Reach pour une connexion privée et sécurisée.

![Image de la contiguïté du cloud privé Azure VMware Solution avec Azure et les environnements locaux](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hôtes, clusters et clouds privés

Les clusters et clouds privés Azure VMware Solution reposent sur un hôte d’infrastructure Azure nu et hyper-convergé. Les hôtes haut de gamme ont une RAM de 576 Go et deux processeurs Intel 18 cœurs de 2,3 GHz. Ils bénéficient en outre de deux groupes de disques vSAN avec un niveau de capacité brut vSAN brut de 15,36 To (SSD) et un niveau de cache vSAN de 3,2 To (NVMe).

Les nouveaux clouds privés sont déployés par le biais du portail Azure ou d’Azure CLI.

## <a name="networking"></a>Mise en réseau

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Pour plus d’informations sur la mise en réseau et l’interconnexion, consultez l’article sur les [concepts de mise en réseau](concepts-networking.md).

## <a name="access-and-security"></a>Accès et sécurité

Pour une sécurité renforcée, les clouds privés Azure VMware Solution utilisent le contrôle d’accès en fonction du rôle vSphere. Les fonctionnalités LDAP SSO vSphere peuvent être intégrées à Azure Active Directory. Pour plus d’informations sur l’identité et les privilèges, consultez l’article sur les [concepts d’accès et d’identité](concepts-identity.md).

Le chiffrement des données au repos vSAN est activé par défaut et est utilisé pour assurer la sécurité du magasin de données vSAN. Il est décrit plus en détail dans l’article sur les [concepts de stockage](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Maintenance du cycle de vie des hôtes et des logiciels

Les mises à niveau régulières du cloud privé Azure VMware Solution et des logiciels VMware garantissent la sécurité, la stabilité et les ensembles de fonctionnalités les plus récents dans vos clouds privés. Vous trouverez plus d’informations sur la maintenance et les mises à niveau de la plateforme dans l’article sur les [concepts de mise à niveau](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Supervision de votre cloud privé

Après le déploiement d’Azure VMware Solution dans votre abonnement, les [journaux Azure Monitor](../azure-monitor/overview.md) sont générés automatiquement. Vous pouvez également collecter les journaux sur chacune des machines virtuelles installées dans votre cloud privé. Vous pouvez [télécharger et installer l’agent MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options) sur des machines virtuelles Linux et Windows s’exécutant dans vos clouds privés Azure VMware Solution, et activer l’[extension Diagnostics Azure](../azure-monitor/platform/diagnostics-extension-overview.md). Vous pouvez aussi exécuter les mêmes requêtes que celles que vous exécutez habituellement sur vos machines virtuelles. Pour en savoir plus sur la création de requêtes, consultez le [guide pratique pour écrire des requêtes](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries). Les modèles de supervision dans Azure VMware Solution sont similaires aux machines virtuelles Azure sur la plateforme IaaS. Pour obtenir des informations supplémentaires et des procédures, consultez [Supervision de machines virtuelles Azure avec Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à se familiariser avec les [concepts clés des clouds privés et des clusters](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
