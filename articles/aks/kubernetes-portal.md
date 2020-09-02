---
title: Accéder aux ressources Kubernetes à partir du Portail Azure (préversion)
description: Découvrez comment interagir avec les ressources Kubernetes pour gérer un cluster Azure Kubernetes service (AKS) à partir du Portail Azure.
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 08/11/2020
ms.author: lahugh
ms.openlocfilehash: 4a0acf284475f3c9119f3b9d012debad656b1faa
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661348"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Accéder aux ressources Kubernetes à partir du Portail Azure (préversion)

Le Portail Azure comprend une visionneuse de ressources Kubernetes (préversion) pour un accès facile aux ressources Kubernetes dans votre cluster Azure Kubernetes service (AKS). L’affichage des ressources Kubernetes à partir du Portail Azure réduit le basculement de contexte entre le Portail Azure et l’outil de ligne de commande `kubectl`, ce qui simplifie l’expérience d’affichage et de modification de vos ressources Kubernetes. La visionneuse de ressources comprend actuellement plusieurs types de ressources, tels que les déploiements, les pods et les jeux de réplicas.

La vue de ressources Kubernetes du Portail Azure remplace le [module complémentaire du tableau de bord AKS][kubernetes-dashboard], qui est défini pour la désapprobation.

>[!NOTE]
>La capacité n’est pas prise en charge actuellement sur les [clusters Azure Kubernetes Service privés](https://docs.microsoft.com/azure/aks/private-clusters).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Pour afficher les ressources Kubernetes dans le Portail Azure, vous avez besoin d’un cluster AKS. N’importe quel cluster est pris en charge, mais si vous utilisez l’intégration Azure Active Directory (Azure AD), votre cluster doit utiliser [Intégration d’Azure AD managé par AKS][aks-managed-aad]. Si votre cluster utilise Azure AD hérité, vous pouvez mettre à niveau votre cluster dans le portail ou à l’aide de [Azure CLI][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Afficher des ressources Kubernetes

Pour afficher les ressources Kubernetes, accédez à votre cluster AKS dans le Portail Azure. Le volet de navigation sur la gauche est utilisé pour accéder à vos ressources. Les ressources incluent :

- **Espaces de noms** affiche les espaces de noms de votre cluster. Le filtre en haut de la liste des espaces de noms fournit un moyen rapide de filtrer et d’afficher les ressources de votre espace de noms.
- **Charges de travail** affiche des informations sur les déploiements, les pods, les jeux de réplicas et les ensembles de démons déployés sur votre cluster. La capture d’écran ci-dessous montre les blocs système par défaut dans un exemple de cluster AKS.
- **Services et entrées** affiche l’ensemble du service et des ressources d’entrée de votre cluster.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Informations de pod Kubernetes affichées dans le Portail Azure." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Déployer une application

Dans cet exemple, nous allons utiliser notre exemple de cluster AKS pour déployer l’application Azure Vote à partir du [démarrage rapide AKS][portal-quickstart].

1. Sélectionnez **Ajouter** à partir de l’un des affichages de ressources (espace de noms, charges de travail ou services et entrées).
1. Collez l’YAML pour l’application Azure Vote du [démarrage rapide AKS][portal-quickstart].
1. Sélectionnez **Ajouter** en bas de l’éditeur YAML pour déployer l’application. 

Une fois le fichier YAML ajouté, la visionneuse de ressources affiche les deux services Kubernetes qui ont été créés : le service interne (azure-vote-back) et le service externe (azure-vote-front) pour accéder à l’application Azure Vote. Le service externe comprend une adresse IP externe liée, ce qui vous permet d’afficher facilement l’application dans votre navigateur.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Informations sur l’application Azure Vote affichées dans le Portail Azure." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Analyser les insights du déploiement

Les clusters AKS avec [Azure Monitor pour les conteneurs][enable-monitor] activés peuvent rapidement afficher les Insights de déploiement. À partir de l’affichage des ressources Kubernetes, les utilisateurs peuvent voir l’état en direct des déploiements individuels, y compris l’utilisation du processeur et de la mémoire, ainsi que la transition vers l’analyse Azure pour obtenir des informations plus approfondies. Voici un exemple d’insights de déploiement à partir d’un exemple de cluster AKS :

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Les insights de déploiement sont affichés dans le Portail Azure." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Modifier YAML

L’affichage des ressources Kubernetes comprend également un éditeur YAML. Un éditeur YAML intégré vous permet de mettre à jour ou de créer des services et des déploiements à partir du portail et d’appliquer les modifications immédiatement.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Éditeur YAML pour un service Kubernetes affiché dans le Portail Azure.":::

Après avoir modifié le YAML, les modifications sont appliquées en sélectionnant **Examiner + enregistrer**, en confirmant les modifications, puis en enregistrant à nouveau.

>[!WARNING]
> Il n’est pas recommandé d’effectuer des modifications de production directes par le biais de l’interface utilisateur ou de l’interface CLI, vous devez donc tirer parti des [meilleures pratiques en matière d’intégration continue et de déploiement continu (CD)](kubernetes-action.md). Les fonctionnalités de gestion de Kubernetes du Portail Azure et de l’éditeur YAML sont conçues pour l’apprentissage et le déploiement en mode Fighting de nouveaux déploiements dans un paramètre de développement et de test.

## <a name="troubleshooting"></a>Dépannage

Cette section répond à des problèmes communs et à des étapes de résolution des problèmes.

### <a name="unauthorized-access"></a>Accès non autorisé

Pour accéder aux ressources Kubernetes, vous devez avoir accès au cluster AKS, à l’API Kubernetes et aux objets Kubernetes. Assurez-vous que vous êtes un administrateur de cluster ou un utilisateur disposant des autorisations appropriées pour accéder au cluster AKS. Pour plus d’informations sur la sécurité du cluster, consultez [Options d’accès et d’identité pour AKS][concepts-identity].

### <a name="enable-resource-view"></a>Activer l’affichage des ressources

Pour les clusters existants, vous devrez peut-être activer l’affichage des ressources Kubernetes. Pour activer l’affichage des ressources, suivez les invites dans le portail de votre cluster.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Message du Portail Azure pour activer l’affichage des ressources Kubernetes." lightbox="media/kubernetes-portal/enable-resource-view.png":::

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment accéder aux ressources Kubernetes pour votre cluster AKS. Consultez [Déploiements et manifestes YAML][deployments] pour une compréhension plus approfondie des ressources de cluster et des fichiers YAML accessibles avec la visionneuse de ressources Kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
