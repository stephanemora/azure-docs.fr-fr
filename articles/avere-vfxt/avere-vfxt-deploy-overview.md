---
title: Vue d’ensemble du déploiement - Avere vFXT pour Azure
description: Vue d’ensemble du déploiement d’Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0c61db5e34ba58bb767b0bda773a54c8e65cd404
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991799"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pour Azure - Vue d’ensemble du déploiement

Cet article offre une vue d’ensemble de la procédure à suivre pour obtenir un cluster Avere vFXT pour Azure opérationnel.

Plusieurs tâches sont nécessaires avant et après la création du cluster vFXT à partir de la Place de marché Azure. Le fait de bien comprendre l’ensemble du processus va vous aider à mesurer l’ampleur des efforts nécessaires. 

## <a name="deployment-steps"></a>Étapes du déploiement

Après avoir [planifié votre système](avere-vfxt-deploy-plan.md), vous pouvez commencer à créer votre cluster Avere vFXT. 

Dans la Place de marché Azure, un modèle Azure Resource Manager collecte les informations nécessaires et déploie automatiquement le cluster entier. 

Une fois le cluster vFXT opérationnel, vous pouvez y connecter des clients et, si nécessaire, déplacer vos données vers le nouveau conteneur de stockage d’objets blob.  

Voici une vue d’ensemble des étapes à suivre.

1. Configuration préalable requise 

   Avant de créer une machine virtuelle, vous devez créer un abonnement pour le projet Avere vFXT, configurer la propriété de l’abonnement, vérifier les quotas et demander une augmentation si nécessaire, puis accepter les conditions d’utilisation du logiciel Avere vFXT. Pour obtenir des instructions détaillées, consultez [Se préparer à la création du système Avere vFXT](avere-vfxt-prereqs.md).

1. Créer un rôle d’accès pour les nœuds de cluster

   Azure utilise le [contrôle d’accès en fonction du rôle](../role-based-access-control/index.yml) (RBAC) pour autoriser les machines virtuelles de nœud de cluster à effectuer certaines tâches. Par exemple, les nœuds de cluster doivent pouvoir affecter ou réaffecter des adresses IP à d’autres nœuds de cluster. Avant de créer le cluster, vous devez définir un rôle qui leur accorde les autorisations adéquates.

   Pour obtenir des instructions, consultez [Créer le rôle d’accès du nœud de cluster](avere-vfxt-prereqs.md#create-the-cluster-node-access-role).

   Le contrôleur de cluster utilise également un rôle d’accès, mais vous pouvez accepter le rôle par défaut, propriétaire, plutôt que de créer votre propre rôle. Si vous souhaitez créer un rôle personnalisé pour le contrôleur de cluster, consultez [Rôle d'accès du contrôleur personnalisé](avere-vfxt-controller-role.md). 

1. Créer le cluster Avere vFXT 

   Utilisez la Place de marché Azure pour créer le cluster Avere vFXT pour Azure. Un modèle collecte les informations requises et exécute des scripts pour créer le produit final.

   La création du cluster implique ces étapes, toutes exécutées via le modèle de la Place de marché : 

   * Création d'une nouvelle infrastructure réseau et de groupes de ressources, si nécessaire
   * Création d'un *contrôleur de cluster*  

     Le contrôleur de cluster est une machine virtuelle simple qui réside dans le même réseau virtuel que le cluster Avere vFXT et dispose du logiciel personnalisé requis pour créer et gérer le cluster. Le contrôleur crée les nœuds vFXT et forme le cluster. Il fournit également une interface de ligne de commande pour gérer le cluster pendant toute sa durée de vie.

     Si vous créez un nouveau réseau virtuel lors du déploiement, votre contrôleur aura une adresse IP publique. Cela signifie que le contrôleur peut servir d’ordinateur hôte saut pour la connexion au cluster à partir d’à l’extérieur du réseau virtuel.

   * Création de machines virtuelles de nœud de cluster

   * Configuration du nœud de cluster pour former un cluster, les machines virtuelles

   * Création facultative d'un nouveau conteneur d'objets blob avec configuration en tant que stockage back-end pour le cluster

1. Configurer le cluster 

   Connectez-vous à l’interface de configuration Avere vFXT (panneau de configuration Avere) pour personnaliser les paramètres du cluster. Choisissez la supervision de la prise en charge et ajoutez votre système de stockage si vous utilisez un centre de données local.

   * [Accéder au cluster vFXT](avere-vfxt-cluster-gui.md)
   * [Activer la prise en charge](avere-vfxt-enable-support.md)
   * [Configurer le stockage](avere-vfxt-add-storage.md) (si nécessaire)

1. Monter des clients

   Suivez les instructions dans [Monter le cluster Avere vFXT](avere-vfxt-mount-clients.md) pour découvrir l’équilibrage de charge et savoir comment les ordinateurs clients doivent monter le cluster.

1. Ajouter des données (si nécessaire)

   Dans la mesure où Avere vFXT est un cache multiclient scalable, la meilleure façon de déplacer les données vers un nouveau conteneur de stockage back-end consiste à utiliser une stratégie de copie de fichiers multiclient et multithread. Pour plus d’informations, consultez [Déplacement des données vers le cluster vFXT](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à [Se préparer à la création du système Avere vFXT](avere-vfxt-prereqs.md) pour effectuer les tâches préliminaires en vue de déployer Avere vFXT pour Azure. 