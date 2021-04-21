---
title: Guide pratique pour planifier et déployer Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Intégrez un grand nombre de clusters à Kubernetes avec Azure Arc pour la gestion de la configuration.
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315162"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Planifier et déployer Kubernetes avec Azure Arc

Le déploiement d’un service d’infrastructure informatique ou d’une application métier est un défi pour toute entreprise. Pour éviter les mauvaises surprises et les coûts imprévus, vous devez le planifier minutieusement afin d’être dans les meilleures conditions de préparation. Un tel plan doit identifier les critères de conception et de déploiement qui doivent être satisfaits pour effectuer les tâches.

Pour que le déploiement continue sans accroc, votre plan doit clairement établir les points suivants :

* Rôles et responsabilités.
* Inventaire de tous les clusters Kubernetes
* Configuration réseau requise
* Ensemble de compétences et formation requises pour opérer un déploiement réussi et une gestion continue.
* Critère d’acceptation et manière de suivre sa satisfaction.
* Outils ou méthodes à utiliser pour automatiser les déploiements.
* Identification des risques et des plans d’atténuation afin d’éviter les retards et interruptions
* Manière de procéder pour éviter une interruption pendant le déploiement.
* Chemin de réaffectation d’un éventuel problème majeur.

L’objectif de cet article est de vous aider à vérifier que vous êtes prêt à déployer correctement Kubernetes avec Azure Arc sur plusieurs clusters de production au sein de votre environnement.

## <a name="prerequisites"></a>Prérequis

* Un cluster Kubernetes existant. Si vous n'en avez pas, vous pouvez créer un cluster en utilisant l'une des options suivantes :
    - [Kubernetes dans Docker (KIND)](https://kind.sigs.k8s.io/)
    - Créer un cluster Kubernetes à l’aide de Docker pour [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Cluster Kubernetes automanagé utilisant l'[API Cluster](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* Vos machines disposent d’une connectivité à partir de votre réseau local ou d’un autre environnement cloud aux ressources Azure, directement ou via un serveur proxy. Pour plus d’informations, consultez [Configuration réseau requise](quickstart-connect-cluster.md#meet-network-requirements).

* Un fichier `kubeconfig` pointant vers le cluster que vous souhaitez connecter à Azure Arc.
* Autorisations d’accès en lecture/écriture pour l’utilisateur ou le principal de service qui crée le type de ressource Kubernetes avec Azure Arc `Microsoft.Kubernetes/connectedClusters`.

## <a name="pilot"></a>Pilote

Avant de déployer sur tous les clusters de production, commencez par évaluer ce processus de déploiement avant de l’adopter largement dans votre environnement. Pour un pilote, identifiez un échantillonnage représentatif de clusters qui ne sont pas critiques en lien avec la capacité de votre entreprise à mener son activité. Veillez à prévoir suffisamment de temps pour exécuter le pilote et évaluer son impact : nous recommandons une durée d’environ 30 jours.

Établissez un plan formel décrivant l’étendue et les détails du pilote. L’exemple de plan suivant peut vous aider à démarrer.

* **Objectifs** : Décrit les facteurs métier et techniques qui ont conduit à la décision de la nécessité d’un pilote.
* **Critères de sélection** : critères utilisés pour sélectionner les aspects de la solution qui seront démontrés via un pilote.
* **Étendue** : couvre notamment les composants de la solution, la planification prévue, la durée du pilote et le nombre de clusters à cibler.
* **Critères et métriques de réussite** : Définit les critères de réussite du pilote et mesures spécifiques servant à déterminer le niveau de réussite.
* **Plan de formation** : Décrit le plan de formation des ingénieurs système, administrateurs et autres intervenants qui découvrent Azure et ses services dans le cadre du pilote.
* **Plan de transition** : Décrit la stratégie et les critères utilisés pour guider la transition du pilote à la production.
* **Restauration** : Décrit les procédures de restauration d’un pilote dans l’état précédant le déploiement.
* **Risques** : Liste tous les risques identifiés pour la conduite du pilote et associés au déploiement en production.

## <a name="phase-1-build-a-foundation"></a>Phase 1 : créer une fondation

Au cours de cette phase, les ingénieurs système ou les administrateurs effectuent les activités de base, telles que la création de groupes de ressources, d’étiquettes et d’attributions de rôles, afin que les ressources Kubernetes avec Azure Arc puissent ensuite être créées et exploitées.

|Tâche |Detail |Duration |
|-----|-------|---------|
| [Créer un groupe de ressources](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Groupe de ressources dédié incluant uniquement les ressources Kubernetes avec Azure Arc et centraliser la gestion et la supervision de ces ressources. | 1 heure |
| Appliquer des [étiquettes](../../azure-resource-manager/management/tag-resources.md) pour faciliter l’organisation des machines | Évaluez et développez une [stratégie d’étiquetage](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) alignée sur l’informatique. Elle pourra vous aider à réduire la complexité liée à la gestion de vos ressources Kubernetes avec Azure Arc, ainsi qu’à simplifier la prise de décisions de gestion. | Un jour |
| Identifier les [configurations](tutorial-use-gitops-connected-cluster.md) pour GitOps | Identifiez les configurations d’application ou de référence, telles que `PodSecurityPolicy` ou `NetworkPolicy`, que vous souhaitez déployer sur vos clusters. | Un jour |
| [Élaborer un plan de gouvernance Azure Policy](../../governance/policy/overview.md) | Déterminez comment vous allez implémenter la gouvernance des clusters Kubernetes avec Azure Arc au niveau de l’abonnement ou de l’étendue de groupe de ressources avec Azure Policy. | Un jour |
| Configurer un [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC) | Développez un plan d’accès afin d’identifier les utilisateurs qui disposent d’autorisations de lecture/d’écriture/complètes sur vos clusters. | Un jour |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Phase 2 : Planifier et déployer Kubernetes avec Azure Arc

Au cours de cette phase, nous allons connecter vos clusters Kubernetes à Azure :

|Tâche |Detail |Duration |
|-----|-------|---------|
| [Connecter le premier cluster Kubernetes à Azure Arc](quickstart-connect-cluster.md) | Dans le cadre de la connexion de votre premier cluster à Azure Arc, configurez votre environnement d’intégration avec tous les outils nécessaires, tels qu’Azure CLI, Helm et l’extension `connectedk8s` pour Azure CLI. | 15 minutes |
| [Créer un principal du service](create-onboarding-service-principal.md) | Créez un principal de service pour connecter les clusters Kubernetes de manière non interactive à l’aide d’Azure CLI ou de PowerShell. | 1 heure |


## <a name="phase-3-manage-and-operate"></a>Phase 3 : gérer et exploiter

Au cours de cette phase, nous allons déployer des applications et des configurations de référence sur vos clusters Kubernetes.

|Tâche |Detail |Duration |
|-----|-------|---------|
|[Créer des configurations](tutorial-use-gitops-connected-cluster.md) sur vos clusters | Créez des configurations pour déployer vos applications sur votre ressource Kubernetes avec Azure Arc. | 15 minutes |
|[Utiliser Azure Policy](use-azure-policy.md) pour l’application à grande échelle des configurations | Créez des attributions de stratégie afin d’automatiser le déploiement de configurations de référence sur tous vos clusters sous un abonnement ou une étendue de groupe de ressources. | 15 minutes |
| [Mettre à niveau les agents Azure Arc](agent-upgrade.md) | Si vous avez désactivé la mise à niveau automatique des agents sur vos clusters, mettez à jour vos agents manuellement vers la dernière version afin d’être sûr de disposer des correctifs de sécurité et de bogues les plus récents. | 15 minutes |

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Créez des configurations sur votre cluster Kubernetes avec Azure Arc](./tutorial-use-gitops-connected-cluster.md).
* [Utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md).