---
title: 'Tutoriel : Router les événements de changement d’état de stratégie vers Event Grid avec Azure CLI'
description: Dans ce tutoriel, vous configurez Event Grid pour écouter les événements de changement d’état de stratégie et appeler un webhook.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: d2017b4e85c4d064fc206d9b908be126559032bd
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108752018"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Tutoriel : Router les événements de changement d’état de stratégie vers Event Grid avec Azure CLI

Dans cet article, vous allez voir comment configurer des abonnements à des événements Azure Policy afin d’envoyer des événements de changement d’état de stratégie à un point de terminaison web. Les utilisateurs Azure Policy peuvent s’abonner à des événements qui sont émis quand des modifications d’état de stratégie se produisent sur des ressources. Ces événements peuvent déclencher des webhooks, des [fonctions Azure](../../../azure-functions/index.yml), des [files d’attente de stockage Azure](../../../storage/queues/index.yml) ou tout autre gestionnaire d’événements pris en charge par [Azure Event Grid](../../../event-grid/index.yml). En règle générale, vous envoyez des événements à un point de terminaison qui traite les données d’événement et entreprend des actions. Toutefois, pour simplifier ce tutoriel, vous envoyez les événements à une application web qui collecte et affiche les messages.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

- Pour suivre ce guide de démarrage rapide, vous devez utiliser Azure CLI version 2.0.76 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

- Même si vous avez déjà utilisé Azure Policy ou Event Grid, réinscrivez leurs fournisseurs de ressources respectifs :

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les rubriques Event Grid sont des ressources Azure et doivent être placées dans un groupe de ressources Azure. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group).

L’exemple suivant crée un groupe de ressources nommé `<resource_group_name>` à l’emplacement _westus_. Remplacez `<resource_group_name>` par un nom unique pour votre groupe de ressources.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Créer une rubrique système Event Grid

Comme nous disposons maintenant d’un groupe de ressources, nous créons une [rubrique système](../../../event-grid/system-topics.md). Une rubrique système dans Event Grid représente un ou plusieurs événements publiés par les services Azure, tels qu’Azure Policy et Azure Event Hubs. Cette rubrique système utilise le type de rubrique `Microsoft.PolicyInsights.PolicyStates` pour les changements d’état Azure Policy. Remplacez `<SubscriptionID>` dans le paramètre **scope** par l’ID de votre abonnement et `<resource_group_name>` dans le paramètre **resource-group** par le groupe de ressources créé.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner à la rubrique, nous allons créer le point de terminaison pour le message de l’événement. En règle générale, le point de terminaison entreprend des actions en fonction des données d’événement. Pour simplifier ce guide de démarrage rapide, déployez une [application web prédéfinie](https://github.com/Azure-Samples/azure-event-grid-viewer) qui affiche les messages d’événement. La solution déployée comprend un plan App Service, une offre App Service Web Apps et du code source en provenance de GitHub.

Remplacez `<your-site-name>` par un nom unique pour votre application web. Le nom de l’application web doit être unique, car il fait partie de l’entrée DNS.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Le déploiement peut prendre quelques minutes. Une fois le déploiement réussi, affichez votre application web pour vérifier qu’elle s’exécute. Dans un navigateur web, accédez à : `https://<your-site-name>.azurewebsites.net`

Vous devez voir le site sans messages affichés.

## <a name="subscribe-to-the-system-topic"></a>S’abonner à la rubrique système

Vous vous abonnez à une rubrique pour communiquer à Event Grid les événements qui vous intéressent, et où les envoyer. L’exemple suivant s’abonne à la rubrique système que vous avez créée et transmet l’URL à partir de votre application web en tant que point de terminaison destiné à recevoir les notifications d’événement. Remplacez `<event_subscription_name>` par un nom pour votre abonnement aux événements. Pour `<resource_group_name>` et `<your-site-name>`, utilisez les valeurs que vous avez créées précédemment.

Le point de terminaison de votre application web doit inclure le suffixe `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Affichez à nouveau votre application web, et notez qu’un événement de validation d’abonnement lui a été envoyé. Sélectionnez l’icône en forme d’œil pour développer les données d’événements. Event Grid envoie l’événement de validation pour que le point de terminaison puisse vérifier qu’il souhaite recevoir des données d’événement. L’application web inclut du code pour valider l’abonnement.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Capture d’écran de l’événement de validation d’abonnement Event Grid dans l’application web prédéfinie":::

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une affectation de stratégie et vous attribuez la définition **Exiger une étiquette sur les groupes de ressources**. Cette définition de stratégie identifie les groupes de ressources qui ne contiennent pas l’étiquette configurée lors de l’attribution de stratégie.

Exécutez la commande suivante pour créer une attribution de stratégie dont l’étendue est définie sur le groupe de ressources destiné à contenir la rubrique Event Grid :

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

La commande précédente utilise les informations suivantes :

- **Name** : nom réel de l’attribution. Dans cet exemple, nous utilisons _requiredtags-events_.
- **DisplayName** : nom d’affichage pour l’attribution de stratégie. En l’occurrence, vous utilisez _Require tag on RG_.
- **Scope** : une étendue détermine les ressources ou le regroupement de ressources sur lequel l’attribution de stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources. Assurez-vous de remplacer &lt;scope&gt; par le nom de votre groupe de ressources. Le format de l’étendue d’un groupe de ressources est `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>`.
- **Policy** : ID de définition de la stratégie, que vous utilisez pour créer l’attribution. En l’occurrence, il s’agit de l’ID de la définition de stratégie _Require a tag on resource groups_. Pour obtenir l’ID de définition de stratégie, exécutez cette commande : `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

Après avoir créé l’attribution de stratégie, attendez qu’une notification d’événement **Microsoft.PolicyInsights.PolicyStateCreated** apparaisse dans l’application web. Au départ, le groupe de ressources que nous avons créé affiche la valeur _NonCompliant_ pour le paramètre `data.complianceState`.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Capture d’écran de l’événement de création d’état de stratégie dans l’abonnement Event Grid pour le groupe de ressources dans l’application web prédéfinie":::

> [!NOTE]
> Si le groupe de ressources hérite d’autres attributions de stratégie de la hiérarchie des groupes d’administration ou des abonnements, les événements correspondants s’affichent également. Pour vérifier que l’événement concerne l’affectation dans ce tutoriel, évaluez la propriété `data.policyDefinitionId`.

## <a name="trigger-a-change-on-the-resource-group"></a>Déclencher une modification sur le groupe de ressources

Pour rendre le groupe de ressources conforme, une étiquette portant le nom **EventTest** est requise. Ajoutez l’étiquette au groupe de ressources avec la commande suivante, en remplaçant `<SubscriptionID>` par votre ID d’abonnement et `<ResourceGroup>` par le nom du groupe de ressources :

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Après avoir ajouté l’étiquette requise au groupe de ressources, attendez qu’une notification d’événement **Microsoft.PolicyInsights.PolicyStateChanged** apparaisse dans l’application web. Développez l’événement ; `data.complianceState` a désormais pour valeur _Compliant_.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de continuer à utiliser cette application web et l’abonnement aux événements Azure Policy, ne supprimez pas les ressources créées dans cet article. Sinon, utilisez la commande suivante pour supprimer les ressources créées avec cet article.

Remplacez `<resource_group_name>` par le nom du groupe de ressources que vous avez créé plus haut.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous savez créer des rubriques et des abonnements aux événements pour Azure Policy, vous pouvez en découvrir plus sur les événements de changement d’état de stratégie et sur ce qu’Event Grid peut vous offrir :

- [Réaction aux événements de changement d’état Azure Policy](../concepts/event-overview.md)
- [Détails du schéma Azure Policy pour Event Grid](../../../event-grid/event-schema-policy.md)
- [À propos d’Event Grid](../../../event-grid/overview.md)
