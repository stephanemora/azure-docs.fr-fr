---
title: Webhooks Azure Container Registry
description: Découvrez comment utiliser des webhooks pour déclencher des événements quand certaines actions sont exécutées dans l’un des dépôts de votre registre.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 0a3d2d0e858dc052095c0a58287970d10c06f0ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099846"
---
# <a name="using-azure-container-registry-webhooks"></a>Utilisation de webhooks Azure Container Registry

Un registre Azure Container Registry stocke et gère des images conteneurs Docker privé, de la même manière qu’un Hub Docker stocke des images de Docker public. Il peut également héberger des référentiels pour [Helm graphiques](container-registry-helm-repos.md) mettre en forme un empaquetage (version préliminaire), pour déployer des applications sur Kubernetes. Vous pouvez utiliser des webhooks pour déclencher des événements quand certaines actions sont exécutées dans l’un des dépôts de votre registre. Les webhooks peuvent répondre à des événements au niveau du registre ou être réduits à une balise de dépôt spécifique.

Pour plus d’informations sur les requêtes de webhook, consultez la [référence de schéma du webhook Azure Container Registry](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Conditions préalables

* Azure Container Registry : créez un Registre de conteneur dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md). Le [références SKU Azure Container Registry](container-registry-skus.md) possèdent des quotas différents webhooks.
* Interface CLI Docker : pour configurer votre ordinateur local comme hôte Docker et accéder aux commandes de l’interface CLI Docker, installez le [moteur Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Créer un webhook - portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez au registre de conteneurs dans lequel vous souhaitez créer un webhook.
1. Sous **Services**, sélectionnez **Webhooks**.
1. Dans la barre d’outils de webhook, sélectionnez **Ajouter**.
1. Remplissez le formulaire *Créer un webhook* avec les informations suivantes :

| Valeur | Description |
|---|---|
| Nom | Nom que vous voulez donner au webhook. Il peut contenir uniquement des lettres et des chiffres et doit être de 5 à 50 caractères. |
| URI de service | URI auquel le webhook doit envoyer des notifications POST. |
| En-têtes personnalisés | En-têtes que vous souhaitez passer avec la demande POST. Ils doivent être au format « clé : valeur ». |
| Actions de déclencheur | Actions qui déclenchent le webhook. Les actions incluent des push de l’image, suppression de l’image, push de graphique Helm, suppression du graphique Helm et mise en quarantaine de l’image. Vous pouvez choisir une ou plusieurs actions pour déclencher le webhook. |
| Statut | État du webhook après sa création. Il est activé par défaut. |
| Scope | Étendue à laquelle le webhook fonctionne. Si non spécifié, l’étendue concerne tous les événements dans le Registre. Il peut être spécifié pour un dépôt ou une balise en utilisant le format « dépôt : balise » ou « dépôt : * » pour toutes les balises sous un référentiel. |

Exemple de formulaire webhook :

![Interface utilisateur de création de webhook ACR dans le portail Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Créer un webhook - Azure CLI

Pour créer un webhook à l’aide d’Azure CLI, utilisez la commande [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). La commande suivante crée un webhook pour tous les événements de suppression dans le Registre d’images *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Tester un webhook

### <a name="azure-portal"></a>Portail Azure

Avant d’utiliser le webhook, vous pouvez la tester avec le **Ping** bouton. La commande Ping envoie une demande POST générique au point de terminaison spécifié et enregistre la réponse. Cette fonctionnalité peut vous aider à vérifier que vous avez correctement configuré le webhook.

1. Sélectionnez le webhook à tester.
2. Dans la barre d’outils supérieure, sélectionnez **Ping**.
3. Vérifiez la réponse du point de terminaison dans la colonne **État HTTP**.

![Interface utilisateur de création de webhook ACR dans le portail Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Pour tester un webhook d’ACR avec Azure CLI, utilisez la commande [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Pour afficher les résultats, utilisez la commande [az acr webhook list-events](/cli/azure/acr/webhook).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Supprimer un webhook

### <a name="azure-portal"></a>Portail Azure

Vous pouvez supprimer chaque webhook en le sélectionnant, puis en appuyant sur le bouton **Supprimer** dans le portail Azure.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="webhook-schema-reference"></a>Référence de schéma de webhook

Pour plus d’informations sur le format et les propriétés des charges utiles d’événement JSON émises par Azure Container Registry, consultez la référence de schéma de webhook :

[Référence de schéma de webhook Azure Container Registry](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Événements Event Grid

Outre les événements de webhook du registre natif abordés dans cet article, Azure Container Registry peut émettre des événements vers Event Grid :

[Démarrage rapide : Envoyer des événements de registre de conteneurs à Event Grid](container-registry-event-grid-quickstart.md)
