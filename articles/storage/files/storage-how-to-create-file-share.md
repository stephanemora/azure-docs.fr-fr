---
title: Crée un partage de fichiers Azure
titleSuffix: Azure Files
description: Guide pratique pour créer un partage de fichiers Azure en utilisant le portail Azure, PowerShell ou l’interface Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 728db85e7b5afab676612d908e2ba420c7582194
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645575"
---
# <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Pour créer un partage de fichiers Azure, vous devez répondre à trois questions se rapportant la façon dont vous allez l’utiliser :

- **Quelles sont les exigences de performances pour votre partage de fichiers Azure ?**  
    Azure Files propose des partages de fichiers Standard, hébergés sur un matériel équipé de disque dur (HDD), et des partages de fichiers Premium, hébergés sur un matériel équipé de disque SSD (SSD).

- **De quelle taille de partage de fichiers avez-vous besoin ?**  
    Les partages de fichiers Standard peuvent couvrir jusqu’à 100 Tio, mais cette fonctionnalité n’est pas activée par défaut. Si vous avez besoin d’un partage de fichiers dont la taille est supérieure à 5 Tio, vous devez activer la fonctionnalité de partage de fichiers volumineux pour votre compte de stockage. Les partages de fichiers Premium peuvent aller jusqu’à 100 Tio sans aucun paramétrage particulier, mais ils sont provisionnés, au lieu de payer à l’utilisation comme vous le faites pour les partages de fichiers Standard. Cela signifie que le provisionnement d’un partage de fichiers plus volumineux que ce dont vous avez besoin va augmenter le coût total du stockage.

- **Quelles sont vos exigences de redondance pour votre partage de fichiers Azure ?**  
    Les partages de fichiers Standard offrent un stockage localement redondant (LRS), redondant interzone (ZRS), géoredondant (GRS) ou géoredondant interzone (GZRS). Toutefois, la fonctionnalité de partage de fichiers volumineux n’est prise en charge que sur les partages de fichiers localement redondants et redondants interzones. Les partages de fichiers Premium ne prennent en charge aucune forme de géoredondance.

    Les partages de fichiers Premium sont disponibles avec la redondance en local dans la plupart des régions qui offrent des comptes de stockage, et avec la redondance interzone dans un sous-ensemble plus petit de régions. Pour savoir si les partages de fichiers Premium sont actuellement disponibles dans votre région, consultez la page des [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pour Azure. Pour plus d’informations sur les régions qui prennent en charge ZRS, consultez [Redondance de Stockage Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Pour plus d’informations sur ces trois choix, consultez [Planification d’un déploiement Azure Files](storage-files-planning.md).

## <a name="prerequisites"></a>Prérequis
- Cet article suppose que vous avez déjà créé un abonnement Azure. Si vous n’avez pas d’abonnement, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Si vous envisagez d’utiliser Azure PowerShell, [installez-en la dernière version](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Si vous envisagez d’utiliser Azure CLI, [installez-en la dernière version](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Les partages de fichiers Azure sont déployés dans des *comptes de stockage*, qui sont des objets de niveau supérieur représentant un pool partagé de stockage. Ce pool de stockage peut être utilisé pour déployer plusieurs partages de fichiers. 

Azure prend en charge plusieurs types de comptes de stockage pour différents scénarios de stockage que les clients peuvent rencontrer, mais il existe deux principaux types de comptes de stockage pour Azure Files. Le type de compte de stockage que vous devez créer varie selon que vous souhaitez créer un partage de fichiers Standard ou un partage de fichiers Premium : 

- **Comptes de stockage version 2 universels (GPv2)**  : Les comptes de stockage GPv2 vous permettent de déployer des partages de fichiers Azure sur du matériel Standard/sur disque dur (HDD). En plus de stocker les partages de fichiers Azure, les comptes de stockage GPv2 peuvent stocker d’autres ressources de stockage, telles que des conteneurs d’objets blob, des files d’attente ou des tables. 

- **Comptes de stockage FileStorage** : Les comptes de stockage FileStorage vous permettent de déployer des partages de fichiers Azure sur du matériel Premium/sur disque SSD. Les comptes FileStorage peuvent uniquement être utilisés pour stocker des partages de fichiers Azure. Aucune autre ressource de stockage (conteneurs d’objets blob, files d’attente, tables, etc.) ne peut être déployée dans un compte FileStorage.

# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour créer un compte de stockage par le biais du portail Azure, sélectionnez **+ Créer une ressource** à partir du tableau de bord. Dans la fenêtre de recherche de la Place de marché Azure qui s’affiche, recherchez **compte de stockage** et sélectionnez le résultat de cette recherche. La page de la vue d’ensemble des comptes de stockage s’ouvre. Sélectionnez **Créer** pour poursuivre avec l’assistant de création des comptes de stockage.

![Capture d’écran de l’option de création rapide d’un compte de stockage dans un navigateur](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>La section Bases
La première section à compléter pour créer un compte de stockage s’intitule **Bases**. Elle contient tous les champs nécessaires à la création d’un compte de stockage. Pour créer un compte de stockage GPv2, assurez-vous que la case d’option **Performances** est définie sur *Standard*, et que la liste déroulante **Type de compte** est sélectionnée pour *StorageV2 (general purpose v2)* .

![Capture d’écran de la case d’option Performances avec la sélection de Standard, et la sélection de StorageV2 pour le Type de compte](media/storage-how-to-create-file-share/create-storage-account-1.png)

Pour créer un compte de stockage FileStorage, assurez-vous que la case d’option **Performances** est définie sur *Premium*, et que la liste déroulante **Type de compte** est sélectionnée pour *FileStorage*.

![Capture d’écran de la case d’option Performances avec la sélection de Premium, et la sélection de FileStorage pour le Type de compte](media/storage-how-to-create-file-share/create-storage-account-2.png)

Les autres champs de base sont indépendants du choix du compte de stockage :
- **Abonnement**: abonnement pour le compte de stockage dans lequel effectuer le déploiement. 
- **Groupe de ressources** : groupe de ressources pour le compte de stockage dans lequel effectuer le déploiement. Vous pouvez créer un groupe de ressources ou utiliser un groupe de ressources existant. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Lorsque vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant.
- **Nom du compte de stockage** : nom de la ressource de compte de stockage à créer. Ce nom doit être globalement unique, mais il peut s’agir du nom de votre choix. Le nom du compte de stockage est utilisé comme nom de serveur lorsque vous montez un partage de fichiers Azure via SMB.
- **Emplacement** : région pour le compte de stockage dans lequel effectuer le déploiement. Il peut s’agir de la région associée au groupe de ressources ou de toute autre région disponible.
- **Réplication** : bien qu’il soit intitulé « réplication », ce champ se rapporte en fait à la **redondance** ; il s’agit du niveau de redondance souhaité : redondance en local (LRS), redondance interzone (ZRS), géoredondance (GRS) et géoredondance interzone. Cette liste déroulante contient également une géoredondance avec accès en lecture (RA-GRS) et une géoredondance interzone avec accès en lecture (RA-GZRS) qui ne s’appliquent pas aux partages de fichiers Azure. Tous les partages de fichiers créés dans un compte de stockage avec ces options sélectionnées seront en fait respectivement géoredondants ou géoredondants interzones. Selon votre région ou le type de compte de stockage sélectionné, certaines options de redondance peuvent ne pas être autorisées.
- **Niveau d’accès** : ce champ ne s’applique pas à Azure Files, vous pouvez donc choisir une de ces cases d’option.

#### <a name="the-networking-blade"></a>Le panneau Mise en réseau
La section de mise en réseau vous permet de configurer les options de réseau. Ces paramètres sont facultatifs pour la création du compte de stockage et peuvent être configurés ultérieurement si vous le souhaitez. Pour plus d’informations sur ces options, consultez [Considérations relatives à la mise en réseau Azure Files](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Le panneau Avancé
La section des paramètres avancés contient plusieurs paramètres importants pour les partages de fichiers Azure :

- **Transfert sécurisé requis** : ce champ indique si le compte de stockage nécessite un chiffrement en transit pour la communication avec le compte de stockage. Nous vous recommandons de laisser cette option activée. Par contre, si vous avez besoin de la prise en charge de SMB 2.1, vous devez désactiver cette option. Si vous désactivez le chiffrement, nous vous recommandons de restreindre l’accès de votre compte de stockage à un réseau virtuel avec des points de terminaison de service et/ou des points de terminaison privés.
- **Partages de fichiers volumineux** : ce champ active le compte de stockage pour les partages de fichiers allant jusqu’à 100 Tio. L’activation de cette fonctionnalité permet de limiter votre compte de stockage uniquement aux options de stockage redondant en local et redondant interzone. Dès lors qu’un compte de stockage GPv2 a été activé pour les partages de fichiers volumineux, vous ne pouvez pas désactiver la fonction de partage de fichiers volumineux. Les comptes de stockage FileStorage (comptes de stockage pour les partages de fichiers Premium) ne disposent pas de cette option puisque tous les partages de fichiers Premium peuvent effectuer un scale-up jusqu’à 100 Tio. 

![Capture d’écran des paramètres avancés importants qui s’appliquent à Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

Les autres paramètres disponibles sous l’onglet Avancé (suppression réversible d’objet blob, espace de noms hiérarchique pour Azure Data Lake Storage Gen 2 et NFSv3 pour le stockage Blob) ne s’appliquent pas à Azure Files.

#### <a name="tags"></a>Balises
Les étiquettes sont des paires nom/valeur qui vous permettent de catégoriser les ressources et d’afficher une facturation centralisée en appliquant la même étiquette à plusieurs ressources et groupes de ressources. Ces options sont facultatives et peuvent être appliquées après la création du compte de stockage.

#### <a name="review--create"></a>Vérifier + créer
La dernière étape de création du compte de stockage consiste à sélectionner le bouton **Créer** sous l’onglet **Vérifier + créer**. Ce bouton n’est pas disponible si tous les champs obligatoires pour un compte de stockage ne sont pas renseignés.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pour créer un compte de stockage à l’aide de PowerShell, nous allons utiliser l’applet de commande `New-AzStorageAccount`. Cette applet de commande présente de nombreuses options ; seules les options obligatoires sont affichées. Pour en savoir plus sur les options avancées, consultez la [documentation sur l’applet de commande `New-AzStorageAccount`](/powershell/module/az.storage/new-azstorageaccount).

Pour simplifier la création du compte de stockage et du partage de fichiers suivant, nous allons stocker plusieurs paramètres dans des variables. Vous pouvez remplacer les contenu des variables par les valeurs de votre choix. Toutefois, notez que le nom du compte de stockage doit être globalement unique.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Pour créer un compte de stockage apte à stocker les partages de fichiers Azure Standard, nous allons utiliser la commande suivante. Le paramètre `-SkuName` est associé au type de redondance souhaité. Si vous souhaitez disposer d’un compte de stockage géoredondant ou géoredondant interzone, vous devez également supprimer le paramètre `-EnableLargeFileShare`.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Pour créer un compte de stockage apte à stocker les partages de fichiers Azure Premium, nous allons utiliser la commande suivante. Notez que le paramètre `-SkuName` a changé pour inclure à la fois `Premium` et le niveau de redondance souhaité en local (`LRS`). Le paramètre `-Kind` est `FileStorage` à la place de `StorageV2`, car les partages de fichiers Premium doivent être créés dans un compte de stockage FileStorage au lieu d’un compte de stockage GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Pour créer un compte de stockage en utilisant l’interface Azure CLI, nous allons nous servir de la commande az storage account create. Cette commande présente de nombreuses options ; seules les options obligatoires sont affichées. Pour en savoir plus sur les options avancées, consultez la [documentation sur les commandes `az storage account create`](/cli/azure/storage/account).

Pour simplifier la création du compte de stockage et du partage de fichiers suivant, nous allons stocker plusieurs paramètres dans des variables. Vous pouvez remplacer les contenu des variables par les valeurs de votre choix. Toutefois, notez que le nom du compte de stockage doit être globalement unique.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Pour créer un compte de stockage apte à stocker les partages de fichiers Azure Standard, nous allons utiliser la commande suivante. Le paramètre `--sku` est associé au type de redondance souhaité. Si vous souhaitez disposer d’un compte de stockage géoredondant ou géoredondant interzone, vous devez également supprimer le paramètre `--enable-large-file-share`.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Pour créer un compte de stockage apte à stocker les partages de fichiers Azure Premium, nous allons utiliser la commande suivante. Notez que le paramètre `--sku` a changé pour inclure à la fois `Premium` et le niveau de redondance souhaité en local (`LRS`). Le paramètre `--kind` est `FileStorage` à la place de `StorageV2`, car les partages de fichiers Premium doivent être créés dans un compte de stockage FileStorage au lieu d’un compte de stockage GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Créer un partage de fichiers
Une fois que vous avez créé votre compte de stockage, il ne vous reste plus qu’à créer votre partage de fichiers. Ce processus est fondamentalement le même, que vous utilisiez un partage de fichiers Premium ou un partage de fichiers Standard. La principale différence se trouve dans le **quota** et ce qu’il représente.

Pour les partages de fichiers Standard, il s’agit d’une limite supérieure du partage de fichiers Azure, au-delà de laquelle les utilisateurs finals ne peuvent pas aller. La finalité principale du quota d’un partage de fichiers Standard est budgétaire : « Je ne veux pas que ce partage de fichiers augmente au-delà de ce point ». Si aucun quota n’est spécifié, le partage de fichiers Standard peut s’étendre jusqu’à 100 Tio (ou 5 Tio si la propriété de partages de fichiers volumineux n’est pas définie pour un compte de stockage).

Pour les partages de fichiers Premium, le quota est surchargé pour signifier de **taille provisionnée**. La taille provisionnée est la quantité pour laquelle vous êtes facturé, quelle que soit l’utilisation faite. Lorsque vous provisionnez un partage de fichiers Premium, vous devez prendre en compte deux facteurs : 1) la croissance future du partage au niveau de l’utilisation de l’espace et 2) les IOPS nécessaires pour votre charge de travail. Chaque Gio provisionné vous donne droit à des e/s par seconde supplémentaires, réservées et en rafale. Pour plus d’informations sur la planification d’un partage de fichiers Premium, consultez [Provisionnement des partages de fichiers Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portail](#tab/azure-portal)
Si vous venez de créer votre compte de stockage, vous pouvez accéder à celui-ci depuis l’écran de déploiement en sélectionnant **Accéder à la ressource**. Si vous avez déjà créé le compte de stockage, vous pouvez y accéder par le groupe de ressources qui le contient. Une fois dans le compte de stockage, sélectionnez la vignette intitulée **Partages de fichiers** (vous pouvez également accéder à **Partages de fichiers** en utilisant le sommaire pour le compte de stockage).

![Capture d’écran de la vignette Partages de fichiers](media/storage-how-to-create-file-share/create-file-share-1.png)

Dans la liste des partages de fichiers, vous devez voir tous les partages de fichiers que vous avez créés précédemment dans ce compte de stockage ; si aucun partage de fichiers n’a encore été créé, un tableau vide est affichée. Sélectionnez **+ Partage de fichiers** pour créer un partage de fichiers.

Le nouveau panneau de partage de fichiers doit s’afficher à l’écran. Renseignez les champs du nouveau panneau de partage de fichiers pour créer un partage de fichiers :

- **Nom** : nom du partage de fichiers à créer.
- **Quota** : quota du partage de fichiers pour les partages de fichiers Standard ; taille provisionnée du partage de fichiers pour les partages de fichiers Premium.

Sélectionnez **Créer** pour terminer la création du partage. Notez que si votre compte de stockage se trouve dans un réseau virtuel, vous ne serez pas en mesure de créer un partage de fichiers Azure, à moins que votre client se trouve également dans le réseau virtuel. Vous pouvez aussi contourner cette limite à ce stade en utilisant l’applet de commande Azure PowerShell `New-AzRmStorageShare`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vous pouvez créer le partage de fichiers Azure avec l’applet de commande [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare). Les commandes PowerShell suivantes supposent que vous avez défini les variables `$resourceGroupName` et `$storageAccountName` comme indiqué ci-dessus à la section de création d’un compte de stockage avec Azure PowerShell. 

> [!Important]  
> Pour les partages de fichiers Premium, le paramètre `-QuotaGiB` fait référence à la taille provisionnée du partage de fichiers. La taille provisionnée du partage de fichiers est la quantité pour laquelle vous êtes facturé, quelle que soit l’utilisation faite. Les partages de fichiers Standard sont facturés en fonction de l’utilisation, et non par rapport à la taille provisionnée.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> Le nom de votre partage de fichiers doit être en minuscules. Pour obtenir des informations détaillées sur le nommage des partages de fichiers et des fichiers, consultez  [Nommage et référencement des partages, des répertoires, des fichiers et des métadonnées](https://msdn.microsoft.com/library/azure/dn167011.aspx).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Avant de pouvoir créer un partage de fichiers Azure à l’aide de l’interface Azure CLI, vous devez obtenir une clé de compte de stockage avec laquelle autoriser l’opération de création de partage de fichiers. Pour ce faire, utilisez la commande [`az storage account keys list`](/cli/azure/storage/account/keys) :

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Une fois que vous disposez de la clé de compte de stockage, vous pouvez créer le partage de fichiers Azure avec la commande [`az storage share create`](/cli/azure/storage/share). 

> [!Important]  
> Pour les partages de fichiers Premium, le paramètre `--quota` fait référence à la taille provisionnée du partage de fichiers. La taille provisionnée du partage de fichiers est la quantité pour laquelle vous êtes facturé, quelle que soit l’utilisation faite. Les partages de fichiers Standard sont facturés en fonction de l’utilisation, et non par rapport à la taille provisionnée.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Cette commande échoue si le compte de stockage est contenu dans un réseau virtuel et que l’ordinateur à partir duquel vous appelez cette commande ne fait pas partie du réseau virtuel. Vous pouvez contourner cette limite à ce stade en utilisant l’applet de commande Azure PowerShell `New-AzRmStorageShare`, comme décrit ci-dessus, ou en exécutant l’interface Azure CLI à partir d’un ordinateur faisant partie du réseau virtuel, y compris via une connexion VPN.

---

> [!Note]  
> Le nom de votre partage de fichiers doit être en minuscules. Pour obtenir des informations détaillées sur le nommage des partages de fichiers et des fichiers, consultez  [Nommage et référencement des partages, des répertoires, des fichiers et des métadonnées](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### <a name="create-a-hot-or-cool-file-share"></a>Créer un partage de fichiers chaud ou froid
Un **compte de stockage v2 (GPv2) à usage général** peut contenir des partages de fichiers à chaud ou à froid (ou un mélange des deux), optimisés pour les transactions. Des partages optimisés pour les transactions sont disponibles dans toutes les régions Azure, mais des partages de fichiers chauds et froids ne sont disponibles que [dans un sous-ensemble de régions](storage-files-planning.md#storage-tiers). Vous pouvez créer un partage de fichiers chaud ou froid à l’aide du module de préversion Azure PowerShell ou d’Azure CLI. 

# <a name="portal"></a>[Portail](#tab/azure-portal)
Le portail Azure ne prend pas encore en charge la création de partages de fichiers chauds et froids, ou le déplacement de partages de fichiers optimisés pour les transactions existants vers des partages chauds ou froids. Consultez les instructions de création d’un partage de fichiers avec PowerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myhotshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Hot

# You can also change an existing share's tier.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

> [!Note]  
> La possibilité de définir et de changer des niveaux par le biais de PowerShell est fournie dans le module PowerShell Az.Storage en préversion. Quand vous créez des scripts, gardez à l’esprit que ces applets de commande ou leur sortie peuvent changer avant leur publication dans le module PowerShell AZ.Storage mis à la disposition générale.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Les fonctionnalités permettant de créer ou de déplacer un partage de fichiers vers un niveau spécifique sont disponibles dans la dernière mise à jour d’Azure CLI. La mise à jour d’Azure CLI est spécifique du système d’exploitation ou de la distribution Linux que vous utilisez. Pour obtenir des instructions sur la façon de mettre à jour Azure CLI sur votre système, consultez [Installer l’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

```bash
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2
# storage accounts. Standard tiers are only available in standard storage accounts.
shareName="myhotshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Hot"
```

> [!Note]  
> La possibilité de définir un niveau avec le paramètre `--access-tier` est fournie dans le dernier package Azure CLI. Cette commande ou sa sortie peut changer avant d’être marquée comme étant mise à la disposition générale. Vous devez donc créer des scripts en en tenant compte.

---

## <a name="next-steps"></a>Étapes suivantes
- [Planifier un déploiement d’Azure Files](storage-files-planning.md) ou [Planifier un déploiement d’Azure File Sync](storage-sync-files-planning.md). 
- [Présentation de la mise en réseau](storage-files-networking-overview.md).
- Connecter et monter un partage de fichiers sur [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) et [Linux](storage-how-to-use-files-linux.md).