---
title: Déplacer des ressources Azure vers un nouvel abonnement ou un nouveau groupe de ressources | Microsoft Docs
description: Utilisez Azure Resource Manager ou une API REST pour déplacer des ressources vers un nouveau groupe de ressources ou abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 69cd6031111c72d54cb87975c2040078a9965821
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035546"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement

Cet article vous montre comment déplacer des ressources Azure vers un autre abonnement Azure ou un autre groupe de ressources sous le même abonnement. Vous pouvez utiliser le portail Azure, Azure PowerShell, Azure CLI ou l’API REST pour déplacer des ressources.

Le groupe source et le groupe cible sont verrouillés pendant l’opération de déplacement. Les opérations d’écriture et de suppression sont bloquées sur les groupes de ressources tant que le déplacement n’est pas terminé. Ce verrou signifie que vous ne pouvez pas ajouter, mettre à jour ou supprimer des ressources dans les groupes de ressources, mais il ne signifie pas que les ressources sont figées. Par exemple, si vous déplacez un serveur SQL Server et sa base de données vers un nouveau groupe de ressources, une application qui utilise la base de données ne rencontre aucune interruption de service. Elle peut toujours lire et écrire dans la base de données.

Déplacer une ressource la déplace seulement vers un nouveau groupe de ressources ou un nouvel abonnement. Cette opération ne change pas l’emplacement de la ressource.

## <a name="checklist-before-moving-resources"></a>Liste de contrôle avant le déplacement de ressources

Plusieurs étapes importantes doivent être effectuées avant de déplacer une ressource. Vérifiez ces conditions pour prévenir d'éventuelles erreurs.

1. Les ressources que vous voulez déplacer doivent prendre en charge l’opération de déplacement. Pour obtenir la liste des ressources prenant en charge l’opération de déplacement, consultez [Prise en charge de l’opération de déplacement pour les ressources](move-support-resources.md).

1. Certains services ont des limitations ou des exigences spécifiques lors du déplacement des ressources. Si vous déplacez un des services suivants, consultez ces conseils avant.

   * [Conseils pour le déplacement d’App Services](./move-limitations/app-service-move-limitations.md)
   * [Conseils pour le déplacement de DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Conseils pour le déplacement de modèles de déploiement classique](./move-limitations/classic-model-move-limitations.md) : Calcul classique, Stockage classique, Réseaux virtuels classiques et services Cloud
   * [Aide pour le déplacement du réseau](./move-limitations/networking-move-limitations.md)
   * [Conseils pour le déplacement de Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Conseils pour le déplacement de machines virtuelles](./move-limitations/virtual-machines-move-limitations.md)

1. Les abonnements source et de destination doivent être actifs. Si vous rencontrez des problèmes lors de l’activation d’un compte qui a été désactivé, [créez une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md). Sélectionnez **Gestion des abonnements** comme type de problème.

1. Les abonnements source et de destination doivent exister dans le même [client Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Pour vérifier que les deux abonnements ont le même ID client, utilisez Azure PowerShell ou Azure CLI.

   Pour Azure PowerShell, utilisez :

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Pour l’interface de ligne de commande Azure, consultez :

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Si les ID client pour les abonnements source et de destination ne sont pas identiques, utilisez les méthodes suivantes pour rapprocher les ID client :

   * [Transfert de la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md)
   * [Associer ou ajouter un abonnement Azure à Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. L’abonnement de destination doit être inscrit pour le fournisseur de la ressource déplacée. Sinon, vous recevez une erreur indiquant que **l’abonnement n’est pas inscrit pour un type de ressource**. Vous pouvez rencontrer cette erreur lors du déplacement d’une ressource vers un nouvel abonnement qui n’a jamais été utilisé avec ce type de ressource.

   Pour PowerShell, utilisez les commandes suivantes pour obtenir l’état de l’inscription :

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Pour inscrire un fournisseur de ressources, utilisez :

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Pour l’interface CLI d’Azure, utilisez les commandes suivantes pour obtenir l’état de l’inscription :

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Pour inscrire un fournisseur de ressources, utilisez :

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Le compte déplaçant les ressources doit avoir au moins les autorisations suivantes :

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** sur le groupe de ressources source.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** sur le groupe de ressources de destination.

1. Avant de déplacer les ressources, vérifiez les quotas d’abonnement pour l’abonnement vers lequel vous souhaitez déplacer les ressources. Si le déplacement des ressources signifie que l’abonnement dépassera ses limites, vous devez vérifier si vous pouvez demander une augmentation du quota. Pour connaître la liste des limites et savoir comment demander une augmentation, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

1. **Pour un déplacement entre des abonnements, la ressource et ses ressources dépendantes doivent se trouver dans le même groupe de ressources et être déplacées ensemble.** Par exemple, une machine virtuelle avec des disques managés nécessite le déplacement simultané de la machine virtuelle, des disques managés ainsi que des autres ressources dépendantes.

   Si vous déplacez une ressource vers un nouvel abonnement, vérifiez si la ressource a des ressources dépendantes et si elles se trouvent dans le même groupe de ressources. Si les ressources ne se trouvent pas dans le même groupe de ressources, vérifiez si elles peuvent y être consolidées. Dans ce cas, mettez toutes ces ressources dans le même groupe de ressources à l’aide d’une opération de déplacement entre les groupes de ressources.

   Pour plus d’informations, consultez [Scénario de déplacement entre des abonnements](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scénario de déplacement entre différents abonnements

Le déplacement de ressources d’un abonnement vers un autre est un processus en trois étapes :

![scénario de déplacement entre des abonnements](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

Pour illustrer cela, nous avons une seule ressource dépendante.

* Étape 1 : Si les ressources dépendantes sont réparties entre différents groupes de ressources, commencez par les déplacer vers un même groupe de ressources.
* Étape 2 : Déplacez la ressource et les ressources dépendantes ensemble de l’abonnement source vers l’abonnement cible.
* Étape 3 : Si vous le souhaitez, redistribuez les ressources dépendantes dans différents groupes de ressources dans l’abonnement cible. 

## <a name="validate-move"></a>Valider le déplacement

[L’opération de validation du déplacement](/rest/api/resources/resources/validatemoveresources) vous permet de tester votre scénario de déplacement sans réellement déplacer les ressources. Utilisez cette opération pour vérifier si le déplacement va réussir. La validation est appelée automatiquement quand vous envoyez une demande de déplacement. Utilisez cette opération seulement quand vous devez prédéterminer les résultats. Pour exécuter cette opération, vous avez besoin des éléments suivants :

* Nom du groupe de ressources source
* ID de ressource du groupe de ressources cible
* ID de ressource de chaque ressource à déplacer
* [Jeton d’accès](/rest/api/azure/#acquire-an-access-token) pour votre compte

Envoyez la requête suivante :

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Avec le corps de la demande :

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Si la demande est correctement mise en forme, l’opération retourne :

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Le code d’état 202 indique que la demande de validation a été acceptée, mais la réussite de l’opération de déplacement n’est pas encore déterminée. La valeur de `location` contient une URL que vous utilisez pour vérifier l’état de l’opération longue.  

Pour vérifier l’état, envoyez la demande suivante :

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Pendant l’exécution de l’opération, vous continuez à recevoir le code d’état 202. Attendez le nombre de secondes indiqué dans la valeur `retry-after` avant de réessayer. Si l’opération de déplacement est validée, vous recevez le code d’état 204. Si la validation de déplacement échoue, vous recevez un message d’erreur, par exemple :

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Utiliser le portail

Pour déplacer des ressources, sélectionnez le groupe de ressources qui les contient, puis sélectionnez le bouton **Déplacer**.

![Déplacer des ressources](./media/resource-group-move-resources/select-move.png)

Indiquez si vous déplacez les ressources vers un nouveau groupe de ressources ou vers un nouvel abonnement.

Sélectionnez les ressources à déplacer et le groupe de ressources de destination. Confirmez que vous devez mettre à jour les scripts de ces ressources et sélectionnez **OK**. Si vous avez sélectionné l’icône Modifier l’abonnement à l’étape précédente, vous devez également sélectionner l’abonnement de destination.

![Sélectionner la destination](./media/resource-group-move-resources/select-destination.png)

Dans **Notifications**, vous voyez que l’opération de déplacement est en cours d’exécution.

![afficher l’état du déplacement](./media/resource-group-move-resources/show-status.png)

Lorsque l’opération est terminée, vous êtes informé du résultat.

![afficher les résultats du déplacement](./media/resource-group-move-resources/show-result.png)

Si vous recevez une erreur, consultez [Résoudre les problèmes liés au déplacement de ressources vers un nouveau groupe de ressource ou un nouvel abonnement](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez la commande [Move-AzResource](/powershell/module/az.resources/move-azresource). L’exemple suivant vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Pour déplacer des ressources vers un nouvel abonnement, renseignez une valeur pour le paramètre `DestinationSubscriptionId`.

Si vous recevez une erreur, consultez [Résoudre les problèmes liés au déplacement de ressources vers un nouveau groupe de ressource ou un nouvel abonnement](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, exécutez la commande [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) . Fournissez les ID des ressources à déplacer. L’exemple suivant vous indique comment déplacer plusieurs ressources vers un nouveau groupe de ressources. Dans le paramètre `--ids`, spécifiez une liste séparée par des espaces des ID des ressources à déplacer.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Pour déplacer des ressources vers un nouvel abonnement, spécifiez le paramètre `--destination-subscription-id`.

Si vous recevez une erreur, consultez [Résoudre les problèmes liés au déplacement de ressources vers un nouveau groupe de ressource ou un nouvel abonnement](troubleshoot-move.md).

## <a name="use-rest-api"></a>Avec l’API REST

Pour déplacer des ressources existantes vers un autre groupe de ressources ou un autre abonnement, utilisez l’opération [Déplacer des ressources](/rest/api/resources/Resources/MoveResources).

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

Dans le corps de la requête, vous indiquez le groupe de ressources cible et les ressources à déplacer.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Si vous recevez une erreur, consultez [Résoudre les problèmes liés au déplacement de ressources vers un nouveau groupe de ressource ou un nouvel abonnement](troubleshoot-move.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des ressources prenant en charge l’opération de déplacement, consultez [Prise en charge de l’opération de déplacement pour les ressources](move-support-resources.md).
