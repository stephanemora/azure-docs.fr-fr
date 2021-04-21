---
title: Bonnes pratiques pour la gestion de l’identité
titleSuffix: Azure Kubernetes Service
description: Découvrir les bonnes pratiques de l’opérateur relatives à l’authentification et à l’autorisation des clusters dans Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105100"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives à l’authentification et à l’autorisation dans Azure Kubernetes Service (AKS)

Lorsque vous déployez et gérez des clusters dans Azure Kubernetes Service (AKS), vous implémentez les méthodes de gestion de l'accès aux ressources et aux services. Sans ces contrôles :
* les comptes peuvent avoir accès à des ressources et services inutiles ; 
* il peut être difficile de déterminer quel ensemble d'informations d'identification a été utilisé pour apporter des modifications.

Cet article traite des bonnes pratiques relatives à la gestion des accès et des identités pour les opérateurs de clusters AKS. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Authentifier les utilisateurs de clusters AKS auprès d'Azure Active Directory
> * Contrôler l'accès aux ressources à l'aide du contrôle d'accès en fonction du rôle Kubernetes (RBAC Kubernetes)
> * Utiliser Azure RBAC pour contrôler de manière précise l'accès à la ressource AKS, à l'API Kubernetes à grande échelle, ainsi qu’à `kubeconfig`
> * Utiliser une identité managée pour l'authentification des pods auprès d'autres services

## <a name="use-azure-active-directory-azure-ad"></a>Utiliser Azure Active Directory (Azure AD)

> **Conseils sur les bonnes pratiques** 
> 
> Déployez des clusters AKS avec l'intégration Azure AD. L’utilisation d’Azure AD permet de centraliser le composant de gestion des identités. Tout changement au niveau du compte d’utilisateur ou de l’état du groupe est automatiquement mis à jour dans l’accès au cluster AKS. Étendez les utilisateurs ou les groupes au nombre minimum d'autorisations à l'aide de [rôles (Roles), rôles de cluster (ClusterRoles) ou liaisons (Bindings)](#use-kubernetes-role-based-access-control-kubernetes-rbac).

Les développeurs et propriétaires d'applications de votre cluster Kubernetes doivent pouvoir accéder à différentes ressources. Kubernetes ne dispose d'aucune solution de gestion des identités qui vous permettrait de contrôler les ressources avec lesquelles les utilisateurs peuvent interagir. Au lieu de cela, vous intégrez généralement votre cluster à une solution d’identité existante. Accédez à Azure AD : une solution de gestion des identités adaptées aux entreprises qui s'intègre aux clusters AKS.

Avec les clusters intégrant Azure AD dans AKS, vous créez des *Roles* ou des *ClusterRoles* qui définissent les autorisations d'accès aux ressources. Vous *liez* ensuite les rôles aux utilisateurs ou aux groupes à partir d’Azure AD. Pour plus d'informations sur le contrôle d'accès en fonction du rôle Kubernetes (RBAC Kubernetes), consultez [la section suivante](#use-kubernetes-role-based-access-control-kubernetes-rbac). L'intégration d'Azure AD et la façon dont vous contrôlez l'accès aux ressources sont représentées dans le diagramme suivant :

![Authentification au niveau du cluster pour l’intégration d’Azure Active Directory à AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Le développeur s’authentifie auprès d’Azure AD.
1. Le point de terminaison d’émission de jeton Azure AD émet le jeton d’accès.
1. Le développeur effectue une action à l'aide du jeton Azure AD, par exemple `kubectl create pod`.
1. Kubernetes valide le jeton auprès d'Azure AD et récupère (fetch) les appartenances aux groupes du développeur.
1. Les stratégies relatives au cluster et au RBAC Kubernetes sont appliquées.
1. La demande du développeur aboutit sur la base de la validation précédente de l'appartenance au groupe Azure AD et des stratégies relatives au cluster et au RBAC Kubernetes.

Pour créer un cluster AKS qui utilise Azure AD, consultez [Intégrer Azure Active Directory à AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Utiliser le contrôle d’accès en fonction du rôle Kubernetes (RBAC Kubernetes)

> **Conseils sur les bonnes pratiques**
> 
> Définissez les autorisations des utilisateurs ou des groupes pour l'accès aux ressources du cluster avec le RBAC Kubernetes. Créez des rôles et des liaisons qui affectent le plut petit nombre d’autorisations nécessaires. L'intégration à Azure AD permet de mettre automatiquement à jour tout changement d'état de l'utilisateur, ou d'appartenance à un groupe, et de maintenir à jour l'accès aux ressources du cluster.

Dans Kubernetes, vous fournissez un contrôle d'accès granulaire aux ressources du cluster. Vous définissez les autorisations au niveau du cluster ou d'espaces de noms spécifiques. Vous déterminez les ressources qui peuvent être gérées et les autorisations associées. Puis vous appliquez ces rôles aux utilisateurs ou aux groupes avec une liaison. Pour plus d’informations sur *Roles*, *ClusterRoles* et *Bindings*, consultez [Options d’accès et d’identité pour Azure Kubernetes Service (AKS)][aks-concepts-identity].

Par exemple, vous créez un rôle (Role) qui accorde un accès complet aux ressources dans l'espace de noms *finance-app*, comme illustré dans l'exemple de manifeste YAML suivant :

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

Puis vous créez une liaison de rôle (RoleBinding) et liez l'utilisateur Azure AD *developer1\@contoso.com* à celle-ci, comme illustré dans le manifeste YAML suivant :

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

Quand *developer1\@contoso.com* est authentifié auprès du cluster AKS, il dispose d’autorisations complètes aux ressources dans l’espace de noms *finance-app*. De cette façon, vous séparez et contrôlez logiquement l’accès aux ressources. Utilisez le contrôle d'accès en fonction du rôle (RBAC) Kubernetes en conjonction avec l'intégration d'Azure AD.

Pour en savoir plus sur l’utilisation des groupes Azure AD pour contrôler l’accès aux ressources Kubernetes à l’aide de RBAC Kubernetes, consultez [Contrôler l’accès aux ressources de cluster à l’aide du contrôle d’accès en fonction du rôle et d’identités Azure Active Directory dans AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Utiliser Azure RBAC 
> **Conseils sur les bonnes pratiques** 
> 
> Utilisez Azure RBAC pour définir les autorisations minimales requises pour permettre aux utilisateurs et aux groupes d'accéder aux ressources AKS dans le cadre d'un ou plusieurs abonnements.

Il existe deux niveaux d’accès nécessaires pour pleinement utiliser un cluster AKS : 
1. Accéder à la ressource AKS sur votre abonnement Azure. 

    Ce niveau d'accès vous permet d'effectuer les opérations suivantes :
      * Contrôler la mise à l'échelle ou la mise à niveau de votre cluster à l'aide des API AKS
      * Extraire votre fichier `kubeconfig`

    Pour savoir comment contrôler l'accès aux ressources AKS et au fichier `kubeconfig`, consultez [Limiter l'accès au fichier de configuration du cluster](control-kubeconfig-access.md).

2. Accédez à l’API Kubernetes. 
    
    Ce niveau d'accès est contrôlé par :
    * le [RBAC Kubernetes](#use-kubernetes-role-based-access-control-kubernetes-rbac) (en général) ; ou 
    * en intégrant Azure RBAC à AKS pour l'autorisation Kubernetes.

    Pour savoir comment accorder des autorisations à l'API Kubernetes de façon granulaire à l'aide d'Azure RBAC, consultez [Utiliser Azure RBAC pour l'autorisation Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Utiliser des identités managées par pod

> **Conseils sur les bonnes pratiques** 
> 
> N'utilisez pas d'informations d'identification fixes au sein de pods ou d'images conteneurs, car elles présentent un risque d'exposition ou d'abus. Utilisez plutôt les *identités de pod* pour demander automatiquement l'accès à l'aide d'une solution d'identité Azure AD centrale. 

> [!NOTE]
> Les identités de pod sont conçues pour être utilisées avec des images conteneur et des pods Linux uniquement. La prise en charge des identités managées par pod pour les conteneurs Windows sera bientôt disponible.

Pour accéder à d'autres services Azure, comme Cosmos DB, Key Vault ou Stockage Blob, le pod a besoin d'informations d'identification d'accès. Vous pouvez définir des informations d'identification d'accès avec l'image conteneur ou les injecter en tant que secret Kubernetes. Dans les deux cas, vous devez les créer et les attribuer manuellement. Généralement, ces informations d'identification sont réutilisées entre les pods et ne font pas l'objet d'une rotation régulière.

Les identités managées par pod pour les ressources Azure vous permettent de demander automatiquement l'accès aux services par le biais d'Azure AD. Les identités managées par pod sont actuellement disponibles à l'état de préversion pour AKS. Pour commencer, consultez la documentation [Utiliser des identités managées par pod Azure Active Directory dans Azure Kubernetes Service (préversion)[( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity). 

Au lieu de définir manuellement des informations d'identification pour les pods, les identités managées par pod demandent un jeton d'accès en temps réel et l'utilisent uniquement pour accéder aux services qui leur sont attribués. Dans AKS, eux composants gèrent les opérations pour permettre aux pods d’utiliser des identités managées :

* Le **serveur NMI (Node Management Identity)** est un pod qui s’exécute en tant que DaemonSet sur chaque nœud du cluster AKS. Le serveur NMI écoute les demandes adressées par les pods aux services Azure.
* Le **fournisseur de ressources Azure** interroge le serveur d’API Kubernetes et recherche un mappage d’identités Azure qui correspond à un pod.

Lorsque des pods demandent l'accès à un service Azure, les règles de réseau redirigent le trafic vers le serveur NMI. 
1. Le serveur NMI :
    * Identifie les pods demandant l'accès aux services Azure en fonction de leur adresse distante ;
    * interroge le fournisseur de ressources Azure. 
1. Le fournisseur de ressources Azure vérifie les mappages d'identité Azure dans le cluster AKS.
1. Le serveur NMI demande un jeton d'accès à Azure AD en fonction du mappage d'identité du pod. 
1. Azure AD fournit un jeton d’accès au serveur NMI, qui est retourné au pod. 
    * Ce jeton d’accès peut alors être utilisé par le pod pour demander l’accès à des services dans Azure.

Dans l’exemple suivant, un développeur crée un pod qui utilise une identité managée pour demander l’accès à Azure SQL Database :

![Identités de pod permettant à un pod de demander automatiquement l’accès à d’autres services](media/operator-best-practices-identity/pod-identities.png)

1. L'opérateur du cluster crée un compte de service pour mapper les identités lorsque les pods demandent l'accès aux services.
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
