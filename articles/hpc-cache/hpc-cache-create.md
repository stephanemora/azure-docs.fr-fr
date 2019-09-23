---
title: Créer un cache Azure HPC Cache
description: Comment créer une instance de cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: e1b69f17d964647944f23f4d16a0a1a5f112b60d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037062"
---
# <a name="create-an-azure-hpc-cache"></a>Créer un cache Azure HPC Cache

Utilisez le portail Azure pour créer votre cache.

![capture d’écran de la vue d’ensemble du cache dans le portail Azure, avec le bouton Créer en bas](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Définir les détails de base

![capture d’écran de la page Détails du projet dans le portail Azure](media/hpc-cache-create-basics.png)

Dans **Détails du projet**, sélectionnez l’abonnement et le groupe de ressources qui hébergeront le cache Azure HPC Cache. Assurez-vous que l’abonnement figure dans la liste d’[accès à la préversion](hpc-cache-prereqs.md#azure-subscription).

Dans **Détails sur le service**, définissez le nom du cache et les autres attributs suivants :

* Emplacement : sélectionnez l’une des [régions prises en charge](hpc-cache-overview.md#region-availability).
* Réseau virtuel : vous pouvez sélectionner un réseau virtuel existant ou en créer un.
* Sous-réseau : choisissez ou créez un sous-réseau avec au moins 64 adresses IP (/24) qui sera utilisé uniquement pour le cache Azure HPC Cache.

## <a name="set-cache-capacity"></a>Définir la capacité du cache
<!-- change link in GUI -->

Dans la page **Cache**, vous devez définir la capacité de votre cache Azure HPC Cache. Cette valeur détermine la quantité de données que votre cache peut contenir et la vitesse à laquelle il peut traiter les demandes des clients. Après la période de préversion publique, la capacité aura également une incidence sur le coût du cache.

La capacité du cache est mesurée en opérations d’entrée/de sortie par seconde (E/S par seconde). Choisissez la capacité en définissant les deux valeurs suivantes :

* Taux de transfert de données maximal pour le cache (débit), en Go/seconde
* Quantité de stockage allouée aux données mises en cache, en To

Choisissez l’une des valeurs de débit et des tailles de stockage du cache disponibles. La capacité d’E/S par seconde est calculée et affichée sous les sélecteurs de valeurs.

Gardez à l’esprit que le taux de transfert de données réel dépend de la charge de travail, des vitesses réseau et du type de cible de stockage. Si un fichier n’est pas dans le cache ou s’il est marqué comme obsolète, le service utilise du débit pour l’extraire du stockage back-end. La valeur que vous choisissez définit le débit maximal pour l’intégralité du cache, et il n’est pas disponible dans sa totalité pour les demandes des clients.

Pour le stockage du cache, Azure HPC Cache gère les fichiers qui sont mis en cache et préchargés afin d’optimiser les taux de réussite du cache. Le contenu du cache est évalué en permanence et les fichiers sont déplacés vers un stockage à long terme lorsqu’ils sont moins fréquemment sollicités. Choisissez une taille de stockage du cache qui peut contenir facilement l’ensemble actif de fichiers de travail avec de l’espace supplémentaire pour les métadonnées et d’autres surcharges.

![capture d’écran de la page de dimensionnement du cache](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Ajouter des cibles de stockage

Les cibles de stockage sont le stockage back-end à long terme pour le contenu de votre cache.

Vous pouvez définir des cibles de stockage lors de la création du cache, mais vous pouvez également les ajouter par la suite à l’aide du lien figurant dans la section **Configurer** de la page de votre cache dans le portail.

![capture d’écran de la page des cibles de stockage](media/hpc-cache-storage-targets-pop.png)

Cliquez sur le lien **Add storage target** (Ajouter une cible de stockage) pour définir vos systèmes de stockage back-end. Le stockage peut correspondre à des conteneurs d’objets blob Azure ou des systèmes NFS locaux.

Vous pouvez définir jusqu’à dix cibles de stockage différentes.

Des instructions pas à pas sur l’ajout d’une cible de stockage sont incluses dans [Ajouter du stockage](hpc-cache-add-storage.md). La procédure est différente pour le stockage d’objets blob ou pour les exportations NFS.

Voici quelques conseils : 

* Pour les deux types de stockage, vous devez spécifier le mode de recherche du système de stockage back-end (une adresse NFS ou un nom de conteneur d’objets blob) et le chemin de l’espace de noms orienté client.

* Lorsque vous créez une cible de stockage d’objets blob, assurez-vous que le cache dispose des autorisations d’accès au compte de stockage, comme décrit dans [Ajouter les rôles de contrôle d’accès](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Si vous ne savez pas si la configuration du rôle va réussir, créez d’abord le cache, puis ajoutez le stockage d’objets blob par la suite.

* Lorsque vous créez une cible de stockage NFS, spécifiez un [modèle d’utilisation](hpc-cache-add-storage.md#choose-a-usage-model). Le paramètre de modèle d’utilisation aide le cache à optimiser votre workflow.

## <a name="add-resource-tags-optional"></a>Ajouter des balises de ressources (facultatif)

La page **Balises** vous permet d’ajouter des [balises de ressources](https://go.microsoft.com/fwlink/?linkid=873112) à votre cache Azure HPC Cache. 

## <a name="finish-creating-the-cache"></a>Terminer la création du cache

Après avoir configuré le nouveau cache, cliquez sur l’onglet **Vérifier + créer**. Le portail valide vos choix et vous permet de les passer en revue. Si tout est correct, cliquez sur **Créer**. 

La création du cache prend environ 10 minutes. Vous pouvez suivre la progression dans le panneau de notifications du portail Azure. 

![capture d’écran des pages de création de cache « déploiement en cours » et « notifications » sur le portail](media/hpc-cache-deploy-status.png)

À l’issue de l’étape de création, une notification s’affiche avec un lien vers la nouvelle instance Azure HPC Cache, et le cache apparaît dans la liste **Ressources** de votre abonnement. 

![capture d’écran de l’instance de cache Azure HPC Cache dans le portail Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois votre cache affiché dans la liste **Ressources**, vous pouvez le monter pour l’accès client, l’utiliser pour déplacer vos données de plage de travail vers une nouvelle cible de stockage d’objets blob Azure ou définir des sources de données supplémentaires.

* [Monter le cache Azure HPC Cache](hpc-cache-mount.md)
* [Déplacer les données dans le stockage d’objets blob Azure pour le cache Azure HPC Cache](hpc-cache-ingest.md)
* [Ajouter des cibles de stockage](hpc-cache-add-storage.md)
