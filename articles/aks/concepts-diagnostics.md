---
title: Vue d’ensemble des diagnostics Azure Kubernetes Service (AKS)
description: Découvrez les clusters d'auto-diagnostic dans Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011556"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Vue d’ensemble des diagnostics d’Azure Kubernetes Service

La résolution des problèmes de cluster Azure Kubernetes Service (AKS) joue un rôle important en termes de maintenance de votre cluster, notamment si celui-ci exécute des charges de travail stratégiques. Diagnostics AKS est une expérience intelligente et d’autodiagnostic qui :
* vous aide à identifier et à résoudre les problèmes dans votre cluster ; 
* est natif cloud ;
* ne nécessite ni configuration ni coût de facturation supplémentaire.

Cette fonctionnalité est désormais disponible en préversion publique. 

## <a name="open-aks-diagnostics"></a>Ouvrir AKS Diagnostics

Pour accéder à AKS Diagnostics :

1. Accédez à votre cluster Kubernetes dans le [portail Azure](https://portal.azure.com).
1. Dans le volet de navigation de gauche, cliquez sur **Diagnostiquer et résoudre les problèmes** pour ouvrir AKS Diagnostics.
1. Choisissez une catégorie qui décrit le mieux le problème de votre cluster, telle que _Cluster Node Issues_, en procédant comme suit :
    * Utilisez des mots clés dans la vignette de la page d’accueil.
    * Entrez un mot clé qui décrit le mieux votre problème dans la barre de recherche.

![Page d’accueil](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Afficher un rapport de diagnostic

Après avoir cliqué sur une catégorie, vous pouvez afficher un rapport de diagnostic spécifique à votre cluster. Les rapports de diagnostic identifient intelligemment les problèmes de votre cluster avec des icônes d’état. Vous pouvez descendre dans la hiérarchie de chaque rubrique en cliquant sur **Plus d’informations** afin de voir une description détaillé de :
* Problèmes
* Actions recommandées
* Liens vers des documents utiles
* Métriques associées
* Journalisation de données 

Les rapports de diagnostic sont générés intelligemment en fonction de l’état actuel de votre cluster après exécution de différentes vérifications. En outre, les rapports de diagnostic permettent d’identifier le problème de votre cluster et de trouver les étapes suivantes à exécuter pour y remédier.

![Rapport de diagnostic](./media/concepts-diagnostics/diagnostic-report.png)

![Rapport de diagnostic développé](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster Insights (Insights de cluster)

Les vérifications de diagnostic suivantes sont disponibles dans **Insights de cluster**.

### <a name="cluster-node-issues"></a>Cluster Node Issues

Cluster Node Issues recherche les problèmes de nœud entraînant un comportement inattendu de votre cluster.

- Problèmes de préparation de nœud
- Échecs de nœud
- Ressources insuffisantes
- Configuration IP de nœud manquante
- Échecs CNI de nœud
- Nœud introuvable
- Nœud hors tension
- Échec d'authentification de nœud
- Proxy Kube de nœud obsolète

### <a name="create-read-update--delete-crud-operations"></a>Opérations de création, de lecture, de mise à jour et de surpression (CRUD)

CRUD Operations recherche les opérations CRUD entraînant des problèmes dans votre cluster.

- Erreur de l'opération de suppression de sous-réseau en cours d’utilisation
- État de l'opération de suppression du groupe de sécurité réseau
- Erreur de l’opération de suppression de la table de route en cours d’utilisation
- Erreur d'approvisionnement des ressources référencées
- Erreur de l’opération de suppression d'adresse IP publique
- Échec de déploiement en raison du quota de déploiement
- Erreur d’opération en raison de la stratégie de l’organisation
- Inscription de l'abonnement manquante
- Échec d'approvisionnement d’une extension de machine virtuelle
- Capacité du sous-réseau
- Erreur de dépassement de quota

### <a name="identity-and-security-management"></a>Gestion de la sécurité et identité

Gestion des identités et de la sécurité détecte les erreurs d’authentification et d’autorisation empêchant la communication avec votre cluster.

- Échecs d'autorisation de nœud
- Erreurs 401
- Erreurs 403

## <a name="next-steps"></a>Étapes suivantes

* Collectez les journaux pour faciliter la résolution des problèmes de votre cluster à l’aide d'[AKS Periscope](https://aka.ms/aksperiscope).

* Consultez la [section relative aux pratiques de triage](/azure/architecture/operator-guides/aks/aks-triage-practices) du Guide des opérations AKS (2e journée).

* Publiez vos questions ou vos commentaires sur [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) en ajoutant « [Diag] » dans le titre.