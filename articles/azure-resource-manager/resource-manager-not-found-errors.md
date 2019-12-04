---
title: Erreurs liées à des ressources introuvables
description: Décrit comment résoudre les erreurs liées à des ressources introuvables quand vous déployez des ressources avec un modèle Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.openlocfilehash: c6dbe0e494e59017cda166c0785a187ab07c3b54
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150597"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Résoudre les erreurs de ressources Azure introuvables

Cet article décrit les erreurs que vous pouvez observer lorsqu’une ressource est introuvable pendant le déploiement.

## <a name="symptom"></a>Symptôme

Lorsque votre modèle inclut le nom d’une ressource qui ne peut pas être résolue, vous recevez une erreur similaire à celle-ci :

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si vous utilisez les fonctions [reference](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez l’erreur suivante :

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Cause :

Resource Manager a besoin de récupérer les propriétés d’une ressource, mais ne peut pas identifier la ressource dans votre abonnement.

## <a name="solution-1---set-dependencies"></a>Solution 1 : Définir des dépendances

Si vous souhaitez déployer la ressource manquante dans le modèle, vérifiez si vous devez ajouter une dépendance. Resource Manager optimise le déploiement en créant des ressources en parallèle, quand cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** de votre modèle. Par exemple, quand vous déployez une application web, le plan App Service doit être présent. Si vous n’avez pas spécifié que l’application web est dépendante du plan App Service, Resource Manager crée les deux ressources en même temps. Vous obtenez une erreur indiquant que la ressource du plan App Service est introuvable, car elle n’existe pas encore au moment où vous tentez de définir une propriété dans l’application web. Vous pouvez éviter cette erreur en définissant la dépendance dans l’application web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Il vaut mieux éviter de définir des dépendances qui ne sont pas nécessaires. Lorsque vous avez des dépendances inutiles, vous prolongez la durée du déploiement en empêchant les ressources qui ne sont pas interdépendantes d’être déployées en parallèle. Par ailleurs, il se peut que créiez des dépendances circulaires qui bloquent le déploiement. La fonction [reference](resource-group-template-functions-resource.md#reference) et les fonctions [list*](resource-group-template-functions-resource.md#list) créent une dépendance implicite envers la ressource référencée, lorsque cette ressource est déployée dans le même modèle et référencée par son nom (et non l’ID de la ressource). Il se peut donc que vous ayez des dépendances en plus des dépendances spécifiées dans la propriété **dependsOn**. La fonction [resourceId](resource-group-template-functions-resource.md#resourceid) ne crée pas de dépendance implicite pas plus qu’elle ne valide l’existence de la ressource. La fonction [reference](resource-group-template-functions-resource.md#reference) et les fonctions [list*](resource-group-template-functions-resource.md#list) ne créent pas de dépendance implicite lorsque la ressource est référencée par son ID. Pour créer une dépendance implicite, transmettez le nom de la ressource qui est déployée dans le même modèle.

Si vous observez des problèmes de dépendance, vous devez déterminer l’ordre de déploiement des ressources. Pour afficher l’ordre des opérations de déploiement :

1. Sélectionnez l’historique de déploiement de votre groupe de ressources.

   ![sélectionner l’historique de déploiement](./media/resource-manager-not-found-errors/select-deployment.png)

2. Sélectionnez un déploiement dans l’historique, puis sélectionnez **Événements**.

   ![sélectionner les événements de déploiement](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Examinez la séquence d’événements de chaque ressource. Faites attention à l’état de chaque opération. Par exemple, l’image suivante montre trois comptes de stockage déployés en parallèle. Remarquez que les trois comptes de stockage sont démarrés en même temps.

   ![déploiement en parallèle](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   L’image suivante montre trois comptes de stockage non déployés en parallèle. Le deuxième compte de stockage dépend du premier compte de stockage, et le troisième du deuxième. Le premier compte de stockage est démarré, accepté et terminé avant que le suivant ne démarre.

   ![déploiement séquentiel](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solution 2 : Obtenir les ressources des différents groupes de ressources

Lorsque la ressource existe dans un groupe de ressources différent de celui dans lequel a lieu le déploiement, utilisez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid) pour obtenir le nom complet de la ressource.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solution 3 : Vérifier la fonction de référence

Recherchez une expression qui inclut la fonction [reference](resource-group-template-functions-resource.md#reference). Les valeurs que vous fournissez varient selon si la ressource fait partie des mêmes modèle, groupe de ressources et abonnement. Vérifiez que vous fournissez les valeurs de paramètres requises pour votre scénario. Si la ressource se trouve dans un autre groupe de ressources, fournissez l’ID complet de la ressource. Par exemple, pour faire référence à un compte de stockage dans un autre groupe de ressources, utilisez :

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```