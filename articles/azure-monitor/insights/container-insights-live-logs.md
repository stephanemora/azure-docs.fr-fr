---
title: Consulter Azure Monitor pour afficher les journaux d’activité des conteneurs en temps réel | Microsoft Docs
description: Cet article décrit la vue en temps réel des journaux d’activité de conteneur (stdout/stderr) sans l’utilisation de kubectl avec Azure Monitor pour les conteneurs.
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
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: c8baa4d2355adf99ce188d632ac50901db29a758
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997697"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Affichage des journaux d’activité de conteneur en temps réel avec Azure Monitor pour les conteneurs (préversion)
Cette fonctionnalité, qui est actuellement en préversion, fournit une vue en temps réel de vos journaux d’activité de conteneur Azure Kubernetes Service (AKS) (stdout/stderr) sans nécessiter l’exécution des commandes kubectl. Lorsque vous sélectionnez cette option, le nouveau volet apparaît au-dessous la table de données de performances de conteneurs de la vue **Conteneurs**.  Il affiche la journalisation dynamique générée par le moteur du conteneur afin de faciliter la résolution des problèmes en temps réel. **Contributeur** accès à la ressource de cluster est requis pour cette fonctionnalité soit opérationnelle.

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
        resources: ["pods/log"] 
        verbs: ["get"] 
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

2. Créez la liaison de règle de cluster en exécutant la commande suivante : `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Configurer AKS avec Azure Active Directory
AKS peut être configuré dans le but d’utiliser Azure Active Directory (AD) pour l’authentification utilisateur. Si vous effectuez cette configuration pour la première fois, consultez [Intégrer Azure Active Directory dans Azure Kubernetes Service](../../aks/azure-ad-integration.md). Au cours des étapes de création de l’[application cliente](../../aks/azure-ad-integration.md#create-client-application) et de spécification de l’**URI de redirection**, vous devez ajouter un autre URI à la liste `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>La configuration de l’authentification avec Azure Active Directory pour l’authentification unique est possible seulement pendant le déploiement initial d’un nouveau cluster AKS. Vous ne pouvez pas configurer l’authentification unique pour un cluster AKS déjà déployé.  
> 

## <a name="view-live-logs"></a>Afficher les journaux d’activité dynamiques
Lorsque vous affichez **Conteneurs**, vous pouvez **Afficher les journaux d’activité du conteneur** ou **Afficher les journaux d’activité dynamiques de conteneur**.  Lorsque vous sélectionnez **Afficher les journaux d’activité dynamiques de conteneur**, un nouveau volet apparaît au-dessous de la table de données de performances de conteneurs. Il affiche la journalisation dynamique générée par le moteur du conteneur afin de faciliter la résolution des problèmes en temps réel.  
1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Dans le menu **Microsoft Azure**, sélectionnez **Contrôler** , puis **Conteneurs**.  
3. Sélectionnez un conteneur dans la liste sous la vue **Monitored containers** (Conteneurs surveillés).  
4. Sélectionnez la vue **Conteneurs**. Le panneau de propriétés du conteneur sélectionné comporte le lien **Afficher les journaux d’activité dynamiques de conteneur**.  
5. Si le cluster AKS est configuré avec l’authentification unique à l’aide d’AAD, vous êtes invité à vous authentifier lors de la première utilisation sur cette session de navigateur. Sélectionnez votre compte et finalisez l’authentification auprès d’Azure.  

Une fois l’authentification réussie, le volet du journal dynamique s’affiche dans la section inférieure du volet central. Si l’indicateur d’état de récupération affiche une coche verte tout à droite du volet, cela signifie que les données peuvent être récupérées.
    
  ![Données récupérées sur le volet des journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-01.png)  

Dans la barre de recherche, vous pouvez filtrer les résultats par mot clé pour mettre en évidence ce texte dans le journal.   

  ![Exemple de filtre du volet des journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Pour interrompre le défilement automatique, contrôler le comportement du volet et faire défiler manuellement les nouvelles données de journal lues, cliquez sur l’option **Faire défiler**.  Pour réactiver le défilement automatique, cliquez simplement sur l’option **Faire défiler** à nouveau.  Vous pouvez aussi interrompre la récupération des données de journal en cliquant sur l’option **Suspendre**. Lorsque vous êtes prêt à reprendre la récupération, il vous suffit de cliquer sur **Lire**.  

![Affichage dynamique de l’interruption du volet de journaux d’activité dynamiques](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Étapes suivantes
- Pour continuer à découvrir comment utiliser Azure Monitor et surveiller les autres aspects de votre cluster AKS, consultez [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).
- Vue [connecter des exemples de requêtes](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples pour évaluer ou personnaliser pour génération d’alertes, la visualisation ou l’analyse de vos clusters.
