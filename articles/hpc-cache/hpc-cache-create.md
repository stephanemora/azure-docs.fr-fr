---
title: Créer un cache Azure HPC Cache
description: Comment créer une instance de cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 793a80e7019e72c1cb3087da02d5642639cb8d5e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647154"
---
# <a name="create-an-azure-hpc-cache"></a>Créer un cache Azure HPC Cache

Utilisez le portail Azure pour créer votre cache.

![capture d’écran de la vue d’ensemble du cache dans le portail Azure, avec le bouton Créer en bas](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Définir les détails de base

![capture d’écran de la page Détails du projet dans le portail Azure](media/hpc-cache-create-basics.png)

Dans **Détails du projet**, sélectionnez l’abonnement et le groupe de ressources qui doit héberger le cache. Assurez-vous que l'abonnement figure dans la liste d'[accès](hpc-cache-prereqs.md#azure-subscription).

Dans **Détails sur le service**, définissez le nom du cache et les autres attributs suivants :

* Emplacement : sélectionnez l’une des [régions prises en charge](hpc-cache-overview.md#region-availability).
* Réseau virtuel : vous pouvez sélectionner un réseau virtuel existant ou en créer un.
* Sous-réseau – Choisissez ou créez un sous-réseau avec au moins 64 adresses IP (/24) qui sera utilisé uniquement pour cette instance Azure HPC Cache.

## <a name="set-cache-capacity"></a>Définir la capacité du cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Dans la page **Cache**, vous devez définir la capacité de votre cache. Les valeurs définies ici déterminent la quantité de données que votre cache peut contenir et la rapidité avec laquelle il peut traiter les requêtes des clients.

La capacité affecte également le coût du cache.

Choisissez la capacité en définissant les deux valeurs suivantes :

* Taux de transfert de données maximal pour le cache (débit), en Go/seconde
* Quantité de stockage allouée aux données mises en cache, en To

Choisissez l’une des valeurs de débit et des tailles de stockage du cache disponibles.

Gardez à l’esprit que le taux de transfert de données réel dépend de la charge de travail, des vitesses réseau et du type de cible de stockage. Les valeurs que vous choisissez définissent le débit maximal pour l’ensemble du système de mise en cache. Toutefois, certaines d’entre elles sont utilisées pour les tâches de surcharge. Par exemple, si un client demande un fichier qui n’est pas déjà stocké dans le cache, ou si le fichier est marqué comme étant obsolète, votre cache utilise une partie de son débit pour l’extraire du stockage back-end.

Azure HPC Cache gère les fichiers mis en cache et préchargés pour maximiser les taux de réussite du cache. Le contenu du cache est évalué en permanence et les fichiers sont déplacés vers un stockage à long terme lorsqu’ils sont moins fréquemment sollicités. Choisissez une taille de stockage du cache qui peut contenir facilement l’ensemble actif de fichiers de travail avec de l’espace supplémentaire pour les métadonnées et d’autres surcharges.

![capture d’écran de la page de dimensionnement du cache](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Ajouter des balises de ressources (facultatif)

La page **Balises** vous permet d’ajouter des [balises de ressources](https://go.microsoft.com/fwlink/?linkid=873112) à votre instance Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Terminer la création du cache

Après avoir configuré le nouveau cache, cliquez sur l’onglet **Vérifier + créer**. Le portail valide vos choix et vous permet de les passer en revue. Si tout est correct, cliquez sur **Créer**.

La création du cache prend environ 10 minutes. Vous pouvez suivre la progression dans le panneau de notifications du portail Azure.

![capture d’écran des pages de création de cache « déploiement en cours » et « notifications » sur le portail](media/hpc-cache-deploy-status.png)

À l’issue de l’étape de création, une notification s’affiche avec un lien vers la nouvelle instance Azure HPC Cache, et le cache apparaît dans la liste **Ressources** de votre abonnement.
<!-- double check on notification -->

![capture d’écran de l’instance de cache Azure HPC Cache dans le portail Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre cache apparaît dans la liste **Ressources**, définissez des cibles de stockage pour permettre au cache d’accéder à vos sources de données.

* [Ajouter des cibles de stockage](hpc-cache-add-storage.md)
