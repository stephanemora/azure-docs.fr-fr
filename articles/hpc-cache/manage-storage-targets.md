---
title: Gérer les cibles de stockage Azure HPC Cache
description: Procédures de suspension, de suppression, de suppression forcée et de vidage des cibles de stockage Azure HPC Cache, et compréhension de l’état de la cible de stockage
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: v-erkel
ms.openlocfilehash: 5b6127a43ebd93b89ea3a648533c2b739fc58691
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274288"
---
# <a name="view-and-manage-storage-targets"></a>Afficher et gérer les cibles de stockage

La page des paramètres des cibles de stockage affiche des informations sur chaque cible de stockage pour votre cache HPC et fournit des options pour gérer les cibles de stockage individuelles.

> [!TIP]
> Les instructions pour répertorier les cibles de stockage à l’aide de Azure CLI sont incluses dans l’article [Ajouter des cibles de stockage](hpc-cache-add-storage.md#view-storage-targets). Les autres actions répertoriées ici ne sont peut-être pas encore disponibles dans Azure CLI.

![Capture d’écran de la page Paramètres > Cibles de stockage dans le portail Azure. La liste contient plusieurs cibles de stockage et les en-têtes de colonne affichent le nom, le type, l’état, l’état de provisionnement, l’adresse/le conteneur et le modèle d’utilisation pour chacune d’elles.](media/storage-targets-list-states.png)

## <a name="manage-storage-targets"></a>Gérer les cibles de stockage

Vous pouvez effectuer des actions de gestion sur des cibles de stockage individuelles. Ces actions complètent les options au niveau du cache décrites dans [Gérer votre cache](hpc-cache-manage.md).

Ces contrôles peuvent vous aider à récupérer d’une situation inattendue (par exemple, une cible de stockage qui ne répond pas), ainsi que vous permettre de remplacer certaines actions automatiques du cache (comme la réécriture de fichiers modifiés dans le système de stockage à long terme).

Ouvrez la page **Cibles de stockage** sur le portail Azure. Cliquez sur l’image **...** tout à fait à droite de la liste des cibles de stockage pour ouvrir la liste des tâches.

![Capture d’écran de la page des cibles de stockage dans le portail Azure, avec le curseur positionné sur le menu affiché en cliquant sur le symbole des points de suspensions (...) tout à fait à droite de la ligne de la cible de stockage dans la liste.](media/storage-target-manage-options.png)

Les options suivantes sont disponibles :

* **Vider** – Écrire toutes les modifications mises en cache dans le stockage principal
* **Suspendre** – Arrêter temporairement le service des demandes par la cible de stockage
* **Reprendre** – Remettre en service une cible de stockage suspendue
* **Forcer la suppression** – Supprimer une cible de stockage, en ignorant certaines étapes de sécurité (**Forcer la suppression peut entraîner une perte de données**)
* **Supprimer** – Supprimer définitivement une cible de stockage

Certaines cibles de stockage disposent également d’une option d’**actualisation du DNS** dans ce menu, qui met à jour l’adresse IP de la cible de stockage à partir d’un serveur DNS personnalisé. Cette configuration est rare.

Pour plus d’informations sur ces options, lisez le reste de cet article.

### <a name="write-cached-files-to-the-storage-target"></a>Écrire les fichiers mis en cache dans la cible de stockage

L’option **Vider** indique au cache de copier immédiatement les fichiers modifiés stockés dans le cache vers le système de stockage principal. Par exemple, si vos machines clientes mettent à jour un fichier particulier à plusieurs reprises, elles sont conservées dans le cache pour un accès plus rapide, et ne sont pas écrites dans le système de stockage à long terme pendant une période allant de quelques minutes à plus d’une heure.

L’action **Vider** indique au cache d’écrire tous les fichiers dans le système de stockage.

Le cache n’accepte pas de demandes des clients visant des fichiers stockés sur cette cible de stockage tant que le vidage n’est pas terminé.

Vous pouvez utiliser cette option pour vous assurer que le stockage principal est rempli avant d’effectuer une sauvegarde, ou dans toute situation où vous souhaitez vous assurer que le stockage contient des mises à jour récentes.

Cette option s’applique principalement aux modèles d’utilisation qui incluent une mise en cache d’écriture. Pour en savoir plus sur la mise en cache de lecture et d’écriture, consultez [Comprendre les modèles d’utilisation du cache](cache-usage-models.md).

### <a name="suspend-a-storage-target"></a>Suspendre une cible de stockage

La fonctionnalité de suspension désactive l’accès client à une cible de stockage, mais ne supprime pas définitivement la cible de stockage de votre cache. Vous pouvez utiliser cette option si vous devez désactiver un système de stockage principal à des fins de maintenance, de réparation ou de remplacement.

### <a name="put-a-suspended-storage-target-back-in-service"></a>Remettre en service une cible de stockage suspendue

Utilisez l’option **Reprendre** pour annuler la suspension d’une cible de stockage.

### <a name="force-remove-a-storage-target"></a>Forcer la suppression d’une cible de stockage

> [!NOTE]
> Cette option peut entraîner une perte de données pour la cible de stockage affectée.

Si une cible de stockage ne peut pas être supprimée à l’aide d’une action de suppression normale, vous pouvez utiliser l’option **Forcer la suppression** pour la supprimer d’Azure HPC Cache.

Cette action ignore l’étape qui synchronise les fichiers dans le cache avec les fichiers dans le système de stockage principal. Il n’est nullement garanti que les modifications écrites dans le HPC Cache seront écrites dans le système de stockage principal. Il se peut donc que des modifications soient perdues si vous utilisez cette option.

Il n’est pas non plus garanti que le système de stockage principal sera accessible après sa suppression du cache.

En règle générale, la suppression forcée est utilisée uniquement quand une cible de stockage ne répond plus ou se trouve dans un état incorrect. Cette option vous permet de supprimer la cible de stockage incorrecte au lieu de devoir prendre des mesures plus drastiques.

### <a name="delete-a-storage-target"></a>Supprimer une cible de stockage

Vous pouvez utiliser le portail Azure ou Azure CLI pour supprimer une cible de stockage.

L’option de suppression normale supprime définitivement la cible de stockage du HPC Cache, mais synchronise au préalable le contenu du cache avec le système de stockage principal. Elle diffère de l’option de suppression forcée qui ne synchronise pas les données.

La suppression d’une cible de stockage a pour effet de supprimer l’association du système de stockage avec cet Azure HPC Cache, mais ne modifie pas le système de stockage principal. Par exemple, si vous avez utilisé un conteneur Stockage Blob Azure, le conteneur et son contenu existent toujours une fois que vous l’avez supprimé du cache. Vous pouvez ajouter le conteneur à un autre Azure HPC Cache, le rajouter à ce cache ou le supprimer avec le portail Azure.

Si le cache contient une grande quantité de données modifiées, la suppression d’une cible de stockage peut prendre plusieurs minutes. Attendez que l’action se termine pour vous assurer que les données sont stockées en toute sécurité dans votre système de stockage à long terme.

#### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour supprimer une cible de stockage, ouvrez la page **Cibles de stockage**. Cliquez sur « ... » en regard de la cible de stockage, puis choisissez **Supprimer** dans le menu.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

Utilisez [az hpc-cache storage-target remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) pour supprimer une cible de stockage du cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Mettre à jour l’adresse IP (configurations DNS personnalisées uniquement)

Si votre cache utilise une configuration DNS non définie par défaut, il est possible que l’adresse IP de votre cible de stockage NFS soit modifiée en raison des modifications DNS principales. Si votre serveur DNS modifie l’adresse IP du système de stockage principal, HPC Azure Cache peut perdre l’accès au système de stockage.

Dans l’idéal, vous devez utiliser le gestionnaire du système DNS personnalisé de votre cache pour planifier les mises à jour, car ces modifications rendent le stockage indisponible.

Si vous devez mettre à jour l’adresse IP fournie par le DNS d’une cible de stockage, utilisez la page **Cibles de stockage**. Cliquez sur le symbole **...** dans la colonne de droite pour ouvrir le menu contextuel. Cliquez sur **Actualiser le DNS** pour interroger le serveur DNS personnalisé afin d’obtenir une nouvelle adresse IP.

![Capture d’écran de la liste Cibles de stockage. Pour une cible de stockage, le menu « ... » dans la colonne à l’extrême droite est ouvert et ces options s’affichent : Vider, Suspendre, Actualiser le DNS, Forcer la suppression, Reprendre (cette option est désactivée) et Supprimer.](media/refresh-dns.png)

En cas de réussite, la mise à jour doit prendre moins de deux minutes. Vous ne pouvez actualiser qu’une cible de stockage à la fois. Attendez la fin de l’opération précédente avant d’en essayer une autre.

## <a name="understand-storage-target-state"></a>Comprendre l’état de la cible de stockage

La liste des cibles de stockage affiche deux types d’état : **État** et **État d'approvisionnement**.

* **État** indique l’état opérationnel de la cible de stockage. Cette valeur est mise à jour régulièrement et vous aide à comprendre si la cible de stockage est disponible pour les requêtes des clients, ainsi que les options de gestion disponibles.
* L'**État d’approvisionnement** vous indique si la dernière action d’ajout ou de modification de la cible de stockage a réussi. Cette valeur est mise à jour uniquement si vous modifiez la cible de stockage.

La valeur **État** influence les options de gestion que vous pouvez utiliser. Voici une brève explication des valeurs et de leurs effets.

* **Prêt** : la cible de stockage fonctionne normalement et est disponible pour les clients. Vous pouvez utiliser n’importe quelle option de gestion sur cette cible de stockage (à l’exception de **Reprendre**, qui est n’est valide que pour les cibles de stockage suspendues).
* **Occupé** : la cible de stockage traite une autre opération. Vous pouvez supprimer ou forcer la suppression de la cible de stockage.
* **Suspendu** : la cible de stockage a été mise hors connexion. Vous pouvez toujours vider, supprimer ou forcer la suppression de cette cible de stockage. Choisissez **Reprendre** pour remettre la cible en service.
* **Vidage** : la cible de stockage écrit des données dans le stockage principal. La cible ne peut pas traiter les requêtes du client pendant le vidage, mais elle revient automatiquement à son état précédent une fois l’écriture des données terminée.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [actions de gestion au niveau du cache](hpc-cache-manage.md)
* [Modifier une cible de stockage](hpc-cache-edit-storage.md)
