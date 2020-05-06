---
title: Créer un cache Azure HPC Cache
description: Comment créer une instance de cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: 4ff31ca6a171beece1672802367f08768676efbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195007"
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
* Sous-réseau : choisissez ou créez un sous-réseau avec au moins 64 adresses IP (/24). Ce sous-réseau doit être utilisé uniquement pour cette instance Azure HPC Cache.

## <a name="set-cache-capacity"></a>Définir la capacité du cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Dans la page **Cache**, vous devez définir la capacité de votre cache. Les valeurs définies ici déterminent la quantité de données que votre cache peut contenir et la rapidité avec laquelle il peut traiter les requêtes des clients.

La capacité affecte également le coût du cache.

Choisissez la capacité en définissant les deux valeurs suivantes :

* Taux de transfert de données maximal pour le cache (débit), en Go/seconde
* Quantité de stockage allouée aux données mises en cache, en To

Choisissez l’une des valeurs de débit et des tailles de stockage du cache disponibles.

Gardez à l’esprit que le taux de transfert de données réel dépend de la charge de travail, des vitesses réseau et du type de cible de stockage. Les valeurs que vous choisissez définissent le débit maximal pour l’ensemble du système de mise en cache. Toutefois, certaines d’entre elles sont utilisées pour les tâches de surcharge. Par exemple, si un client demande un fichier qui n’est pas déjà stocké dans le cache, ou si le fichier est marqué comme étant obsolète, votre cache utilise une partie de son débit pour l’extraire du stockage back-end.

Azure HPC Cache gère les fichiers mis en cache et préchargés pour maximiser les taux de réussite du cache. Le contenu du cache est évalué en permanence, et les fichiers sont déplacés vers un stockage à long terme quand ils sont moins fréquemment sollicités. Choisissez une taille de stockage du cache qui peut contenir facilement l’ensemble actif de fichiers de travail ainsi que de l’espace supplémentaire pour les métadonnées et d’autres surcharges.

![capture d’écran de la page de dimensionnement du cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Activer le chiffrement Azure Key Vault (facultatif)

Si votre cache se trouve dans une région qui prend en charge les clés de chiffrement gérées par le client, la page **Clés de chiffrement de disque** s’affiche entre les onglets **Cache** et **Balises**. Au moment de la publication, cette option est prise en charge dans les régions USA Est, USA Centre Sud et USA Ouest 2.

Si vous souhaitez gérer les clés de chiffrement utilisées avec votre stockage de cache, fournissez vos informations Azure Key Vault sur la page **Clés de chiffrement de disque**. Le coffre de clés doit se trouver dans la même région et dans le même abonnement que le cache.

Vous pouvez ignorer cette section si vous n’avez pas besoin de clés gérées par le client. Par défaut, Azure chiffre les données avec des clés gérées par Microsoft. En savoir plus sur le [chiffrement de stockage Azure](../storage/common/storage-service-encryption.md).

> [!NOTE]
>
> * Après avoir créé le cache, vous ne pouvez plus passer d’une clé gérée par Microsoft à une clé gérée par le client.
> * Une fois le cache créé, vous devez l’autoriser à accéder au coffre de clés. Cliquez sur le bouton **Activer le chiffrement** dans la page **Vue d’ensemble** du cache pour activer le chiffrement. Effectuez cette étape dans les 90 minutes qui suivent la création du cache.
> * Les caches de disque sont créés après cette autorisation. Cela signifie que le temps de création initial du cache est court, mais que le cache ne sera pas prêt à être utilisé pendant dix minutes ou plus après que vous en aurez autorisé l’accès.

Pour obtenir une explication complète du processus de chiffrement avec clé gérée par le client, lisez [Utiliser des clés de chiffrement gérées par le client pour Azure HPC Cache](customer-keys.md).

![capture d’écran de la page des clés de chiffrement avec l’option « gérée par le client » sélectionnée et les champs du coffre de clés affichés](media/create-encryption.png)

Sélectionnez **Gérée par le client** pour choisir le chiffrement avec clé gérée par le client. Les champs de spécification du coffre de clés s’affichent. Sélectionnez le coffre Azure Key Vault à utiliser, puis sélectionnez la clé et la version à utiliser pour ce cache. La clé doit être une clé RSA 2048 bits. Vous pouvez créer un coffre de clés, une clé ou une version de clé à partir de cette page.

Après avoir créé le cache, vous devez l’autoriser à utiliser le service de coffre de clés. Pour plus d’informations, consultez [Autoriser le chiffrement Azure Key Vault à partir du cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache).

## <a name="add-resource-tags-optional"></a>Ajouter des balises de ressources (facultatif)

La page **Balises** vous permet d’ajouter des [balises de ressources](https://go.microsoft.com/fwlink/?linkid=873112) à votre instance Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Terminer la création du cache

Après avoir configuré le nouveau cache, cliquez sur l’onglet **Vérifier + créer**. Le portail valide vos choix et vous permet de les passer en revue. Si tout est correct, cliquez sur **Créer**.

La création du cache prend environ 10 minutes. Vous pouvez suivre la progression dans le panneau de notifications du portail Azure.

![capture d’écran des pages de création de cache « déploiement en cours » et « notifications » sur le portail](media/hpc-cache-deploy-status.png)

À l’issue de l’étape de création, une notification s’affiche avec un lien vers la nouvelle instance Azure HPC Cache, et le cache apparaît dans la liste **Ressources** de votre abonnement.

![capture d’écran de l’instance de cache Azure HPC Cache dans le portail Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Si votre cache utilise des clés de chiffrement gérées par le client, le cache peut apparaître dans la liste des ressources avant que l’état du déploiement ne devienne Terminé. Dès que l’état du cache est **En attente de la clé**, vous pouvez [l’autoriser](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) à utiliser le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre cache apparaît dans la liste **Ressources**, vous pouvez passer à l’étape suivante.

* [Définissez des cibles de stockage](hpc-cache-add-storage.md) pour permettre à votre cache d’accéder à vos sources de données.
* Si vous utilisez des clés de chiffrement gérées par le client, vous devez [autoriser le chiffrement Azure Key Vault](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) à partir de la page de présentation du cache pour terminer la configuration de ce dernier. Vous devez effectuer cette étape avant de pouvoir ajouter un stockage. Pour plus d’informations, consultez [Utiliser des clés de chiffrement gérées par le client](customer-keys.md).
