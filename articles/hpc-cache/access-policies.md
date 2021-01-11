---
title: Utiliser des stratégies d’accès dans Azure HPC Cache
description: Comment créer et appliquer des stratégies d’accès personnalisées pour limiter l’accès client aux cibles de stockage dans Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802408"
---
# <a name="use-client-access-policies"></a>Utiliser les stratégies d’accès client

Cet article explique comment créer et appliquer des stratégies d’accès client personnalisées pour vos cibles de stockage.

Les stratégies d’accès client contrôlent la manière dont les clients peuvent se connecter aux exportations de cible de stockage. Vous pouvez contrôler certains éléments tels que l’écrasement racine et l’accès en lecture/écriture au niveau du réseau ou de l’hôte client.

Les stratégies d’accès sont appliquées à un chemin d’accès d’espace de noms, ce qui signifie que vous pouvez utiliser différentes stratégies d’accès pour deux exportations différentes sur un système de stockage NFS.

Cette fonctionnalité est destinée aux flux de travail où vous devez contrôler la façon dont les différents groupes de clients accèdent aux cibles de stockage.

Si vous n’avez pas besoin d’un contrôle fin sur l’accès à la cible de stockage, vous pouvez utiliser la stratégie par défaut ou vous pouvez personnaliser la stratégie par défaut avec des règles supplémentaires.

## <a name="create-a-client-access-policy"></a>Création d’une stratégie d’accès stockée

Utilisez la page **Stratégies d’accès client** dans le Portail Azure pour créer et gérer des stratégies. <!-- is there AZ CLI for this? -->

[![Capture d’écran de la page des stratégies d’accès client. Plusieurs stratégies sont définies et d’autres sont développées pour montrer leurs règles](media/policies-overview.png)](media/policies-overview.png#lightbox)

Chaque stratégie est constituée de règles. Ces règles sont appliquées aux hôtes dans l’ordre, de la plus petite étendue (hôte) à la plus grande (par défaut). La première règle qui correspond à est appliquée et les règles ultérieures sont ignorées.

Pour créer une stratégie d’accès, cliquez sur le bouton **+ Ajouter une stratégie d’accès** en haut de la liste. Donnez un nom à la nouvelle stratégie d’accès et entrez au moins une règle.

![Capture d’écran du panneau de modification des stratégies d’accès avec plusieurs règles remplies. Cliquez sur OK pour enregistrer la règle.](media/add-policy.png)

Le reste de cette section décrit les valeurs que vous pouvez utiliser dans les règles.

### <a name="scope"></a>Étendue

La portée et le filtre d’adresse fonctionnent ensemble pour définir quels sont les clients affectés par la règle.

Utilisez-les pour spécifier si la règle s’applique à un seul client (hôte), à une plage d’adresses IP (réseau) ou à tous les clients (par défaut).

Sélectionnez la valeur **Étendue** correspondant à votre environnement :

* **Hôte** : la règle s’applique à un client individuel.
* **Réseau** : la règle s’applique aux clients d’une plage d’adresses IP.
* **Par défaut** : la règle s’applique à tous les clients.

Les règles de la stratégie sont évaluées dans cet ordre. Une fois qu’une demande de montage du client correspond à une règle, les autres sont ignorées.

### <a name="address-filter"></a>Filtre d’adresse

La valeur de **Filtre d’adresse** spécifie les clients qui correspondent à la règle.

Si vous définissez l’étendue sur **Hôte**, vous ne pouvez spécifier qu’une seule adresse IP dans le filtre. Pour le paramètre d’étendue **Par défaut**, vous ne pouvez pas entrer d’adresses IP dans le champ **Filtre d’adresse**, car l’étendue par défaut correspond à tous les clients.

Spécifiez l’adresse IP ou la plage d’adresses pour cette règle. Utilisez la notation CIDR (par exemple : 0.1.0.0/16) pour ajouter une plage d’adresses.

### <a name="access-level"></a>Niveau d’accès

Définissez les privilèges à accorder les clients et qui correspondent à l’étendue et au filtre.

Les options sont **lecture/écriture**, **lecture seule** ou **aucun accès**.

### <a name="suid"></a>SUID

Cochez la case **SUID** pour permettre aux fichiers stockés dans le stockage de définir des ID utilisateur lors de l’accès.

Le SUID est généralement utilisé pour augmenter temporairement les privilèges d’un utilisateur afin que cet utilisateur puisse effectuer une tâche sur ce fichier.

### <a name="submount-access"></a>Accès au sous-montage

Activez cette case à cocher pour permettre aux clients spécifiés de monter directement les sous-répertoires de cette exportation.

### <a name="root-squash"></a>Écrasement de la racine

Indiquez si vous souhaitez ou non définir l’écrasement de la racine pour les clients qui correspondent à cette règle.

Cette valeur vous permet d’autoriser l’écrasement de la racine au niveau de l’exportation de stockage. Vous pouvez également [définir l’écrasement de la racine au niveau du cache](configuration.md#configure-root-squash).

Si vous activez l’écrasement de la racine, vous devez également définir la valeur d’ID utilisateur anonyme sur l’une des options suivantes :

* **-2** (personne)
* **65534** (personne)
* **-1** (aucun accès)
* **65535** (aucun accès)
* **0** (racine sans privilège)

## <a name="update-access-policies"></a>Mettre à jour les stratégies d’accès

Vous pouvez modifier ou supprimer des stratégies d’accès à partir du tableau de la page **Stratégies d’accès client**.

Cliquez sur le nom de la stratégie pour l’ouvrir et le modifier.

Pour supprimer une stratégie, activez la case à cocher en regard de son nom dans la liste, puis cliquez sur le bouton **Supprimer** en haut de la liste. Il est impossible de supprimer la stratégie « par défaut ».

> [!NOTE]
> Vous ne pouvez pas supprimer une stratégie d’accès en cours d’utilisation. Supprimez la stratégie de tous les chemins d’accès d’espace de noms qui l’incluent avant d’essayer de la supprimer.

## <a name="next-steps"></a>Étapes suivantes

* Appliquez des stratégies d’accès aux chemins d’accès de l’espace de noms de vos cibles de stockage. Pour savoir comment faire, consultez [Configurer l’espace de noms agrégé](add-namespace-paths.md).
