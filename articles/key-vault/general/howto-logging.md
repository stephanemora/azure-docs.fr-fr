---
title: Activer la journalisation d’Azure Key Vault
description: Découvrez comment activer la journalisation d’Azure Key Vault, qui enregistre les informations dans un compte de stockage Azure que vous fournissez.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 6e2c44b80390fc1fcf4f9b579daba65d0387a0f7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751190"
---
# <a name="enable-key-vault-logging"></a>Activer la journalisation de Key Vault

Une fois que vous avez créé un ou plusieurs coffres de clés, vous voulez probablement contrôler qui accède à ces derniers, par quel moyen et quand. Pour plus d’informations sur la fonctionnalité, consultez [Journalisation d’Azure Key Vault](logging.md).

Éléments journalisés :

* Toutes les requêtes d’API REST authentifiées, ce qui inclut les requêtes ayant échoué suite à des autorisations d’accès, des erreurs système ou des requêtes incorrectes.
* Les opérations sur le coffre de clés proprement dit, notamment la création, la suppression, la définition des stratégies d’accès au coffre de clés et la mise à jour des attributs de coffre de clés (par exemple, les balises).
* Les opérations sur les clés et secrets dans le coffre de clés, à savoir :
  * Création, modification ou suppression de ces clés ou secrets.
  * Signature, vérification, chiffrement, déchiffrement, inclusion dans un wrapper et retrait d’un wrapper de clés, obtention des secrets, et liste de clés et secrets (et leurs versions).
* les requêtes non authentifiées qui génèrent une réponse 401. Il s’agit notamment des requêtes dépourvues de jeton du porteur, dont le format est incorrect, qui ont expiré ou qui comportent un jeton non valide.  
* Les événements de notification Azure Event Grid pour les conditions suivantes : expiré, proche de l’expiration et stratégie d’accès du coffre modifié (l’événement de nouvelle version n’est pas enregistré). Les événements sont journalisés même si un abonnement aux événements est créé sur le coffre de clés. Pour plus d’informations, consultez [Azure Key Vault en tant que source Event Grid](../../event-grid/event-schema-key-vault.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer des éléments suivants :

* Un coffre de clés existant que vous utilisez déjà.  
* [Azure Cloud Shell](https://shell.azure.com) – Environnement Bash.
* Espace de stockage suffisant sur Azure pour vos journaux d’activité de coffre de clés.

Les commandes de cet article sont mises en forme pour [Cloud Shell](https://shell.azure.com) avec l’environnement Bash.

## <a name="connect-to-your-key-vault-subscription"></a>Se connecter à l’abonnement Key Vault

La première étape de la configuration de la journalisation des clés consiste à se connecter à l’abonnement contenant le coffre de clés. Cette étape est particulièrement importante si plusieurs abonnements sont associés à votre compte.

Avec Azure CLI, vous pouvez afficher tous vos abonnements à l’aide de la commande [az account list](/cli/azure/account#az_account_list). Ensuite, vous vous connectez à un abonnement à l’aide de la commande [az account set](/cli/azure/account#az_account_set) :

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Avec Azure PowerShell, vous pouvez d’abord répertorier vos abonnements à l’aide de l’applet de commande [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription). Ensuite, vous vous connectez à un abonnement à l’aide de l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Créer un compte de stockage pour vos journaux

Même si vous pouvez utiliser un compte de stockage existant pour vos journaux, vous en créez un dédié à vos journaux Key Vault. 

Pour simplifier davantage la gestion, vous allez utiliser le groupe de ressources qui contient le coffre de clés. Dans le [guide de démarrage rapide pour Azure CLI](quick-create-cli.md) et le [guide de démarrage rapide pour Azure PowerShell](quick-create-powershell.md), ce groupe de ressources est nommé **myResourceGroup** et la localisation est *eastus*. Remplacez ces valeurs par les vôtres, comme il convient. 

Vous devez également fournir un nom de compte de stockage. Ce nom doit être unique et comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres. Enfin, vous créez un compte de stockage de la référence SKU `Standard_LRS`.

Avec Azure CLI, utilisez la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Avec Azure PowerShell, utilisez l’applet de commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Vous devrez fournir la localisation qui correspond au groupe de ressources.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

Dans les deux cas, notez l’ID du compte de stockage. L’opération Azure CLI retourne l’ID dans la sortie. Pour obtenir l’ID avec Azure PowerShell, utilisez [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) et attribuez la sortie à une variable `$sa`. Vous pouvez alors voir le compte de stockage avec `$sa.id`. (La propriété `$sa.Context` est également utilisée plus loin dans cet article.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

L’ID du compte de stockage a le format « /subscriptions/*ID de votre abonnement*/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/*nom unique de votre compte de stockage* ».

> [!NOTE]
> Si vous décidez d’utiliser un compte de stockage existant, ce dernier doit utiliser le même abonnement que votre coffre de clés, Il doit utiliser le modèle de déploiement Azure Resource Manager plutôt que le modèle de déploiement Azure Classic.

## <a name="obtain-your-key-vault-resource-id"></a>Obtenir l’ID de ressource du coffre de clés

Dans le [guide de démarrage rapide pour CLI](quick-create-cli.md) et le [guide de démarrage rapide pour PowerShell](quick-create-powershell.md), vous avez créé une clé avec un nom unique. Utilisez ce nom à nouveau dans les étapes suivantes. Si vous ne vous souvenez pas du nom de votre coffre de clés, vous pouvez utiliser la commande Azure CLI [az keyvault list](/cli/azure/keyvault#az_keyvault_list) ou l’applet de commande Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) pour lister les coffres de clés.

Utilisez le nom de votre coffre de clés pour trouver son ID de ressource. Avec Azure CLI, utilisez la commande [az keyvault show](/cli/azure/keyvault#az_keyvault_show).

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Avec Azure PowerShell, utilisez l’applet de commande [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

L’ID de ressource de votre coffre de clés a le format « /subscriptions/*ID de votre abonnement*/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/*nom unique de votre coffre de clés* ». Notez-le pour l’étape suivante.

## <a name="enable-logging"></a>Activation de la journalisation

Vous pouvez activer la journalisation pour Key Vault à l’aide d’Azure CLI, d’Azure PowerShell ou du portail Azure.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="azure-cli"></a>Azure CLI

Utilisez la commande Azure CLI [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings) avec l’ l’ID du compte de stockage et l’ID de ressource du coffre de clés, comme suit :

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Si vous le souhaitez, vous pouvez définir une stratégie de conservation pour vos journaux, afin que les anciens journaux soient automatiquement supprimés après un laps de temps spécifié. Par exemple, vous pouvez définir une stratégie de rétention qui supprime automatiquement les journaux datant de plus de 90 jours.

Avec Azure CLI, utilisez la commande [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) . 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Utiliser la cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) avec l’indicateur `-Enabled` défini sur `$true` et `category` défini sur `AuditEvent` (unique catégorie pour la journalisation du Key Vault) :

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Si vous le souhaitez, vous pouvez définir une stratégie de conservation pour vos journaux, afin que les anciens journaux soient automatiquement supprimés après un laps de temps spécifié. Par exemple, vous pouvez définir une stratégie de rétention qui supprime automatiquement les journaux datant de plus de 90 jours.

Avec Azure PowerShell, utilisez l’applet de commande [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting).

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Pour configurer les paramètres de diagnostic dans le portail Azure, procédez comme suit :

1. Dans le menu du volet **Ressource**, sélectionnez **Paramètres de diagnostic**.

   :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="Capture d’écran montrant comment sélectionner les paramètres de diagnostic.":::

1. Sélectionnez **+ Ajouter le paramètre de diagnostic**.

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="Capture d’écran montrant comment ajouter un paramètre de diagnostic.":::
 
1. Indiquez un nom de votre paramètre de diagnostic. Pour configurer la journalisation d’Azure Monitor pour le Key Vault, sélectionnez l’option **AuditEvent**, puis **Envoyer à l’espace de travail Log Analytics**. Choisissez ensuite l’abonnement et l’espace de travail Log Analytics auquel vous souhaitez envoyer à vos journaux.

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="Capture d’écran des options Paramètres de diagnostic.":::

    Autrement, sélectionnez les options relatives aux journaux que vous souhaitez sélectionner.

1. Une fois que vous avez sélectionné les options souhaitées, sélectionnez **Enregistrer**.

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="Capture d’écran montrant comment enregistrer les options que vous avez sélectionnées.":::

---

## <a name="access-your-logs"></a>Accéder à vos journaux d’activité

Vos journaux Key Vault sont stockés dans le conteneur *insights-logs-auditevent* du compte de stockage que vous avez fourni. Pour visualiser les journaux, vous devez télécharger des objets blob.

Tout d’abord, obtenez tous les objets blob du conteneur.  Avec Azure CLI, utilisez la commande [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Avec Azure PowerShell, utilisez [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). Pour répertorier tous les objets blob présents dans ce conteneur, entrez la commande suivante :

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Dans la sortie de la commande Azure CLI ou de l’applet de commande Azure PowerShell, vous verrez que le nom des objets blob a le format suivant : `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`. Les valeurs de date et d’heure utilisent le temps universel coordonné.

Étant donné que vous pouvez utiliser le même compte de stockage pour collecter les journaux de plusieurs ressources, l’ID de ressource complet dans le nom de l’objet blob est utile si vous voulez uniquement accéder aux objets blob dont vous avez besoin ou télécharger ces derniers.

Mais d’abord, téléchargez tous les objets blob. Avec Azure CLI, utilisez la commande [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download), en lui fournissant les noms des objets blob et le chemin du fichier dans lequel vous souhaitez enregistrer les résultats.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Avec Azure PowerShell, utilisez la cmdlet [Gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob) pour obtenir une liste de tous les objets blob. Puis dirigez cette liste vers la cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) pour télécharger les journaux vers le chemin choisi.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Quand vous exécutez cette seconde applet de commande dans PowerShell, le délimiteur `/` présent dans les noms d’objet blob crée une structure de dossiers complète sous le dossier de destination. Vous allez utiliser cette structure pour télécharger et stocker les objets blob sous forme de fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple :

* Si vous disposez de plusieurs coffres de clés et souhaitez télécharger les journaux d’activité d’un seul d’entre eux nommé CONTOSOKEYVAULT3 :

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’activité d’un seul d’entre eux, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Si vous souhaitez télécharger tous les journaux du mois de janvier 2019, utilisez `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

## <a name="use-azure-monitor-logs"></a>Utiliser les journaux d’activité Azure Monitor

Vous pouvez utiliser la solution Key Vault dans les journaux Azure Monitor pour consulter les journaux `AuditEvent` de Key Vault. Dans les journaux Azure Monitor, vous utilisez des requêtes de journaux pour analyser les données et obtenir les informations dont vous avez besoin.

Pour plus d’informations, notamment sur la procédure de configuration correspondante, consultez [Azure Key Vault dans Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations conceptuelles, notamment sur l’interprétation des journaux Key Vault, consultez [Journalisation de Key Vault](logging.md).
- Pour accéder à un tutoriel utilisant Azure Key Vault dans une application web .NET, consultez l’article [Utilisation d’Azure Key Vault à partir d’une application web](tutorial-net-create-vault-azure-web-app.md).
- Voir les informations de référence sur la programmation dans le [Guide du développeur Azure Key Vault](developers-guide.md).
