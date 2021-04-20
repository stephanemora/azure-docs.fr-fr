---
title: Bonnes pratiques relatives à l’isolation de cluster
titleSuffix: Azure Kubernetes Service
description: Découvrir les bonnes pratiques de l’opérateur relatives à l’isolation de clusters dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105168"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives à l’isolation de clusters dans Azure Kubernetes Service (AKS)

Quand vous gérez des clusters dans Azure Kubernetes Service (AKS), il est souvent nécessaire d’isoler les équipes et les charges de travail. AKS vous offre une certaine flexibilité dans la manière d’exécuter les clusters multilocataires et d’isoler les ressources. Pour maximiser votre investissement dans Kubernetes, il est important de comprendre et d’implémenter ces fonctionnalités de multilocation et d’isolation.

Cet article traite des bonnes pratiques d’isolation pour les opérateurs de clusters. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Préparer des clusters multilocataires et la séparation des ressources
> * Utiliser l’isolement logique ou physique dans vos clusters AKS

## <a name="design-clusters-for-multi-tenancy"></a>Concevoir des clusters pour la multilocation

Kubernetes vous permet d’isoler logiquement les équipes et les charges de travail dans le même cluster. L’objectif est de fournir le moins de privilèges possible sur les seules ressources dont chaque équipe a besoin. Un [espace de noms][k8s-namespaces] dans Kubernetes crée une limite d’isolation logique. Voici d’autres fonctionnalités et considérations liées à l’isolation et à la multilocation dans Kubernetes :

### <a name="scheduling"></a>Planification

La *planification* utilise des fonctionnalités de base telles que les quotas de ressources et les budgets de perturbation de pod. Pour plus d’informations sur ces fonctionnalités, consultez [Bonnes pratiques relatives aux fonctionnalités de base du planificateur dans AKS][aks-best-practices-scheduler].

Les fonctionnalités plus avancées du planificateur sont les suivantes :
* Teintes et tolérances
* Sélecteurs de nœud
* Affinité de nœud et de pod ou anti-affinité. 

Pour plus d’informations sur ces fonctionnalités, consultez [Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Mise en réseau

Le *réseau* utilise des stratégies réseau pour contrôler le flux du trafic à destination et en provenance des pods.

### <a name="authentication-and-authorization"></a>Authentification et autorisation

L’*authentification et l’autorisation* utilisent :
* Contrôle d’accès en fonction du rôle
* L’intégration Azure Active Directory (AD)
* Des identités de pod
* Des secrets dans Azure Key Vault 

Pour plus d’informations sur ces fonctionnalités, consultez [Bonnes pratiques relatives à l’authentification et à l’autorisation dans AKS][aks-best-practices-identity].

### <a name="containers"></a>Containers
Les *conteneurs* sont les suivants :
* Le module complémentaire Azure Policy pour AKS pour appliquer la sécurité des pods.
* L’utilisation de contextes de sécurité des pods.
* L’analyse des images et du runtime pour identifier les vulnérabilités. 
* Utilisation d’App Armor ou de Seccomp (Secure Computing) pour restreindre l’accès du conteneur au nœud sous-jacent.

## <a name="logically-isolate-clusters"></a>Isoler logiquement des clusters

> **Conseils sur les bonnes pratiques**
>
> La séparation des équipes et des projets à l’aide de l’*isolation logique*. Réduire le nombre de clusters AKS physiques que vous déployez pour isoler les équipes ou les applications.

Grâce à l’isolation logique, un même cluster AKS peut être utilisé pour plusieurs charges de travail, équipes ou environnements. Les [espaces de noms][k8s-namespaces] Kubernetes forment la limite d’isolation logique pour les charges de travail et les ressources.

![Isolation logique d’un cluster Kubernetes dans AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

La séparation logique des clusters fournit généralement une densité de pod supérieure à celle des clusters physiquement isolés, avec une capacité de calcul en attente moins importante dans le cluster. En combinant cette approche à l’outil Kubernetes de mise à l’échelle automatique de cluster (« autoscaler »), vous pouvez effectuer un scale-up ou un scale-down des nœuds pour répondre aux demandes. Cette bonne pratique concernant la mise à l’échelle automatique vous permet d’exécuter uniquement le nombre de nœuds nécessaires et de réduire les coûts.

Actuellement, les environnements Kubernetes ne sont pas totalement sûrs pour une utilisation multi-locataire hostile. Dans un environnement multilocataire, plusieurs locataires travaillent sur une infrastructure partagée commune. Si tous les locataires ne peuvent pas être approuvés, vous aurez besoin d’une planification supplémentaire pour empêcher les locataires d’avoir un impact sur la sécurité et le service des autres.

Des fonctionnalités de sécurité supplémentaires, telles que les *stratégies de sécurité de pod* ou le RBAC Kubernetes pour les nœuds, bloquent efficacement les attaques. Pour une véritable sécurité lors de l’exécution de charges de travail multi-locataires hostiles, vous ne devez faire confiance qu’à un hyperviseur. Le domaine de sécurité de Kubernetes devient le cluster, et non un nœud individuel. 

Pour ces types de charges de travail multi-locataires hostiles, vous devez utiliser des clusters physiquement isolés.

## <a name="physically-isolate-clusters"></a>Isoler physiquement des clusters

> **Conseils sur les bonnes pratiques** 
>
> Réduisez l’utilisation de l’isolation physique pour chaque équipe ou déploiement d’application. Utilisez plutôt l’isolation *logique*, comme indiqué dans la section précédente.

Une approche courante en matière d’isolation de cluster consiste à utiliser des clusters AKS séparés physiquement. Dans ce modèle d’isolation, les équipes ou charges de travail se voient affecter leur propre cluster AKS. Bien que l’isolation physique peut sembler être la façon la plus simple d’isoler des charges de travail ou des équipes, elle ajoute une surcharge de gestion et financière. Vous devez maintenant gérer ces clusters, en particulier fournir un accès et affecter des autorisations à chacun d’eux. Vous serez également facturé pour chaque nœud individuel.

![Isolation physique de clusters Kubernetes individuels dans AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Les clusters séparés physiquement offrent généralement une faible densité de pods. Comme chaque équipe ou charge de travail a son propre cluster AKS, le cluster est fréquemment surprovisionné avec des ressources de calcul. Souvent, un petit nombre de pods sont planifiés sur ces nœuds. Toute capacité de nœud inutilisée ne peut pas être exploitée par d’autres équipes pour des applications ou des services en cours de développement. Cet excédent de ressources engendre des frais supplémentaires pour les clusters séparés physiquement.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons traité de l’isolation des clusters. Pour plus d’informations sur les opérations liées aux clusters dans AKS, consultez les bonnes pratiques suivantes :

* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]
* [Fonctionnalités avancées du planificateur Kubernetes][aks-best-practices-advanced-scheduler]
* [Authentification et autorisation][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
