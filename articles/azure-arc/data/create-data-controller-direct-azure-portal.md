---
title: Déployer le contrôleur de données Azure Arc à partir du portail Azure | Mode de connexion directe
description: Explique comment déployer le contrôleur de données en mode de connexion directe à partir du portail Azure.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/13/2021
ms.topic: overview
ms.openlocfilehash: c1c72ea44dc392e169f505c4820f99affdc82615
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602515"
---
#  <a name="create-azure-arc-data-controller-from-azure-portal---direct-connectivity-mode"></a>Créer le contrôleur de données Azure Arc à partir du portail Azure - Mode de connectivité directe

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Cet article décrit comment déployer le contrôleur de données Azure Arc en mode de connexion directe pendant la préversion actuelle de cette fonctionnalité. 

## <a name="complete-prerequisites"></a>Répondre aux prérequis

Avant de commencer, vérifiez que vous répondez aux prérequis dans [Déployer le contrôleur de données – Mode de connexion directe – Prérequis](create-data-controller-direct-prerequisites.md).

>[!NOTE]
>Vous devez d’abord déployer une extension Kubernetes Data Services avec Arc activé utilisant l’Azure CLI.
>
>```azurecli
>az k8s-extension create -c "{connected_cluster_name}" -g "{resource_group_name}" --name "arcdataservices" --cluster-type "connectedClusters" --extension-type "microsoft.arcdataservices" --scope "cluster" --release-namespace {namespace} --config "Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper"
>```


## <a name="deploy-azure-arc-data-controller"></a>Déployer un contrôleur de données Azure Arc

Vous pouvez lancer le processus de création d’un contrôleur Azure Arc à partir du Portail Azure de l’une des manières suivantes :

- Dans la barre de recherche du Portail Azure, recherchez « Contrôleurs de données Azure Arc », puis sélectionnez « + Créer ».
- À partir de la page Vue d’ensemble de votre cluster Kubernetes compatible avec Azure Arc,
  - sélectionnez « Extensions (préversion) » sous Paramètres.
  - Sélectionnez « Ajouter » dans la page Vue d’ensemble des extensions, puis sélectionnez « Contrôleur de données Azure Arc »
  - Sélectionnez Créer à partir de la galerie du Marketplace de contrôleur de données Azure Arc
  
L’une ou l’autre de ces actions vous permet de vous rendre sur la page Conditions préalables de la création de flux pour le contrôleur de données Azure Arc.

- Assurez-vous que l’option cluster Kubernetes compatible avec Azure Arc (mode de connectivité directe) est sélectionnée. Sélectionnez « Suivant : Détails du contrôleur de données »
- Dans la page **Détails du contrôleur de données** :
  - Sélectionnez l’abonnement Azure et le groupe de ressources où le contrôleur de données Azure ARC sera projeté.
  - Indiquer un **nom** pour le contrôleur de données
  - Sélectionnez un **emplacement personnalisé** créé au préalable ou sélectionnez « Créer » pour créer un nouvel emplacement personnalisé. Si vous choisissez de créer un emplacement personnalisé, entrez un nom pour le nouvel emplacement personnalisé, sélectionnez le cluster Kubernetes avec Azure Arc activé dans la liste déroulante, puis entrez un espace de noms à associer au nouvel emplacement personnalisé, puis sélectionnez Créer dans la fenêtre Créer un emplacement personnalisé. Apprenez-en davantage sur les [emplacements personnalisés](../kubernetes/conceptual-custom-locations.md).
  - **Configuration de Kubernetes** : sélectionnez un modèle de configuration Kubernetes qui correspond le mieux à votre distribution Kubernetes dans la liste déroulante. Si vous choisissez d’utiliser vos propres paramètres ou si vous souhaitez utiliser un profil personnalisé, sélectionnez l’option de modèle personnalisé dans la liste déroulante. Dans le panneau qui s’ouvre sur le côté droit, entrez les détails des informations d’identification de l’administrateur, les informations du référentiel, la balise d’image, la stratégie d’extraction d’images, le type d’infrastructure, les paramètres de stockage pour les données, les journaux et leurs tailles, le type de service et les ports du contrôleur et du proxy de gestion. Sélectionnez Appliquer lorsque toutes les informations requises sont fournies. Vous pouvez également choisir de télécharger votre propre fichier de modèle en sélectionnant « Télécharger un modèle (JSON) » dans la partie supérieure du panneau. Si vous utilisez des paramètres personnalisés et souhaitez télécharger une copie de ces paramètres, utilisez l’option « Télécharger ce modèle (JSON) ». En savoir plus sur les [profils de configuration personnalisés](create-custom-configuration-template.md).
  - Sélectionnez le **Type de service** correspondant à votre environnement.
  - **Compte administrateur** : entrez les informations d’identification pour la connexion et le mot de passe du contrôleur de données.
  - **Principal du service** : entrez l’ID client, l’ID de locataire et les informations de clé secrète client pour le compte du principal de service à utiliser.
  - Sélectionnez le bouton « Suivant : paramètres supplémentaires » pour continuer une fois que toutes les informations requises ont été fournies.
- Dans la page **Paramètres supplémentaires** :
  - Si vous choisissez de télécharger automatiquement vos journaux dans Azure Log Analytics, entrez l’ID de l’espace de travail Log Analytics et la clé d’accès partagé log Analytics.
  - Si vous choisissez de ne pas télécharger vos journaux vers Azure Log Analytics automatiquement, désactivez la case à cocher « Activer le chargement des journaux ».
  - Sélectionnez « Suivant : Balises » pour continuer.
- Dans la page **Balises**, entrez les noms et les valeurs de vos balises, puis sélectionnez « Suivant :Examiner + Créer ».
- Dans la page **Examiner + Créer**, affichez le résumé de votre déploiement. Vérifiez que tous les paramètres sont corrects, puis sélectionnez « Créer » pour démarrer le déploiement du contrôleur de données Azure Arc.

## <a name="monitor-the-creation-from-azure-portal"></a>Surveiller la création à partir du Portail Azure

Si vous sélectionnez le bouton « Créer » à l’étape précédente, la page Vue d’ensemble du déploiement Azure s’affiche. Elle indique la progression du déploiement contrôleur de données Azure Arc.

## <a name="monitor-the-creation-from-your-kubernetes-cluster"></a>Surveiller la création à partir de votre cluster Kubernetes

La progression du déploiement contrôleur de données Azure Arc peut être surveillée comme suit :

- Vérifiez si les CRD sont créés en exécutant ```kubectl get crd ``` à partir de votre cluster  
- Vérifiez si les espaces de noms sont créés en exécutant ```kubectl get ns``` à partir de votre cluster
- Vérifiez si l’emplacement personnalisé est créé en exécutant ```az customlocation list --resource-group <resourcegroup> -o table``` 
- Vérifiez l’état du déploiement du pod en exécutant ```kubectl get pods -ns <namespace>```

## <a name="next-steps"></a>Étapes suivantes

[Créer une instance SQL Managed Instance avec Azure Arc](create-sql-managed-instance.md)

[Créer un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](create-postgresql-hyperscale-server-group.md)
