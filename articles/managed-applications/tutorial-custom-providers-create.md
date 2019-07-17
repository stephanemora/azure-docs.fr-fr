---
title: Créer et utiliser un fournisseur personnalisé Azure
description: Ce tutoriel explique comment créer et utiliser un fournisseur personnalisé.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799128"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Création d’un point de terminaison RESTful pour un fournisseur personnalisé

Les fournisseurs personnalisés permettent de personnaliser les workflow dans Azure. Un fournisseur personnalisé implique qu’un contrat soit passé entre Azure et un point de terminaison (`endpoint`). Ce tutoriel vous guide tout au long de la procédure de création d’un fournisseur personnalisé. Si vous ne savez pas encore ce qu’est un fournisseur personnalisé Azure, consultez [Présentation des fournisseurs de ressources personnalisés](./custom-providers-overview.md).

Ce tutoriel comprend les sections suivantes :

- Définition d’un fournisseur personnalisé
- Définition des actions et des ressources personnalisées
- Déploiement d’un fournisseur personnalisé

Ce tutoriel s’appuie sur les tutoriels suivants :

- [Création d’un point de terminaison RESTful pour un fournisseur personnalisé](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Création d’un fournisseur personnalisé

> [!NOTE]
> Ce tutoriel n’explique pas comment créer un point de terminaison. Si vous ne disposez pas d’un point de terminaison RESTful, suivez le [tutoriel sur la création de points de terminaison RESTful](./tutorial-custom-providers-function-authoring.md).

Une fois le point de terminaison (`endpoint`) créé, vous pouvez créer un fournisseur personnalisé pour générer un contrat entre celui-ci et le point de terminaison (`endpoint`). Un fournisseur personnalisé vous permet de spécifier une liste de définitions de point de terminaison.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Propriété | Obligatoire | Description
---|---|---
Nom | *Oui* | Nom de la définition de point de terminaison. Azure expose ce nom via son API sous « /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName} »
routingType | *non* | Détermine le type de contrat passé avec le point de terminaison (`endpoint`). En l’absence de spécification, « Proxy » est spécifié par défaut.
endpoint | *Oui* | Point de terminaison vers lequel router les requêtes. Il gère la réponse, ainsi que tous les effets secondaires de la requête.

Dans ce cas, le point de terminaison (`endpoint`) est l’URL du déclencheur de la fonction Azure. `<yourapp>` (nom de l’application), `<funcname>` (nom de la fonction) et `<functionkey>` (clé de la fonction) doivent être remplacés par les valeurs de la fonction que vous avez créée.

## <a name="defining-custom-actions-and-resources"></a>Définition des actions et des ressources personnalisées

Le fournisseur personnalisé contient une liste de définitions de point de terminaison modélisée sous `actions` et `resourceTypes`. Les `actions` sont mappées sur les actions personnalisées qui sont exposées par le fournisseur personnalisé, et les `resourceTypes` (types des ressources) correspondent aux ressources personnalisées. Pour ce tutoriel, nous allons définir un fournisseur personnalisé avec un `action` nommé `myCustomAction` et un `resourceType` nommé `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

Remplacez `endpoint` par l’URL du déclencheur appartenant à la fonction créée plus tôt dans le tutoriel précédent.

## <a name="deploying-the-custom-provider"></a>Déploiement d’un fournisseur personnalisé

> [!NOTE]
> `endpoint` doit être remplacé par l’URL de la fonction.

Le fournisseur personnalisé ci-dessus peut être déployé à l’aide d’un modèle Azure Resource Manager.

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>Utilisation des actions et des ressources personnalisées

Une fois que nous avons créé le fournisseur personnalisé, nous pouvons utiliser les nouvelles API Azure. La section suivante explique comment appeler et utiliser un fournisseur personnalisé.

### <a name="custom-actions"></a>Actions personnalisées

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` et `{resourceGroupName}` doivent être remplacés par l’abonnement et le groupe de ressources dans lequel le fournisseur personnalisé a été déployé.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Paramètre | Obligatoire | Description
---|---|---
action | *Oui* | Nom de l’action définie dans le fournisseur personnalisé que vous avez créé.
ids | *Oui* | ID de ressource du fournisseur personnalisé créé.
request-body | *non* | Corps de la requête qui sera envoyé au point de terminaison (`endpoint`).

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

Aucune.

---

### <a name="custom-resources"></a>Ressources personnalisées

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` et `{resourceGroupName}` doivent être remplacés par l’abonnement et le groupe de ressources dans lequel le fournisseur personnalisé a été déployé.

Créer une ressource personnalisée :

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Paramètre | Obligatoire | Description
---|---|---
is-full-object | *Oui* | Indique que l’objet properties inclut d’autres options telles que l’emplacement, les étiquettes, une référence SKU et/ou un plan.
id | *Oui* | ID de ressource de la ressource personnalisée. Celui-ci est généré lors de la création du fournisseur personnalisé.
properties | *Oui* | Corps de la requête qui sera envoyé au point de terminaison (`endpoint`).

Supprimer une ressource personnalisée Azure :

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paramètre | Obligatoire | Description
---|---|---
id | *Oui* | ID de ressource de la ressource personnalisée. Celui-ci est généré lors de la création du fournisseur personnalisé.

Récupérer une ressource personnalisée Azure :

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paramètre | Obligatoire | Description
---|---|---
id | *Oui* | ID de ressource de la ressource personnalisée. Celui-ci est généré lors de la création du fournisseur personnalisé.

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

Exemple de modèle Azure Resource Manager :

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Paramètre | Obligatoire | Description
---|---|---
resourceTypeName | *Oui* | Nom (`name`) du type de ressource (*resourceType*) défini dans le fournisseur personnalisé.
resourceProviderName | *Oui* | Nom de l’instance de fournisseur personnalisé.
customResourceName | *Oui* | Nom de la ressource personnalisée.

---

> [!NOTE]
> Une fois que vous en avez terminé avec le déploiement et l’utilisation du fournisseur personnalisé, pensez à nettoyer les ressources créées, y compris la fonction Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les fournisseurs personnalisés. Passez au suivant pour créer un fournisseur personnalisé.

- [Guide pratique pour l’ajout d’actions personnalisées à l’API REST Azure](./custom-providers-action-endpoint-how-to.md)
- [Guide pratique pour l’ajout de ressources personnalisées à l’API REST Azure](./custom-providers-resources-endpoint-how-to.md)
