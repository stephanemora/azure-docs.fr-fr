---
title: Consulter Azure Monitor pour afficher les journaux d’activité des conteneurs en temps réel | Microsoft Docs
description: Cet article décrit la vue en temps réel des journaux d’activité de conteneur (stdout/stderr) et des événements sans l’utilisation de kubectl avec Azure Monitor pour les conteneurs.
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
ms.date: 06/19/2019
ms.author: magoedte
ms.openlocfilehash: 7fd9248fd38054b7f0e1fad2888d8b0d4cf2e60c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274231"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Guide pratique pour afficher les journaux et événements en temps réel (préversion)
Azure Monitor pour conteneurs inclut une fonctionnalité actuellement en préversion qui fournit une vue en temps réel de vos journaux de conteneur Azure Kubernetes Service (AKS) (stdout/stderr) et événements sans nécessiter l’exécution de commandes kubectl. Lorsque vous sélectionnez une option, un nouveau volet s’affiche sous la table de données de performances dans la vue **Nœuds**, **Contrôleurs** et **Conteneurs**. Il affiche la journalisation dynamique et les événements générés par le moteur du conteneur afin de faciliter la résolution des problèmes en temps réel.

>[!NOTE]
>Un accès **Contributeur** à la ressource de cluster est requis pour que cette fonctionnalité soit opérationnelle.
>

Les journaux d’activité dynamiques prennent en charge trois méthodes de contrôle d’accès aux journaux d’activité :

1. AKS sans autorisation Kubernetes RBAC activée
2. AKS activé avec autorisation Kubernetes RBAC
3. AKS activé avec authentification unique SAML Azure Active Directory (AD)

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

2. Si vous le configurez pour la première fois, vous créez la liaison de règle de cluster en exécutant la commande suivante: `kubectl create -f LogReaderRBAC.yaml`. Si vous avez activé précédemment la prise en charge de la préversion des journaux dynamiques avant l’introduction des journaux des événements dynamiques, pour mettre à jour votre configuration, exécutez la commande suivante : `kubectl apply -f LogReaderRBAC.yml`.

## <a name="configure-aks-with-azure-active-directory"></a>Configurer AKS avec Azure Active Directory

AKS peut être configuré dans le but d’utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Si vous effectuez cette configuration pour la première fois, voir [Intégrer Azure Active Directory dans Azure Kubernetes Service](../../aks/azure-ad-integration.md). Au cours des étapes de création de l’[application cliente](../../aks/azure-ad-integration.md#create-the-client-application), spécifiez ce qui suit :

- **URI de redirection (facultatif)**  : Il s’agit d’un type d’application **web** et la valeur de l’URL de base doit être `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Après avoir inscrit l’application, dans la page **Présentation**, sélectionnez **Authentification** dans le volet de gauche. Dans la page **Authentification**, sous **Paramètres avancés** accordez implicitement des **Jetons d’accès** et des **Jetons d’ID**, puis enregistrez votre modifications.

>[!NOTE]
>La configuration de l’authentification avec Azure Active Directory pour l’authentification unique est possible seulement pendant le déploiement initial d’un nouveau cluster AKS. Vous ne pouvez pas configurer l’authentification unique pour un cluster AKS déjà déployé.
  
>[!IMPORTANT]
>Si vous avez reconfiguré Azure AD pour l’authentification utilisateur à l’aide de l’URI mis à jour, effacez le cache de votre navigateur pour vous assurer que le jeton d’authentification mis à jour est téléchargé et appliqué.   

## <a name="view-live-logs-and-events"></a>Afficher les journaux dynamiques et les événements en direct

Vous pouvez afficher les événements de journal en temps réel à mesure qu’ils sont générés par le moteur de conteneur à partir de la vue **Nœuds**, **Contrôleurs** et **Conteneurs**. Dans le volet des propriétés, sélectionnez l’option **Afficher les données actives (préversion)** . Un volet est présenté sous le tableau de données de performances dans lequel vous pouvez afficher le journal et les événements dans un flux continu.

![Option Afficher les journaux dynamiques du volet Propriétés du nœud](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Les messages de journal et d’événement sont limités en fonction du type de ressource sélectionné dans la vue.

| Affichage | Type de ressource | Journal ou événement | Données présentées |
|------|---------------|--------------|----------------|
| Nœuds | Nœud | Événement | Quand un nœud est sélectionné, les événements ne sont pas filtrés et les événements Kubernetes à l’échelle du cluster s’affichent. Le titre du volet affiche le nom du cluster. |
| Nœuds | Pod | Événement | Quand un pod est sélectionné, les événements sont filtrés sur son espace de noms. Le titre du volet indique l’espace de nom du pod. | 
| Controllers | Pod | Événement | Quand un pod est sélectionné, les événements sont filtrés sur son espace de noms. Le titre du volet indique l’espace de nom du pod. |
| Controllers | Controller | Événement | Quand un contrôleur est sélectionné, les événements sont filtrés sur son espace de noms. Le titre du volet indique l’espace de noms du contrôleur. |
| Nœuds/contrôleurs/conteneurs | Conteneur | Journaux d’activité | Le titre du volet indique le nom du pod avec lequel le conteneur est groupé. |

Si le cluster AKS est configuré avec l’authentification unique à l’aide d’AAD, vous êtes invité à vous authentifier lors de la première utilisation sur cette session de navigateur. Sélectionnez votre compte et finalisez l’authentification auprès d’Azure.  

Une fois l’authentification réussie, le volet du journal dynamique s’affiche dans la section inférieure du volet central. Si l’indicateur d’état de récupération affiche une coche verte tout à droite du volet, cela signifie que les données peuvent être récupérées.
    
  ![Données récupérées sur le volet des journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-01.png)  

Dans la barre de recherche, vous pouvez filtrer par mot clé pour mettre en surbrillance ce texte dans le journal ou l’événement. Et dans la barre de recherche tout à fait à droite, il indique le nombre de résultats correspondant au filtre.

  ![Exemple de filtre du volet des journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Lors de l’affichage d’événements, vous pouvez également limiter les résultats à l’aide du bouton **Filtrer** à droite de la barre de recherche. Selon la ressource que vous avez sélectionnée, cela a pour effet d’afficher un pod, un espace de noms ou un cluster dans lequel opérer un choix.  

Pour interrompre le défilement automatique, contrôler le comportement du volet et faire défiler manuellement les nouvelles données lues, cliquez sur l’option **Faire défiler**. Pour réactiver le défilement automatique, cliquez simplement sur l’option **Faire défiler** à nouveau. Vous pouvez aussi suspendre la récupération des données de journal ou d’événement en cliquant sur l’option **Suspendre**. Lorsque vous êtes prêt à reprendre la récupération, cliquez simplement sur **Lire**.  

![Affichage dynamique de l’interruption du volet de journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Vous pouvez accéder aux journaux Azure Monitor pour afficher les journaux de conteneur historiques en sélectionnant **Afficher les journaux du conteneur** dans la liste déroulante **Afficher dans Analytics**.

## <a name="next-steps"></a>Étapes suivantes
- Pour continuer à découvrir comment utiliser Azure Monitor et surveiller les autres aspects de votre cluster AKS, consultez [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).
- Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.
