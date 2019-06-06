---
title: Consulter Azure Monitor pour afficher les journaux d’activité des conteneurs en temps réel | Microsoft Docs
description: Cet article décrit la vue en temps réel des fichiers journaux de conteneurs (stdout/stderr) et des événements sans utiliser kubectl avec Azure Monitor pour les conteneurs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: magoedte
ms.openlocfilehash: 8d4cc5e46066ad2f18d596d0484f62f478b4cc23
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514320"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Comment afficher les journaux et les événements en temps réel (version préliminaire)
Azure Monitor pour les conteneurs inclut une fonctionnalité qui est actuellement en version préliminaire, qui fournit un affichage en temps réel dans vos journaux de conteneurs Azure Kubernetes Service (AKS) (stdout/stderr) et les événements sans avoir à exécuter des commandes kubectl. Lorsque vous sélectionnez une option, un nouveau volet s’affiche au-dessous de la table de données de performances sur le **nœuds**, **contrôleurs**, et **conteneurs** vue. Il montre la journalisation en direct et les événements générés par le moteur de conteneur pour venir en aide à résoudre les problèmes en temps réel. 

>[!NOTE]
>**Contributeur** accès à la ressource de cluster est requis pour cette fonctionnalité soit opérationnelle.
>

Journaux en direct prennent en charge trois méthodes différentes pour contrôler l’accès aux journaux :

1. AKS sans autorisation Kubernetes RBAC activée 
2. AKS activé avec autorisation Kubernetes RBAC
3. AKS activé avec Azure Active Directory (AD) basée sur SAML pour lequel l’authentification unique sur 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes sans RBAC activé
 
Si votre cluster Kubernetes n’est pas configuré avec l’autorisation Kubernetes RBAC ni intégré dans l’authentification unique Azure AD, il est inutile de suivre ces étapes. L’autorisation Kubernetes utilisant kube-api, les autorisations en lecture seule sont requises.

## <a name="kubernetes-rbac-authorization"></a>Autorisation Kubernetes RBAC
Si vous avez activé l’autorisation Kubernetes RBAC, vous devez appliquer la liaison de rôle de cluster. Les exemples d’étapes suivants montrent comment configurer la liaison de rôle de cluster à partir de ce modèle de configuration yaml. 

1. Copiez et collez le fichier yaml, puis enregistrez-le sous le nom LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. Si vous le configurez pour la première fois, vous créez la liaison de règle de cluster en exécutant la commande suivante : `kubectl create -f LogReaderRBAC.yaml`. Si vous déjà activé la prise en charge pour les journaux en direct en version préliminaire avant que nous avons introduit les journaux des événements en direct, pour mettre à jour votre configuration, exécutez la commande suivante : `kubectl apply -f LogReaderRBAC.yml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Configurer AKS avec Azure Active Directory
AKS peut être configuré dans le but d’utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Si vous le configurez pour la première fois, consultez [intégrer Azure Active Directory avec Azure Kubernetes Service](../../aks/azure-ad-integration.md). Au cours des étapes pour créer le [application cliente](../../aks/azure-ad-integration.md#create-client-application), vous devez spécifier deux **l’URI de redirection** entrées. Les deux URI sont :

- https://ininprodeusuxbase.microsoft.com/*
- https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html  

>[!NOTE]
>La configuration de l’authentification avec Azure Active Directory pour l’authentification unique est possible seulement pendant le déploiement initial d’un nouveau cluster AKS. Vous ne pouvez pas configurer l’authentification unique pour un cluster AKS déjà déployé. Vous devez configurer l’authentification à partir de **inscription de l’application (hérité)** option dans Azure AD afin de prendre en charge l’utilisation d’un caractère générique dans l’URI et alors qu’ajouter à la liste, inscrivez-le comme une **natif** application.
> 

## <a name="view-live-logs-and-events"></a>Afficher les journaux en direct et des événements

Vous pouvez afficher les événements de journal en temps réel qu’ils sont générés par le moteur de conteneur à partir de la **nœuds**, **contrôleurs**, et **conteneurs** vue. Dans le volet Propriétés, vous sélectionnez **consulter les données actives (version préliminaire)** option et un volet est présentée ci-dessous la table de données de performances où vous pouvez afficher les journaux et les événements dans un flux continu. 

![Option de journaux en direct d’affichage du volet Propriétés de nœud](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Les messages de journal et des événements sont limitées, selon le type de ressource sélectionné dans la vue.

| Affichage | Type de ressource | Journal ou un événement | Données présentées |
|------|---------------|--------------|----------------|
| Nœuds | Nœud | Événement | Lorsqu’un nœud est sélectionné les événements ne sont pas filtrés et affichent les événements de Kubernetes à l’échelle du cluster. Titre du volet affiche le nom du cluster. |
| Nœuds | Pod | Événement | Lorsqu’un pod est sélectionné les événements sont filtrés à son espace de noms. Titre du volet affiche l’espace de noms du pod. | 
| Controllers | Pod | Événement | Lorsqu’un pod est sélectionné les événements sont filtrés à son espace de noms. Titre du volet affiche l’espace de noms du pod. |
| Controllers | Controller | Événement | Lorsqu’un contrôleur est sélectionné les événements sont filtrés à son espace de noms. Titre du volet affiche l’espace de noms du contrôleur. |
| Nœuds/contrôleurs/conteneurs | Conteneur | Journaux d’activité | Titre du volet affiche que le nom du pod le conteneur est regroupé avec. |

Si le cluster AKS est configuré avec l’authentification unique à l’aide d’AAD, vous êtes invité à vous authentifier lors de la première utilisation sur cette session de navigateur. Sélectionnez votre compte et finalisez l’authentification auprès d’Azure.  

Une fois l’authentification réussie, le volet du journal dynamique s’affiche dans la section inférieure du volet central. Si l’indicateur d’état de récupération affiche une coche verte tout à droite du volet, cela signifie que les données peuvent être récupérées.
    
  ![Données récupérées sur le volet des journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-01.png)  

Dans la barre de recherche, vous pouvez filtrer par mot clé pour mettre en évidence ce texte dans le journal ou d’un événement et dans la barre de recherche à l’extrême droite, il affiche le nombre de résultats correspond à out le filtre.   

  ![Exemple de filtre du volet des journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Pour interrompre le défilement automatique et contrôler le comportement du volet et vous permettent de faire défiler manuellement les nouvelles données à lire, cliquez sur le **défilement** option. Pour réactiver le défilement automatique, cliquez simplement sur le **défilement** option à nouveau. Vous pouvez également interrompre la récupération des données de journal ou un événement en cliquant sur le **suspendre** , lorsque vous êtes prêt à continuer, cliquez simplement sur **lire**.  

![Affichage dynamique de l’interruption du volet de journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Vous pouvez accéder aux journaux d’Azure Monitor pour afficher les journaux de conteneur historiques en sélectionnant **afficher les journaux de conteneur** dans la liste déroulante **vue dans analytique**.

## <a name="next-steps"></a>Étapes suivantes
- Pour continuer à découvrir comment utiliser Azure Monitor et surveiller les autres aspects de votre cluster AKS, consultez [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).
- Vue [connecter des exemples de requêtes](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples pour évaluer ou personnaliser pour génération d’alertes, la visualisation ou l’analyse de vos clusters.
