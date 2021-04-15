---
title: Activer l’identité managée sur des rubriques et domaines personnalisés Azure Event Grid
description: Cet article explique comment activer une identité de service managée pour une rubrique ou un domaine personnalisés Azure Event Grid.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278216"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Attribuer une identité managée par le système à une rubrique ou un domaine personnalisés Event Grid 
Cet article explique comment activer une identité managée par le système pour une rubrique ou un domaine personnalisés Event Grid. Pour découvrir les identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Activer une identité au moment de la création

### <a name="using-azure-portal"></a>En passant par le portail Azure
Vous pouvez activer une identité attribuée par le système pour une rubrique ou un domaine personnalisés lors de leur création dans le portail Azure. L’image suivante montre comment activer une identité managée par le système pour une rubrique personnalisée. Fondamentalement, dans la page **Avancée** de l’Assistant Création de rubrique, vous sélectionnez l’option **Activer l’identité attribuée par le système**. Cette option apparaît également sur la page **Avancé** de l’Assistant Création de domaine. 

![Activer une identité lors de la création d’une rubrique personnalisée](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez également utiliser Azure CLI pour créer une rubrique ou un domaine personnalisés avec une identité affectée par le système. Utilisez la commande `az eventgrid topic create` avec le paramètre `--identity` défini sur `systemassigned`. Si vous ne spécifiez pas de valeur pour ce paramètre, la valeur par défaut `noidentity` est utilisée. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Vous pouvez également utiliser la commande `az eventgrid domain create` pour créer un domaine avec une identité managée par le système.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Activer une identité pour une rubrique ou un domaine personnalisés existants
Dans cette section, vous allez apprendre à activer une identité managée par le système pour une rubrique ou un domaine personnalisés existants. 

### <a name="using-azure-portal"></a>En passant par le portail Azure
La procédure suivante montre comment activer une identité managée par le système pour une rubrique personnalisée. Les étapes d’activation d’une identité pour un domaine sont similaires. 

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans la barre de recherche en haut, recherchez **Rubriques Event Grid**.
3. Sélectionnez la **rubrique personnalisée** pour laquelle vous souhaitez activer l’identité managée. 
4. Basculez vers l’onglet **Identité**. 
5. **Activez** le commutateur pour activer l’identité. 
1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer le paramètre. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Page identité pour une rubrique personnalisée"::: 

La procédure permettant d’activer une identité pour un domaine Event Grid est similaire.

### <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure
Utilisez la commande `az eventgrid topic update` avec `--identity` défini sur `systemassigned` pour activer une identité affectée par le système pour une rubrique personnalisée existante. Si vous souhaitez désactiver l’identité, spécifiez la valeur `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

La commande de mise à jour d’un domaine existant est similaire (`az eventgrid domain update`).


## <a name="next-steps"></a>Étapes suivantes
Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus). Pour obtenir des instructions détaillées, consultez [Accorder à une identité managée l’accès à une destination Event Grid](add-identity-roles.md). 
