---
title: Vue d’ensemble du déploiement - Avere vFXT pour Azure
description: Vue d’ensemble du déploiement d’Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669816"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT pour Azure - Vue d’ensemble du déploiement

Cet article offre une vue d’ensemble de la procédure à suivre pour obtenir un cluster Avere vFXT pour Azure opérationnel.

Comparé au déploiement de la plupart des outils Azure, le déploiement d’un système Avere vFXT nécessite un grand nombre d’étapes. Le fait de bien comprendre l’ensemble du processus va vous aider à mesurer l’ampleur des efforts nécessaires. Une fois le système opérationnel, sa capacité à accélérer les tâches de calcul basées sur le cloud justifie pleinement les efforts engagés.

## <a name="deployment-steps"></a>Étapes du déploiement

Après avoir [planifié votre système](avere-vfxt-deploy-plan.md), vous pouvez commencer à créer votre cluster Avere vFXT. 

Commencez par créer une machine virtuelle de contrôleur de cluster. Celle-ci sert à créer le cluster vFXT.

Une fois le cluster vFXT opérationnel, vous pouvez y connecter des clients et, si nécessaire, déplacer vos données vers le nouveau conteneur de stockage d’objets blob.  

Voici une vue d’ensemble des étapes à suivre.

1. Configuration préalable requise 

   Avant de créer une machine virtuelle, vous devez créer un abonnement pour le projet Avere vFXT, configurer la propriété de l’abonnement, vérifier les quotas et demander une augmentation si nécessaire, puis accepter les conditions d’utilisation du logiciel Avere vFXT. Pour obtenir des instructions détaillées, consultez [Se préparer à la création du système Avere vFXT](avere-vfxt-prereqs.md).

1. Créer le contrôleur de cluster

   Le *contrôleur de cluster* est une machine virtuelle simple qui réside dans le même réseau virtuel que le cluster Avere vFXT. Le contrôleur crée les nœuds vFXT et forme le cluster. Il fournit également une interface de ligne de commande pour gérer le cluster pendant toute sa durée de vie.

   Si vous configurez votre contrôleur avec une adresse IP publique, il peut également faire office d’hôte de saut. Vous pouvez ainsi vous connecter au cluster Avere vFXT à partir d’un emplacement externe au réseau virtuel.

   Tous les logiciels nécessaires pour créer le cluster vFXT et gérer ses nœuds sont préinstallés sur le contrôleur de cluster.

   Pour plus d’informations, consultez [Créer la machine virtuelle du contrôleur de cluster](avere-vfxt-deploy.md#create-the-cluster-controller-vm).

1. Créer un rôle de runtime pour les nœuds de cluster 

   Azure utilise le [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) pour autoriser les machines virtuelles de nœud de cluster à effectuer certaines tâches. Par exemple, les nœuds de cluster doivent pouvoir affecter ou réaffecter des adresses IP à d’autres nœuds de cluster. Avant de créer le cluster, vous devez définir un rôle qui leur accorde les autorisations adéquates.

   Les logiciels préinstallés du contrôleur de cluster incluent un prototype de rôle que vous pouvez personnaliser. Pour obtenir des instructions, consultez [Créer le rôle d’accès du nœud de cluster](avere-vfxt-deploy.md#create-the-cluster-node-access-role).

1. Créer le cluster Avere vFXT 

   Sur le contrôleur, modifiez le script de création de cluster approprié et exécutez-le pour créer le cluster. L’article [Modifier le script de déploiement](avere-vfxt-deploy.md#edit-the-deployment-script) contient des instructions détaillées. 

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