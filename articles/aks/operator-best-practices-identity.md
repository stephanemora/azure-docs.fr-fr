---
title: Bonnes pratiques pour la gestion de l’identité
titleSuffix: Azure Kubernetes Service
description: Découvrir les bonnes pratiques de l’opérateur relatives à l’authentification et à l’autorisation des clusters dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790967"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives à l’authentification et à l’autorisation dans Azure Kubernetes Service (AKS)

À mesure que vous déployez et tenez à jour des clusters dans Azure Kubernetes Service (AKS), vous devez implémenter des méthodes pour gérer l’accès aux ressources et aux services. Sans ces contrôles, les comptes peuvent avoir accès à des ressources et à des services dont ils n’ont pas besoin. Il peut également être difficile de savoir quel ensemble d’informations d’identification a été utilisé pour apporter des changements.

Cet article traite des bonnes pratiques relatives à la gestion des accès et des identités pour les opérateurs de clusters AKS. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Authentifier les utilisateurs de clusters AKS auprès d’Azure Active Directory
> * Contrôler l’accès aux ressources à l’aide du contrôle d’accès en fonction du rôle (RBAC Kubernetes) Kubernetes
> * Utilisez Azure RBAC pour contrôler de manière précise l’accès à la ressource AKS et l’API Kubernetes à l’échelle, ainsi qu’à kubeconfig.
> * Utiliser une identité managée pour l’authentification des pods auprès d’autres services

## <a name="use-azure-active-directory"></a>Utiliser Azure Active Directory

**Guide de bonne pratique** : déployez des clusters AKS avec l’intégration Azure AD. L’utilisation d’Azure AD permet de centraliser le composant de gestion des identités. Tout changement au niveau du compte d’utilisateur ou de l’état du groupe est automatiquement mis à jour dans l’accès au cluster AKS. Utilisez des Roles ou des ClusterRoles et des Bindings, comme indiqué dans la section suivante, pour affecter aux utilisateurs ou aux groupes le plus petit nombre d’autorisations nécessaires.

Les développeurs et propriétaires d’applications de votre cluster Kubernetes doivent pouvoir accéder à différentes ressources. Kubernetes ne propose pas de solution de gestion des identités pour contrôler les utilisateurs pouvant interagir avec certaines ressources. Au lieu de cela, vous intégrez généralement votre cluster à une solution d’identité existante. Azure Active Directory (AD) fournit une solution de gestion des identités d’entreprise qui s’intègre aux clusters AKS.

Avec les clusters intégrant Azure AD dans AKS, vous créez des *Roles* ou des *ClusterRoles* qui définissent les autorisations d’accès aux ressources. Vous *liez* ensuite les rôles aux utilisateurs ou aux groupes à partir d’Azure AD. Ce contrôle d’accès en fonction du rôle (RBAC Kubernetes) Kubernetes est traité dans la section suivante. L’intégration d’Azure AD et la façon dont vous contrôlez l’accès aux ressources sont représentées dans le diagramme suivant :

![Authentification au niveau du cluster pour l’intégration d’Azure Active Directory à AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Le développeur s’authentifie auprès d’Azure AD.
1. Le point de terminaison d’émission de jeton Azure AD émet le jeton d’accès.
1. Le développeur effectue une action à l’aide du jeton Azure AD, par exemple `kubectl create pod`
1. Kubernetes valide le jeton auprès d’Azure Active Directory et récupère (fetch) les appartenances aux groupes du développeur.
1. Les stratégies relatives au cluster et au contrôle d’accès en fonction du rôle (RBAC Kubernetes) Kubernetes sont appliquées.
1. La demande du développeur réussit ou non selon la validation précédente de l’appartenance au groupe Azure AD et les stratégies relatives au cluster et au RBAC Kubernetes.

Pour créer un cluster AKS qui utilise Azure AD, consultez [Intégrer Azure Active Directory à AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Utiliser le contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes)

**Guide de bonne pratique** : utilisez le RBAC Kubernetes pour définir les autorisations dont disposent les utilisateurs ou les groupes sur les ressources du cluster. Créez des rôles et des liaisons qui affectent le plut petit nombre d’autorisations nécessaires. Effectuez l’intégration à Azure AD pour refléter automatiquement tout changement apporté à l’état de l’utilisateur ou à l’appartenance au groupe, et pour tenir à jour l’accès aux ressources de cluster.

Dans Kubernetes, vous pouvez fournir un contrôle granulaire de l’accès aux ressources dans le cluster. Les autorisations sont définies au niveau du cluster ou d’espaces de noms spécifiques. Vous pouvez définir les ressources qui peuvent être gérées et les autorisations associées. Ces rôles sont ensuite appliqués aux utilisateurs ou aux groupes avec une liaison. Pour plus d’informations sur *Roles*, *ClusterRoles* et *Bindings*, consultez [Options d’accès et d’identité pour Azure Kubernetes Service (AKS)][aks-concepts-identity].

Par exemple, vous pouvez créer un Role qui accorde un accès complet aux ressources dans l’espace de noms nommé *finance-app*, comme illustré dans l’exemple de manifeste YAML suivant :

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Un RoleBinding qui lie l’utilisateur Azure AD *developer1\@contoso.com* au RoleBinding est ensuite créé, comme illustré dans le manifeste YAML suivant :

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quand *developer1\@contoso.com* est authentifié auprès du cluster AKS, il dispose d’autorisations complètes aux ressources dans l’espace de noms *finance-app*. De cette façon, vous séparez et contrôlez logiquement l’accès aux ressources. Kubernetes RBAC doit être utilisé conjointement avec l’intégration à Azure AD, comme indiqué dans la section précédente.

Pour en savoir plus sur l’utilisation des groupes Azure AD pour contrôler l’accès aux ressources Kubernetes à l’aide de RBAC Kubernetes, consultez [Contrôler l’accès aux ressources de cluster à l’aide du contrôle d’accès en fonction du rôle et d’identités Azure Active Directory dans AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Utiliser Azure RBAC 
**Guide des meilleures pratiques** : utilisez Azure RBAC pour définir les autorisations minimales requises dont disposent les utilisateurs ou les groupes pour les ressources AKS dans un ou plusieurs abonnements.

Il existe deux niveaux d’accès nécessaires pour pleinement utiliser un cluster AKS : 
1. Accéder à la ressource AKS sur votre abonnement Azure. Ce niveau d’accès vous permet de contrôler les opérations de mise à l’échelle ou de mise à niveau de votre cluster à l’aide des API AKS, ainsi que d’extraire votre kubeconfig.
Pour savoir comment contrôler l’accès aux ressources AKS et à kubeconfig, consultez [Limiter l’accès au fichier de configuration du cluster](control-kubeconfig-access.md).

2. Accéder à l’API Kubernetes. Ce niveau d’accès est contrôlé par [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (traditionnellement) ou par l’intégration d’Azure RBAC avec AKS pour l’autorisation Kubernetes.
Pour savoir comment accorder de manière granulaire des autorisations à l’API Kubernetes à l’aide d’Azure RBAC, consultez [Utiliser Azure RBAC pour l’autorisation Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Utiliser des identités managées par pod

**Guide de bonne pratique** : n’utilisez pas d’informations d’identification fixes au sein de pods ou d’images conteneurs, car elles présentent un risque d’exposition ou d’abus. Utilisez plutôt des identités de pod pour demander automatiquement l’accès à l’aide d’une solution d’identité Azure AD centrale. Les identités de pod sont conçues pour être utilisées avec des images conteneur et des pods Linux uniquement.

> [!NOTE]
> La prise en charge des identités managées par pod pour les conteneurs Windows sera bientôt disponible.

Pour qu’un pod puisse accéder à d’autres services Azure, tels que Cosmos DB, Key Vault ou Stockage Blob, il a besoin d’informations d’identification d’accès. Ces dernières peuvent être définies avec l’image conteneur ou injectées comme secret Kubernetes, mais elles doivent être manuellement créées et affectées. Souvent, les informations d’identification sont réutilisées entre pods et ne font pas l’objet d’une rotation régulière.

Les identités managées par pod pour les ressources Azure vous permettent de demander automatiquement l’accès aux services par le biais d’Azure AD. Les identités managées par pod sont désormais disponibles en préversion pour Azure Kubernetes Service. Pour commencer, consultez la documentation [Utiliser des identités managées par pod Azure Active Directory dans Azure Kubernetes Service (préversion)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity). Grâce aux identités managées par pod, vous ne définissez pas manuellement d’informations d’identification pour les pods. En fait, les pods demandent un jeton d’accès en temps réel qu’ils peuvent ensuite utiliser pour accéder uniquement aux services qui leur ont été affectés. Dans AKS, eux composants gèrent les opérations pour permettre aux pods d’utiliser des identités managées :

* Le **serveur NMI (Node Management Identity)** est un pod qui s’exécute en tant que DaemonSet sur chaque nœud du cluster AKS. Le serveur NMI écoute les demandes adressées par les pods aux services Azure.
* Le **fournisseur de ressources Azure** interroge le serveur d’API Kubernetes et recherche un mappage d’identités Azure qui correspond à un pod.

Quand des pods demandent l’accès à un service Azure, les règles de réseau redirigent le trafic vers le serveur NMI. Le serveur NMI identifie les pods qui demandent l’accès à des services Azure en fonction de leur adresse distante, puis interroge le fournisseur de ressources Azure. Le fournisseur de ressources Azure recherche des mappages d’identités Azure dans le cluster AKS, puis le serveur NMI demande un jeton d’accès à Azure Active Directory (AD) en fonction du mappage d’identités du pod. Azure AD fournit un jeton d’accès au serveur NMI, qui est retourné au pod. Ce jeton d’accès peut alors être utilisé par le pod pour demander l’accès à des services dans Azure.

Dans l’exemple suivant, un développeur crée un pod qui utilise une identité managée pour demander l’accès à Azure SQL Database :

![Identités de pod permettant à un pod de demander automatiquement l’accès à d’autres services](media/operator-best-practices-identity/pod-identities.png)

1. L’opérateur de cluster crée d’abord un compte de service qui peut être utilisé pour mapper des identités quand des pods demandent l’accès à des services.
1. Le serveur NMI est déployé pour relayer toute demande de pod, avec le fournisseur de ressources Azure, pour des jetons d’accès à Azure AD.
1. Un développeur déploie un pod avec une identité managée qui demande un jeton d’accès par le biais du serveur NMI.
1. Le jeton est retourné au pod et utilisé pour accéder à Azure SQL Database

> [!NOTE]
> Les identités managées par pod sont actuellement disponibles à l’état de préversion.

Pour utiliser des identités managées par pod, consultez [Utiliser des identités managées par pod Azure Active Directory dans Azure Kubernetes Service (préversion)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons traité des bonnes pratiques relatives à l’authentification et à l’autorisation pour votre cluster et vos ressources. Pour implémenter quelques-unes de ces bonnes pratiques, consultez les articles suivants :

* [Intégrer Azure Active Directory à AKS][aks-aad]
* [Utiliser des identités managées par pod Azure Active Directory dans Azure Kubernetes Service (préversion)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Pour plus d’informations sur les opérations liées aux clusters dans AKS, consultez les bonnes pratiques suivantes :

* [Multilocation et isolation de cluster][aks-best-practices-cluster-isolation]
* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]
* [Fonctionnalités avancées du planificateur Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
