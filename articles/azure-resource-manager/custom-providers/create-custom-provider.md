---
title: Créer un fournisseur de ressources
description: Décrit comment créer un fournisseur de ressources et déployer ses types de ressources personnalisés.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648506"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Démarrage rapide : Créer un fournisseur personnalisé et déployer des ressources personnalisées

Dans ce guide de démarrage rapide, vous allez créer votre propre fournisseur de ressources et déployer des types de ressources personnalisés pour celui-ci. Pour plus d’informations sur les fournisseurs personnalisés, consultez [Présentation de la préversion d’Azure Custom Providers](overview.md).

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce guide, vous devez appeler des opérations REST. Il existe [différentes façons d’envoyer des demandes REST](/rest/api/azure/). Si vous ne disposez pas d’un outil pour les opérations REST, installez [ARMClient](https://github.com/projectkudu/ARMClient). Il s’agit d’un outil de ligne de commande open source qui simplifie l’appel de l’API Azure Resource Manager, est nécessaire.

## <a name="deploy-custom-provider"></a>Déployer un fournisseur personnalisé

Pour configurer le fournisseur personnalisé, vous devez déployer un [exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) sur votre abonnement Azure.

Après avoir déployé le modèle, votre abonnement affiche les ressources suivantes :

* Une application de fonction avec les opérations pour les ressources et les actions.
* Un compte de stockage pour stocker les utilisateurs créés via le fournisseur personnalisé.
* Un fournisseur personnalisé qui définit les types de ressources personnalisées et les actions. Il utilise le point de terminaison de l’application de fonction pour envoyer les demandes.
* Des ressources personnalisées provenant du fournisseur personnalisé.

Pour déployer le fournisseur personnalisé avec PowerShell, utilisez :

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Vous pouvez également déployer la solution à l’aide du bouton suivant :

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Afficher le fournisseur personnalisé et la ressource

Dans le portail, le fournisseur personnalisé est un type de ressource masqué. Pour confirmer le déploiement du fournisseur de ressources, accédez au groupe de ressources. Sélectionnez l’option **Afficher les types masqués**.

![Afficher les types de ressources masqués](./media/create-custom-provider/show-hidden.png)

Pour afficher le type de ressource personnalisé que vous avez déployé, utilisez l’opération GET sur votre type de ressource.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Avec ARMClient, utilisez :

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Vous recevez la réponse :

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Appeler une action

Votre fournisseur personnalisé propose également une action nommée **ping**. Le code qui traite la demande est implémenté dans l’application de fonction. L’action ping répond par un message d’accueil.

Pour envoyer une demande ping, utilisez l’opération POST sur votre fournisseur personnalisé.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Avec ARMClient, utilisez :

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Vous recevez la réponse :

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Créer un type de ressource

Pour créer le type de ressource personnalisé, vous pouvez déployer la ressource dans un modèle. Cette approche est illustrée dans le modèle que vous avez déployé dans ce guide. Vous pouvez également envoyer une demande PUT pour le type de ressource.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Avec ARMClient, utilisez :

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Vous recevez la réponse :

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour une introduction aux fournisseurs personnalisés, consultez [Présentation de la préversion d’Azure Custom Providers](overview.md).
