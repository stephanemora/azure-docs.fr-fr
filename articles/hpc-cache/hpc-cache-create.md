---
title: Créer un cache Azure HPC Cache
description: Comment créer une instance de cache Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: femila
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6f457dcf69b0f38173a3816ea1ac16c2cc7c5935
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015330"
---
# <a name="create-an-azure-hpc-cache"></a>Créer un cache Azure HPC Cache

Utilisez le portail Azure ou Azure CLI pour créer votre cache.

![capture d’écran de la vue d’ensemble du cache dans le portail Azure, avec le bouton Créer en bas](media/hpc-cache-home-page.png)

Cliquez sur l’image ci-dessous pour regarder une [vidéo de démonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) montrant comment créer un cache et ajouter une cible de stockage.

[![Miniature de vidéo : Azure HPC Cache : Configuration (cliquez pour accéder à la page vidéo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portail](#tab/azure-portal)

## <a name="define-basic-details"></a>Définir les détails de base

![Capture d’écran de la page Détails du projet dans le portail Azure.](media/hpc-cache-create-basics.png)

Dans **Détails du projet**, sélectionnez l’abonnement et le groupe de ressources qui doit héberger le cache.

Dans **Détails sur le service**, définissez le nom du cache et les autres attributs suivants :

* Emplacement : sélectionnez l’une des [régions prises en charge](hpc-cache-overview.md#region-availability).
* Réseau virtuel : vous pouvez sélectionner un réseau virtuel existant ou en créer un.
* Sous-réseau : choisissez ou créez un sous-réseau avec au moins 64 adresses IP (/24). Ce sous-réseau doit être utilisé uniquement pour cette instance Azure HPC Cache.

## <a name="set-cache-capacity"></a>Définir la capacité du cache
<!-- referenced from GUI - update aka.ms/hpc-cache-iops link if you change this header text -->

Dans la page **Cache**, vous devez définir la capacité de votre cache. Les valeurs définies ici déterminent à quelle vitesse votre cache peut traiter les requêtes des clients et quelle quantité de données il peut contenir.

La capacité influe également sur le coût du cache et le nombre de cibles de stockage qu’il peut prendre en charge.

La capacité du cache est une combinaison de deux valeurs :

* Taux de transfert de données maximal pour le cache (débit), en Go/seconde
* Quantité de stockage allouée aux données mises en cache, en To

![Capture d’écran de la page de dimensionnement du cache dans le portail Azure.](media/hpc-cache-create-capacity.png)

### <a name="understand-throughput-and-cache-size"></a>Comprendre le débit et la taille du cache

Plusieurs facteurs peuvent affecter l’efficacité de votre HPC Cache, mais le choix d’une valeur de débit appropriée et de la taille de stockage du cache est l’un des plus importants.

Lorsque vous choisissez une valeur de débit, gardez à l’esprit que le taux de transfert de données réel dépend de la charge de travail, des vitesses réseau et du type de cible de stockage.

Les valeurs que vous choisissez définissent le débit maximal pour l’ensemble du système de mise en cache. Toutefois, certaines d’entre elles sont utilisées pour les tâches de surcharge. Par exemple, si un client demande un fichier qui n’est pas déjà stocké dans le cache, ou si le fichier est marqué comme étant obsolète, votre cache utilise une partie de son débit pour l’extraire du stockage back-end.

Azure HPC Cache gère les fichiers mis en cache et préchargés pour maximiser les taux de réussite du cache. Le contenu du cache est évalué en permanence, et les fichiers sont déplacés vers un stockage à long terme quand ils sont moins fréquemment sollicités.

Choisissez une taille de stockage du cache qui peut contenir facilement l’ensemble actif de fichiers de travail ainsi que de l’espace supplémentaire pour les métadonnées et d’autres surcharges.

Le débit et la taille du cache affectent également le nombre de cibles de stockage prises en charge pour un cache particulier. Si vous souhaitez utiliser plus de 10 cibles de stockage avec votre cache, vous devez choisir la valeur de taille de stockage de cache la plus élevée disponible pour votre taille de débit ou choisir l’une des configurations à débit élevé en lecture seule. Pour plus d’informations, consultez [Ajouter des cibles de stockage](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets).

Si vous avez besoin d’aide pour dimensionner correctement votre cache, contactez le service et le support Microsoft.

### <a name="choose-the-cache-type-for-your-needs"></a>Choisir le type de cache pour vos besoins

Lorsque vous choisissez la capacité de votre cache, vous remarquerez peut-être que certaines valeurs de débit ont des tailles de cache fixes, tandis que d’autres vous permettent de sélectionner des options de taille de cache multiples. Cela est dû au fait qu’il existe deux styles différents de l’infrastructure du cache :

* Caches standard : répertoriés sous **Cache en lecture-écriture​** dans le menu de débit

  Avec les caches standard, vous pouvez choisir parmi plusieurs valeurs de taille de cache. Ces caches peuvent être configurés pour le mode lecture seule ou pour la mise en cache en lecture et en écriture.

* Caches à débit élevé : répertoriés sous **Cache en lecture seule** dans le menu de débit

  Les configurations à débit élevé ont défini des tailles de cache, car elles sont préconfigurées avec des disques NVME. Elles sont conçues pour optimiser uniquement l’accès en lecture au fichier.

![Capture d’écran du menu de débit maximal dans le portail. Il existe plusieurs options de taille sous le titre « Mise en cache en lecture-écriture » et plusieurs sous l’en-tête « Lecture seule ».](media/rw-ro-cache-sizing.png)

Ce tableau explique quelques différences importantes entre les deux options.

| Attribut | Cache standard | Cache à débit élevé |
|--|--|--|
| Catégorie de menu débit |« Cache en lecture-écriture​ »| « Cache en lecture seule »|
| Tailles de débit | 2, 4 ou 8 Go/s | 4,5, 9 ou 16 Go/s |
| Tailles de cache | 3, 6 ou 12 To pour 2 Go/s<br/> 6, 12 ou 24 To pour 4 Go/s<br/> 12, 24 ou 48 To pour 8 Go/s| 21 To pour 4,5 Go/s <br/> 42 To pour 9 Go/s <br/> 84 To pour 16 Go/s |
| Nombre maximal de cibles de stockage | [10 ou 20](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets) selon la sélection de la taille du cache | 20 |
| Types de cibles de stockage compatibles | Objet BLOB Azure, stockage NFS local, objet BLOB compatible NFS | stockage NFS local <br/>Le stockage d’objets BLOB NFS est en version préliminaire pour cette combinaison |
| Styles de mise en cache | Mise en cache en lecture ou mise en cache en lecture/écriture | Mise en cache en lecture uniquement |
| Le cache peut être arrêté pour limiter les coûts quand il n’est pas nécessaire | Oui | Non |

Découvrez plus d'informations sur ces options :

* [Nombre maximal de cibles de stockage](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)
* [Modes de mise en cache en lecture et en écriture](cache-usage-models.md#basic-file-caching-concepts)

## <a name="enable-azure-key-vault-encryption-optional"></a>Activer le chiffrement Azure Key Vault (facultatif)

Si vous souhaitez gérer les clés de chiffrement utilisées pour votre stockage de cache, fournissez vos informations Azure Key Vault sur la page **Clés de chiffrement de disque**. Le coffre de clés doit se trouver dans la même région et dans le même abonnement que le cache.

Vous pouvez ignorer cette section si vous n’avez pas besoin de clés gérées par le client. Par défaut, Azure chiffre les données avec des clés gérées par Microsoft. En savoir plus sur le [chiffrement de stockage Azure](../storage/common/storage-service-encryption.md).

> [!NOTE]
> Après avoir créé le cache, vous ne pouvez plus passer d’une clé gérée par Microsoft à une clé gérée par le client.

Pour obtenir une explication complète du processus de chiffrement avec clé gérée par le client, lisez [Utiliser des clés de chiffrement gérées par le client pour Azure HPC Cache](customer-keys.md).

![Capture d’écran de la page des clés de chiffrement avec l’option « Managé par le client » sélectionnée et les formulaires de configuration « Paramètres de clé du client » et « Identités gérées » affichés.](media/create-encryption.png)

Sélectionnez **Gérée par le client** pour choisir le chiffrement avec clé gérée par le client. Les champs de spécification du coffre de clés s’affichent. Sélectionnez le coffre Azure Key Vault à utiliser, puis sélectionnez la clé et la version à utiliser pour ce cache. La clé doit être une clé RSA 2048 bits. Vous pouvez créer un coffre de clés, une clé ou une version de clé à partir de cette page.

Cochez la case **Toujours utiliser la version actuelle de la clé** si vous souhaitez utiliser la [permutation automatique des clés](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys).

Si vous voulez utiliser une identité managée spécifique pour ce cache, configurez-la dans la section **Identités managées**. Consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md) pour en savoir plus.

> [!NOTE]
> Vous pouvez modifier l’identité attribuée après la création du cache.

Si vous utilisez une identité gérée attribuée par le système ou une identité affectée par l’utilisateur qui n’a pas encore accès à votre coffre de clés, vous devez suivez une étape supplémentaire après avoir créé le cache. Cette étape manuelle autorise l’identité managée du cache à utiliser le coffre de clés.

* Consultez [Choisir une option d’identité gérée pour le cache](customer-keys.md#choose-a-managed-identity-option-for-the-cache) pour comprendre les différences dans les paramètres d’identité managée.
* Pour plus d’informations, consultez [Autoriser le chiffrement Azure Key Vault à partir du cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed) pour en savoir plus sur l’étape manuelle.

## <a name="add-resource-tags-optional"></a>Ajouter des balises de ressources (facultatif)

La page **Balises** vous permet d’ajouter des [balises de ressources](../azure-resource-manager/management/tag-resources.md) à votre instance Azure HPC Cache.

## <a name="finish-creating-the-cache"></a>Terminer la création du cache

Après avoir configuré le nouveau cache, cliquez sur l’onglet **Vérifier + créer**. Le portail valide vos choix et vous permet de les passer en revue. Si tout est correct, cliquez sur **Créer**.

La création du cache prend environ 10 minutes. Vous pouvez suivre la progression dans le panneau de notifications du portail Azure.

![capture d’écran des pages de création de cache « déploiement en cours » et « notifications » sur le portail](media/hpc-cache-deploy-status.png)

À l’issue de l’étape de création, une notification s’affiche avec un lien vers la nouvelle instance Azure HPC Cache, et le cache apparaît dans la liste **Ressources** de votre abonnement.

![capture d’écran de l’instance de cache Azure HPC Cache dans le portail Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Si votre cache utilise des clés de chiffrement gérées par le client et requiert une étape d’autorisation manuelle après la création, le cache peut apparaître dans la liste des ressources avant que l’état de son déploiement ne devienne Terminé. Dès que l’état du cache est **En attente de la clé**, vous pouvez [l’autoriser](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed) à utiliser le coffre de clés.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Créer le cache avec Azure CLI

[Configurez Azure CLI pour Azure HPC Cache](./az-cli-prerequisites.md).

> [!NOTE]
> Azure CLI ne prend actuellement pas en charge la création d’un cache avec des clés de chiffrement gérées par le client. Utilisez le portail Azure.

Utilisez la commande [az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create) pour créer une nouvelle instance Azure HPC Cache.

Fournissez ces valeurs :

* Nom du groupe de ressources en cache
* Nom du cache
* Région Azure
* Sous-réseau de cache, au format suivant :

  `--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  Le sous-réseau de cache a besoin d’au moins 64 adresses IP (/24), et il ne peut pas héberger d’autres ressources.

* La capacité du cache. Deux valeurs définissent le débit maximal de votre instance Azure HPC Cache :

  * Taille du cache (en Go)
  * La référence des machines virtuelles utilisées dans l’infrastructure du cache

  [az hpc-cache skus list](/cli/azure/hpc-cache/skus) affiche les références SKU disponibles et les options de taille de cache valides pour chacune d’elles. Les options de taille de cache vont de 3 à 48 To, mais seules certaines valeurs sont prises en charge.

  Ce graphique montre les combinaisons de taille de cache et de référence SKU valides au moment de la préparation de ce document (juillet 2020).

  | Taille du cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 Go    | oui         | non          | Non          |
  | 6144 Go    | Oui         | oui         | Non          |
  | 12288 Go   | oui         | oui         | oui         |
  | 24576 Go   | Non          | oui         | oui         |
  | <49152 Go   | Non          | non          | Oui         |

  Si vous souhaitez utiliser plus de 10 cibles de stockage avec votre cache, choisissez la valeur de taille de cache la plus élevée disponible pour votre SKU. Ces configurations prennent en charge jusqu’à 20 cibles de stockage.

  Pour obtenir des informations importantes sur la tarification, le débit et la taille de votre cache en fonction de votre flux de travail, consultez la section **Définir la capacité du cache** sous l’onglet Instructions du portail.

Exemple de création de cache :

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

La création du cache prend plusieurs minutes. En cas de réussite, la commande create retourne une sortie semblable à celle-ci :

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Le message inclut des informations utiles, notamment les éléments suivants :

* Adresses de montage client : utilisez ces adresses IP lorsque vous êtes prêt à connecter les clients au cache. Pour en savoir plus, consultez [Monter le cache Azure HPC Cache](hpc-cache-mount.md).
* État de la mise à niveau : lorsqu’une mise à jour logicielle est publiée, ce message est modifié. Vous pouvez [mettre à niveau du logiciel de cache](hpc-cache-manage.md#upgrade-cache-software) manuellement à un moment approprié, ou la mise à niveau sera appliquée automatiquement après plusieurs jours.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Le module PowerShell Az.HPCCache est en préversion publique. Cette préversion est fournie sans contrat de niveau de service. Il n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Spécifications

Si vous choisissez d’utiliser PowerShell localement, cet article vous demande d’installer le module Az PowerShell et de vous connecter à votre compte Azure avec l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Pour en savoir plus sur l’installation du module Az PowerShell, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps). Si vous choisissez d’utiliser Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../cloud-shell/overview.md) pour plus d’informations.

> [!IMPORTANT]
> Tant que le module PowerShell **Az.HPCCache** est en préversion, vous devez l’installer séparément à l’aide de l’applet de commande `Install-Module`. Une fois que ce module PowerShell sera en disponibilité générale, il fera partie intégrante des versions futures du module PowerShell Az et sera disponible en mode natif dans Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Créer le cache avec Azure PowerShell

> [!NOTE]
> Azure PowerShell ne prend actuellement pas en charge la création d’un cache avec des clés de chiffrement gérées par le client. Utilisez le portail Azure.

Utilisez l’applet de commande [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) pour créer un cache HPC Azure Cache.

Remplacez les valeurs suivantes :

* Nom du groupe de ressources en cache
* Nom du cache
* Région Azure
* Sous-réseau de cache, au format suivant :

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  Le sous-réseau de cache a besoin d’au moins 64 adresses IP (/24), et il ne peut pas héberger d’autres ressources.

* La capacité du cache. Deux valeurs définissent le débit maximal de votre instance Azure HPC Cache :

  * Taille du cache (en Go)
  * La référence des machines virtuelles utilisées dans l’infrastructure du cache

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) affiche les références SKU disponibles et les options de taille de cache valides pour chacune d’elles. Les options de taille de cache vont de 3 à 48 To, mais seules certaines valeurs sont prises en charge.

  Ce graphique montre les combinaisons de taille de cache et de référence SKU valides au moment de la préparation de ce document (juillet 2020).

  | Taille du cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 Go    | oui         | non          | Non          |
  | 6144 Go    | oui         | oui         | non          |
  | 12 288 Go   | Oui         | oui         | Oui         |
  | 24 576 Go   | non          | oui         | Oui         |
  | 49 152 Go   | non          | non          | oui         |

  Pour obtenir des informations importantes sur la tarification, le débit et la taille de votre cache en fonction de votre flux de travail, consultez la section **Définir la capacité du cache** sous l’onglet Instructions du portail.

Exemple de création de cache :

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

La création du cache prend plusieurs minutes. En cas de réussite, la commande create retourne la sortie suivante :

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Le message inclut des informations utiles, notamment les éléments suivants :

* Adresses de montage client : utilisez ces adresses IP lorsque vous êtes prêt à connecter les clients au cache. Pour en savoir plus, consultez [Monter le cache Azure HPC Cache](hpc-cache-mount.md).
* État de la mise à niveau : lorsqu’une mise à jour logicielle est publiée, ce message est modifié. Vous pouvez [mettre à niveau le logiciel de cache](hpc-cache-manage.md#upgrade-cache-software) manuellement à un moment approprié, ou la mise à niveau sera appliquée automatiquement après plusieurs jours.

---

## <a name="next-steps"></a>Étapes suivantes

Une fois votre cache apparaît dans la liste **Ressources**, vous pouvez passer à l’étape suivante.

* [Définissez des cibles de stockage](hpc-cache-add-storage.md) pour permettre à votre cache d’accéder à vos sources de données.
* Si vous utilisez des clés de chiffrement gérées par le client, vous devez [autoriser le chiffrement Azure Key Vault](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed) à partir de la page de présentation du cache pour terminer la configuration de ce dernier, suivez les conseils dans [Utiliser les clés de chiffrement gérées par le client](customer-keys.md). Vous devez effectuer cette étape avant de pouvoir ajouter un stockage.
