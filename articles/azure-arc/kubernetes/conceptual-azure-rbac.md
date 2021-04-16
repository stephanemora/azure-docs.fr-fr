---
title: RBAC Azure – Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article fournit une vue d’ensemble conceptuelle de la fonctionnalité RBAC Azure sur Kubernetes avec Azure Arc
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450716"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>RBAC Azure sur Kubernetes avec Azure Arc

Les types d’objets Kubernetes [ClusterRoleBinding et RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) permettent de définir l’autorisation dans Kubernetes en mode natif. Avec RBAC Azure, vous pouvez utiliser Azure Active Directory (Azure AD) et des attributions de rôles dans Azure pour contrôler les vérifications des autorisations sur le cluster.

Avec cette fonctionnalité, tous les avantages des attributions de rôles Azure, tels que les journaux d’activité présentant toutes les modifications apportées par le RBAC Azure à une ressource Azure, deviennent désormais applicables à votre cluster Kubernetes avec Azure Arc.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architecture – RBAC Azure sur Kubernetes avec Azure Arc

[ ![Architecture du RBAC Azure](./media/conceptual-azure-rbac.png) ](./media/conceptual-azure-rbac.png#lightbox)

Pour router tous les contrôles d’accès d’autorisation vers le service d’autorisation dans Azure, un serveur webhook ([guard](https://github.com/appscode/guard)) est déployé sur le cluster.

L’`apiserver` du cluster est configuré pour utiliser une [authentification par jeton de webhook](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) et une [autorisation webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) afin que les demandes `TokenAccessReview` et `SubjectAccessReview` soient routées vers le serveur webhook guard. Les demandes `TokenAccessReview` et `SubjectAccessReview` sont déclenchées par des demandes de ressources Kubernetes adressées à l’`apiserver` .

Le serveur guard effectue ensuite un appel `checkAccess` sur le service d’autorisation dans Azure pour voir si l’entité Azure AD demandeuse a accès à la ressource concernée. 

S’il existe un rôle dans une attribution autorisant cet accès, une réponse `allowed` est envoyée au serveur guard par le service d’autorisation. Le serveur guard, à son tour, envoie une réponse `allowed` à l’`apiserver`, ce qui permet à l’entité appelante d’accéder à la ressource Kubernetes demandée.


S’il n’existe pas de rôle dans l’attribution autorisant cet accès, une réponse `denied` est envoyée au serveur guard par le service d’autorisation. Le serveur guard envoie une réponse `denied` à l’`apiserver`, notifiant à l’entité appelante une erreur 403 (interdite) sur la ressource demandée.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* [Configurez un RBAC Azure](./azure-rbac.md) sur votre cluster Kubernetes avec Azure Arc.