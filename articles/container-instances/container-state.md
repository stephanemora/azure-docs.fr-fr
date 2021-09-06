---
title: États d’Azure Container Instances
description: En savoir plus sur les états des opérations d’approvisionnement d’Azure Container Instances, des conteneurs et des groupes de conteneurs.
ms.author: nickoman
author: nickomang
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 0144b6ebd82c6df4c9ec501897a4315b4554103d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015153"
---
# <a name="azure-container-instances-states"></a>États d’Azure Container Instances

Azure Container Instances affiche plusieurs valeurs d’état indépendantes. Cet article répertorie ces valeurs, où qu’elles se trouvent et quoi qu’elles indiquent.

## <a name="where-to-find-state-values"></a>Où trouver les valeurs d’état

Dans le portail Azure, l’état est indiqué à différents emplacements. Toutes les valeurs d’état sont accessibles via la définition JSON de la ressource. Cette valeur peut être retrouvée sous Essentials dans le panneau Vue d’ensemble, comme indiqué ci-dessous.

:::image type="content" source="./media/container-state/provisioning-state.png" alt-text="Le panneau Vue d’ensemble du portail Azure est affiché. Le lien « Vue JSON » est mis en surbrillance.":::

L’état est également affiché dans d’autres emplacements du portail Azure. Le tableau suivant résume où les valeurs d’état peuvent être retrouvées :

|Name|Chemin JSON|Emplacement du portail Azure|
|-|-|-|
|État du groupe de conteneurs|`properties.instanceView.state`|Sous Essentials dans le panneau Vue d’ensemble|
|État du conteneur actuel|`properties.containers/initContainers[x].instanceView.currentState.state`|Dans la colonne **État** du tableau dans le panneau Conteneurs|
|État du conteneur précédent|`properties.containers/initContainers[x].instanceView.previousState.state`|Via la *Vue JSON* sous Essentials dans le panneau Vue d’ensemble|
|État de provisionnement|`properties.provisioningState`|Via la *Vue JSON* sous Essentials dans le panneau Vue d’ensemble ; corps de réponse HTTP|

## <a name="container-groups"></a>Groupes de conteneur

Cette valeur correspond à l’état du groupe de conteneurs déployé sur le serveur principal.

:::image type="content" source="./media/container-state/container-group-state.png" alt-text="Le panneau Vue d’ensemble de la ressource dans le portail Azure est affiché dans un navigateur web. Le texte « État : en cours d’exécution » est mis en surbrillance.":::

- **En cours d’exécution** : le groupe de conteneurs est en cours d’exécution et va continuer à s’exécuter jusqu’à ce qu’une action de l’utilisateur ou un arrêt provoqué par la stratégie de redémarrage se produise.

- **Arrêté** : le groupe de conteneurs a été arrêté et ne sera pas planifié pour s’exécuter sans action de l’utilisateur.

- **En attente** : le groupe de conteneurs est en attente d’initialisation (terminer l’exécution des conteneurs init, monter les volumes de fichiers Azure le cas échéant). Le conteneur continue à essayer de passer à l’état **En cours d’exécution** jusqu’à ce qu’une action de l’utilisateur (arrêter/supprimer) se produise.

- **Réussi** : le groupe de conteneurs s’est exécuté correctement. Applicable uniquement pour les stratégies de redémarrage *Jamais* et *En cas d’échec*.

- **Échec** : le groupe de conteneurs ne s’est pas exécuté correctement. Applicable uniquement avec une stratégie de redémarrage *Jamais*. Cet état indique soit une défaillance d’infrastructure (par exemple : informations d’identification du partage de fichiers Azure incorrectes), soit une défaillance de l’application utilisateur (par exemple : l’application fait référence à une variable d’environnement qui n’existe pas).

Le tableau suivant répertorie les états qui s’appliquent à un groupe de conteneurs en fonction de la stratégie de redémarrage désignée :

|Valeur|Jamais|En cas d'échec|Toujours|
|--|--|--|--|
|Exécution en cours|Oui|Oui|Oui|
|Arrêté|Oui|Oui|Oui|
|Pending|Oui|Oui|Oui|
|Opération réussie|Oui|Oui|Non|
|Échec|Oui|Non|Non|

## <a name="containers"></a>Containers

Il existe deux valeurs d’état pour les conteneurs : un état actuel et un état précédent. Dans le portail Azure, comme indiqué ci-dessous, seul l’état actuel est affiché. Toutes les valeurs d’état sont applicables pour un conteneur donné, quelle que soit la stratégie de redémarrage du groupe de conteneurs.

> [!NOTE]
> Les valeurs JSON de `currentState` et `previousState` contiennent des informations supplémentaires, telles qu’un code de sortie ou une raison, qui ne sont pas indiquées ailleurs dans le portail Azure.

:::image type="content" source="./media/container-state/container-state.png" alt-text="Le panneau Conteneurs dans le portail Azure est affiché. Une table s’affiche, et « En cours d’exécution» dans la colonne « État » est mis en surbrillance. ":::

- **En cours d’exécution** : le conteneur est en cours d’exécution.

- **Attente** : l’exécution du conteneur est en attente. Cet état indique que les conteneurs init sont toujours en cours d’exécution ou que le conteneur est en cours de sauvegarde en raison d’une boucle de blocage.

- **Terminé** : le conteneur s’est arrêté, accompagné d’une valeur de code de sortie.

## <a name="provisioning"></a>Approvisionnement

Cette valeur correspond à l’état de la dernière opération effectuée sur un groupe de conteneurs. En règle générale, cette opération est une action PUT (créer), mais il peut également s’agir d’une action POST (démarrer/redémarrer/arrêter) ou DELETE (supprimer).

> [!IMPORTANT]
> En outre, les utilisateurs ne doivent pas créer de dépendances sur des états d’approvisionnement non terminaux. Les dépendances sur des états **Réussi** et **Échec** sont acceptées.

En plus de la vue JSON, l’état d’approvisionnement peut également être retrouvé dans le [corps de la réponse de l’appel HTTP](/rest/api/container-instances/containergroups/createorupdate#response).

### <a name="create-start-and-restart-operations"></a>Opérations de création, de démarrage et de redémarrage

> [!IMPORTANT]
> Les opérations PUT (créer) sont asynchrones. La valeur retournée du corps de la réponse de l’opération PUT n’est pas l’état final. La méthode recommandée pour surveiller l’état du déploiement consiste à effectuer des appels GET ultérieurs sur le resourceId du groupe de conteneurs ou sur AsyncOperation (retourné dans les en-têtes de réponse PUT).

Ces états s’appliquent aux événements PUT (créer) et POST (démarrer/redémarrer).

- **En attente** : le groupe de conteneurs attend une configuration de l’infrastructure, telle qu’une attribution de nœud, un approvisionnement de réseau virtuel ou tout autre besoin avant d’extraire l’image utilisateur.

- **Création** : la configuration de l’infrastructure est terminée. Le groupe de conteneurs est maintenant opérationnel et reçoit les ressources dont il a besoin (montage des volumes de fichiers Azure, obtention de l’adresse IP d’entrée, etc.).

- **Réussi** : le groupe de conteneurs a réussi à ce que ses conteneurs passent à l’état En cours d’exécution et a reçu toutes les ressources dont il a besoin.

- **Non sain** : le groupe de conteneurs n’est pas sain. Si l’état est inattendu, comme en cas de défaillance d’un nœud, un travail est déclenché automatiquement pour réparer le groupe de conteneurs en le déplaçant.

- **Réparation** : le groupe de conteneurs est déplacé afin de réparer un état non sain.

- **Échec** : le groupe de conteneurs n’a pas réussi à passer à l’état d'approvisionnement **Réussi**. Une défaillance peut être due à de nombreuses raisons (profil réseau inaccessible, faible capacité dans la région désignée, consommation complète de quota d’utilisateur, délai d’expiration après 30 minutes, etc.). Vous trouverez plus d’informations sur la défaillance sous `events` dans la vue JSON.
    > [!NOTE]
    > Un état d’échec ne signifie pas que la ressource est supprimée ou qu’elle s’arrête. L’état du groupe de conteneurs indique l’état actuel du groupe. Si vous souhaitez vous assurer que le groupe de conteneurs ne s’exécute pas après un **Échec** d’approvisionnement, vous devez l’arrêter ou le supprimer.

### <a name="stop-and-delete-operations"></a>Opérations d’arrêt et de suppression

Ces valeurs s’appliquent aux événements POST (arrêter) et DELETE (supprimer).

- **Réussi** : l’opération d’arrêt ou de suppression du groupe de conteneurs s’est terminée avec succès.

- **Échec** : le groupe de conteneurs n’a pas réussi à passer à l’état d'approvisionnement **Réussi**, ce qui signifie que l’événement d’arrêt/de suppression n’a pas abouti. Vous trouverez plus d’informations sur la défaillance sous `events` dans la vue JSON.