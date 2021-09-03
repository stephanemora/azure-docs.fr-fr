---
title: Configurer des fonctionnalités d’évaluation dans un abonnement Azure
description: Décrit comment répertorier, inscrire ou annuler l’inscription des fonctionnalités d’évaluation dans votre abonnement Azure pour un fournisseur de ressources.
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9d7b705e4db7c6556eea4b9fd3cbe1916ec257a4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527893"
---
# <a name="set-up-preview-features-in-azure-subscription"></a>Configurer des fonctionnalités d’évaluation dans un abonnement Azure

Cet article explique comment gérer les fonctionnalités d’évaluation dans votre abonnement Azure. Les fonctionnalités d’évaluation vous permettent de choisir les nouvelles fonctionnalités avant leur publication. Certaines fonctionnalités d’évaluation sont disponibles pour toute personne souhaitant participer. D’autres fonctionnalités d’évaluation nécessitent l’approbation de l’équipe produit.

Azure Feature Exposure Control (AFEC) est disponible via l’espace de noms [Microsoft.Features](/rest/api/resources/features). Les fonctionnalités d’évaluation présentent le format d’ID de ressource suivant :

`Microsoft.Features/providers/{resourceProviderNamespace}/features/{featureName}`

## <a name="list-preview-features"></a>Liste des fonctionnalités d’évaluation

Vous pouvez répertorier toutes les fonctionnalités d’évaluation et leur état d’inscription pour un abonnement Azure.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Le portail affiche une fonctionnalité d’évaluation uniquement lorsque le service qui possède la fonctionnalité a explicitement choisi l’expérience de gestion des fonctionnalités d’évaluation.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans la zone de recherche, entrez _abonnements_ et sélectionnez **Abonnements**.

    :::image type="content" source="./media/preview-features/search.png" alt-text="Recherche dans le portail Azure.":::

1. Sélectionnez le lien pour le nom de votre abonnement.

    :::image type="content" source="./media/preview-features/subscriptions.png" alt-text="Sélectionner un abonnement Azure.":::

1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Fonctionnalités d’évaluation**.

    :::image type="content" source="./media/preview-features/preview-features-menu.png" alt-text="Menu des fonctionnalités d’évaluation d’Azure.":::

1. La liste des fonctionnalités d’évaluation et de l’état actuel de l’inscription s’affiche.

    :::image type="content" source="./media/preview-features/preview-features-list.png" alt-text="Liste du portail Azure liste des fonctionnalités d’évaluation":::.

1. Dans **Fonctionnalités d’évaluation** vous pouvez filtrer la liste par **nom**, **état** ou **type** :

    - **Filtrer par nom** : doit contenir du texte venant du nom de la fonctionnalité d’évaluation, et pas de son **nom d’affichage**.
    - **État** : sélectionnez le menu déroulant et choisissez un état. Le portail ne filtre pas sur **Pas inscrit**.
    - **Type** : sélectionnez le menu déroulant et choisissez un type.

    :::image type="content" source="./media/preview-features/filter.png" alt-text="Fonctionnalités d’évaluation du filtre du portail Azure.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour répertorier toutes les fonctionnalités d’évaluation de l’abonnement, utilisez la commande [az feature list](/cli/azure/feature#az_feature_list).

La sortie par défaut pour Azure CLI est JSON. Pour en savoir plus sur les autres formats de sortie, consultez l’article [Formats de sortie pour les commandes Azure CLI](/cli/azure/format-output-azure-cli).

```azurecli-interactive
az feature list
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "NotRegistered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Pour filtrer la sortie d’un fournisseur de ressources en particulier, utilisez le paramètre `namespace`. Dans cet exemple, le paramètre `output` spécifies un format de table.

```azurecli-interactive
az feature list --namespace Microsoft.Compute --output table
```

```Output
Name                                                RegistrationState
-------------------------------------------------   -------------------
Microsoft.Compute/AHUB                              Unregistered
Microsoft.Compute/AllowManagedDisksReplaceOSDisk    Registered
Microsoft.Compute/AllowPreReleaseRegions            Pending
Microsoft.Compute/InGuestPatchVMPreview             NotRegistered
```

Pour filtrer la sortie d’une fonctionnalité d’évaluation en particulier, utilisez la commande [az feature show](/cli/azure/feature#az_feature_show).

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  NotRegistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour répertorier toutes les fonctionnalités d’évaluation de l’abonnement, utilisez la cmdlet [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature).

```azurepowershell-interactive
Get-AzProviderFeature -ListAvailable
```

```Output
FeatureName      ProviderName     RegistrationState
-----------      ------------     -----------------
betaAccess       Microsoft.AAD    NotRegistered
previewAccess    Microsoft.AAD    Registered
tipAccess        Microsoft.AAD    Pending
testAccess       Microsoft.AAD    Unregistered
```

Pour filtrer la sortie d’un fournisseur de ressources en particulier, utilisez le paramètre `ProviderNamespace`. La sortie par défaut affiche uniquement les fonctionnalités inscrites. Pour afficher toutes les fonctionnalités d’évaluation d’un fournisseur de ressources, utilisez le paramètre `ListAvailable` avec le paramètre `ProviderNamespace`.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -ListAvailable
```

```Output
FeatureName                          ProviderName        RegistrationState
-----------                          ------------        -----------------
AHUB                                 Microsoft.Compute   Unregistered
AllowManagedDisksReplaceOSDisk       Microsoft.Compute   Registered
AllowPreReleaseRegions               Microsoft.Compute   Pending
InGuestPatchVMPreview                Microsoft.Compute   NotRegistered
```

Vous pouvez filtrer la sortie d’une fonctionnalité d’évaluation en particulier à l’aide du paramètre `FeatureName`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   NotRegistered
```

---

## <a name="register-preview-feature"></a>Inscrire la fonctionnalité d’évaluation

Inscrivez une fonctionnalité d’évaluation dans votre abonnement Azure pour exposer davantage de fonctionnalités à un fournisseur de ressources. Certaines fonctionnalités d’évaluation nécessitent une approbation.

Une fois la fonctionnalité d’évaluation inscrite dans votre abonnement, vous voyez un de ces deux états : **inscrite** ou **en attente**.

- Pour une fonctionnalité d’évaluation qui ne nécessite pas d’approbation, l’état est **enregistré**.
- Si une fonctionnalité d’évaluation nécessite une approbation, l’état de l’inscription est **En attente**.
  - Pour demander l’approbation, vous pouvez soumettre une [demande de support Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).
  - Une fois l’inscription approuvée, l’état de la fonctionnalité d’évaluation passe à **Enregistré**.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans la zone de recherche, entrez _abonnements_ et sélectionnez **Abonnements**.
1. Sélectionnez le lien pour le nom de votre abonnement.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Fonctionnalités d’évaluation**.
1. Sélectionnez le lien de la fonctionnalité d’évaluation que vous souhaitez inscrire.
1. Sélectionnez **Inscription**.

    :::image type="content" source="./media/preview-features/register.png" alt-text="Portail Azure - inscription de la fonctionnalité d’évaluation.":::

1. Sélectionnez **OK**.

L’écran **Fonctionnalités d’évaluation** s’actualise et l’**état** des fonctionnalités d’évaluation s’affiche.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour inscrire une fonctionnalité d’évaluation, appelez la commande [az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Registering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Pour voir l’état de l’inscription, utilisez la commande `az feature show`.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Registered
```

> [!NOTE]
> Lorsque la commande register s’exécute, un message s’affiche, après l’inscription de la fonctionnalité, pour exécuter `az provider register --namespace <provider-name>` pour la propagation des modifications.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour inscrire une fonctionnalité d’évaluation, utilisez la cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registering
```

Pour voir l’état de l’inscription, utilisez la cmdlet `Get-AzProviderFeature`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registered
```

---

## <a name="unregister-preview-feature"></a>Désinscrire la fonctionnalité d’évaluation

Une fois que vous avez fini d’utiliser une fonctionnalité d’évaluation, annulez son inscription auprès de votre abonnement Azure. Vous pouvez remarquer deux états différents après avoir annulé l’inscription de la fonctionnalité. Si vous annulez l’inscription via le portail, l’état est défini sur **Non inscrit**. Si vous annulez l’inscription via Azure CLI, PowerShell ou l’API REST, l’état est défini sur **Pas inscrit**. L’état est différent, car le portail supprime l’inscription de la fonctionnalité, mais les commandes annulent l’inscription de la fonctionnalité. Dans les deux cas, la fonctionnalité n’est plus disponible dans votre abonnement. Dans les deux cas, vous pouvez réactiver la fonctionnalité en la réinscrivant.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Vous pouvez annuler l’inscription de fonctionnalités d’évaluation dans **Fonctionnalités d’évaluation**. L’**état** passe à **Non inscrit**.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans la zone de recherche, entrez _abonnements_ et sélectionnez **Abonnements**.
1. Sélectionnez le lien pour le nom de votre abonnement.
1. Dans le menu de gauche, sous **Paramètres**, sélectionnez **Fonctionnalités d’évaluation**.
1. Sélectionnez le lien de la fonctionnalité d’évaluation que vous souhaitez désinscrire.
1. Sélectionner **Annuler l’inscription**.

    :::image type="content" source="./media/preview-features/unregister.png" alt-text="Portail Azure - Désinscription de la fonctionnalité d’évaluation.":::

1. Sélectionnez **OK**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour désinscrire une fonctionnalité d’évaluation, appelez la commande [az feature unregister](/cli/azure/feature#az_feature_unregister). L’état `RegistrationState` passe à **Pas inscrit**.

```azurecli-interactive
az feature unregister --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Unregistering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Pour voir l’état de la désinscription, utilisez la commande `az feature show`.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

> [!NOTE]
> Lorsque la commande Unregister s’exécute, un message s’affiche, après l’annulation de l’inscription de la fonctionnalité, pour exécuter `az provider register --namespace <provider-name>` pour la propagation des modifications.

Pour rechercher les fonctionnalités d’évaluation **non inscrites**, utilisez la commande suivante. Remplacez `<ResourceProvider.Name>` par un nom de fournisseur, par exemple `Microsoft.Compute`.

L’exemple suivant affiche une fonctionnalité d’évaluation **non inscrite** pour le fournisseur de ressources `Microsoft.Compute`.

```azurecli-interactive
az feature list --namespace <ResourceProvider.Name> --query "[?properties.state=='Unregistered'].{Name:name, RegistrationState:properties.state}" --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour désinscrire une fonctionnalité d’évaluation, utilisez la cmdlet [Unregister-AzProviderFeature](/powershell/module/az.resources/unregister-azproviderfeature) cmdlet. L’état `RegistrationState` passe à **Pas inscrit**.

```azurepowershell-interactive
Unregister-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistering
```

Pour voir l’état de la désinscription, utilisez la cmdlet `Get-AzProviderFeature`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

L’exemple suivant affiche une fonctionnalité d’évaluation **non inscrite** pour le fournisseur de ressources `Microsoft.Compute`.

```azurepowershell-interactive
Get-AzProviderFeature  -ProviderNamespace "Microsoft.Compute" -ListAvailable | Where-Object { $_.RegistrationState -eq "Unregistered" }
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

---

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser les appels de l’API REST et lister, inscrire ou désinscrire des fonctionnalités d’évaluation, consultez la documentation sur les [fonctionnalités](/rest/api/resources/features).
- Pour plus d’informations sur l’inscription d’un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](resource-providers-and-types.md).
- Pour obtenir la liste qui mappe les fournisseurs de ressources aux services Azure, consultez [Fournisseurs de ressources pour les services Azure](azure-services-resource-providers.md).
