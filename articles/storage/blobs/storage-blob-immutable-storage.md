---
title: Stockage immuable pour les objets blob du Stockage Azure | Microsoft Docs
description: Le Stockage Azure assure la prise en charge des disques optiques non réinscriptibles (WORM) pour le Stockage (d’objets) Blob, qui permettent aux utilisateurs de stocker des données dans un état immuable et non effaçable sur une période donnée.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 11/05/2018
ms.author: hux
ms.component: blobs
ms.openlocfilehash: d3d83e240fec692d5aa655923637910006c7a62f
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261468"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Stocker des données vitales pour l’entreprise dans le stockage Blob Azure

Le stockage immuable pour le Stockage (d’objets) Blob Azure permet aux utilisateurs de stocker des données critiques pour l’entreprise dans un état WORM (disque optique non réinscriptible). Cet état les rend non effaçables et immuables pour une durée spécifiée par l’utilisateur. Au cours de cette période de conservation, il est possible de créer et de lire des objets blob, mais non de les modifier ou de les supprimer.

## <a name="overview"></a>Vue d’ensemble

Le stockage immuable permet aux institutions financières et aux secteurs associés, en particulier les organisations de courtage, de stocker des données en toute sécurité. Il peut également être utilisé dans n’importe quel scénario pour protéger les données vitales contre la suppression.  

Les applications typiques incluent :

- **Conformité réglementaire** : le stockage immuable pour le Stockage Blob Azure permet aux organisations de respecter entre autres les réglementations SEC 17a-4(f), CFTC 1.31(d) et FINRA.

- **Conservation sécurisée des documents** : avec le Stockage Blob, les données ne sont ni modifiables ni supprimables par les utilisateurs, même s’ils disposent d’un compte avec privilèges administratifs.

- **Archivage juridique** : le stockage immuable pour le Stockage Blob Azure permet aux utilisateurs de stocker dans un état de protection inviolable des informations sensibles critiques dans le cadre d’une enquête criminelle ou portant sur un litige, pour la durée souhaitée.

Le stockage immuable permet les prises en charge suivantes :

- **Prise en charge de la stratégie de conservation limitée dans le temps** : les utilisateurs définissent des stratégies pour stocker les données pendant une période donnée.

- **Prise en charge de la stratégie d’archivage juridique** : lorsque la période de conservation est inconnue, les utilisateurs peuvent définir des stratégies d’archivage juridique pour stocker les données de telle sorte qu’elles ne soient pas modifiables tant que les stratégies n’ont pas été levées.  Lorsqu’une conservation juridique est définie, des objets Blob peuvent être créés et lus, mais ils ne peuvent pas être modifiés ni supprimés. Chaque archivage juridique est associé à une balise alphanumérique définie par l’utilisateur, utilisée comme chaîne d’identificateur (comme pour l’ID d’un cas).

- **Prise en charge de tous les niveaux d’objets blob** : les stratégies WORM sont indépendantes du niveau de Stockage Blob Azure et s’appliquent à tous les niveaux (chaud, froid et archive). Les clients peuvent transférer les données au niveau ayant le coût le plus adapté à leurs charges de travail tout en conservant l’immuabilité des données.

- **Configuration du niveau du conteneur** : les utilisateurs peuvent configurer des stratégies de conservation limitée dans le temps et des balises d’archivage juridique au niveau du conteneur. Grâce à de simples paramètres au niveau du conteneur, ils ont la possibilité de créer et verrouiller des stratégies de conservation limitée dans le temps, d’étendre les périodes de conservation, de définir et supprimer des archivages juridiques, etc. Ces stratégies s’appliquent à tous les objets blob du conteneur, anciens ou nouveaux.

- **Prise en charge des enregistrements d’audit** : chaque conteneur comprend un journal d’audit. Il présente jusqu’à cinq commandes pour les stratégies de conservation limitée dans le temps verrouillées, avec un maximum de trois journaux pour les extensions de la période de conservation. Dans le cas de la conservation limitée dans le temps, il contient l’identifiant utilisateur, le type de commande, les timestamps et la période de conservation. En ce qui concerne les stratégies d’archivage juridique, il comporte l’identifiant utilisateur, le type de commande, les timestamps et les balises d’archivage juridique. Ce journal est conservé pendant toute la durée de vie du conteneur, conformément aux instructions réglementaires SEC 17a-4(f). Le [Journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) est un journal plus complet de toutes les activités du plan de contrôle. Il est de la responsabilité de l’utilisateur de stocker ces journaux de manière persistante, car ceux-ci peuvent être nécessaires à des fins réglementaires ou autres.

Le stockage immuable est activé dans toutes les régions publiques Azure.

## <a name="how-it-works"></a>Fonctionnement

Le stockage immuable du Stockage Blob Azure prend en charge deux types de stratégies WORM ou immuables : la conservation limitée dans le temps et l’archivage juridique. Pour plus d’informations sur la création de ces stratégies non modifiables, voir la section [Bien démarrer](#getting-started).

Lorsqu’une stratégie de conservation limitée dans le temps ou d’archivage juridique est appliquée sur un conteneur, tous les objets blob existants prennent l’état immuable (protégé contre l’écriture et la suppression). Tous les nouveaux objets blob chargés dans le conteneur auront également cet état.

> [!IMPORTANT]
> Une stratégie de conservation limitée dans le temps doit être *verrouillée* pour que l’objet blob ait un état immuable (protégé contre l’écriture et la suppression) conformément entre autres à la réglementation SEC 17a-4(f). Il est recommandé de verrouiller la stratégie au bout d’un délai raisonnable, en général dans les 24 heures. Nous conseillons de ne pas utiliser l’état *déverrouillé* pour d’autres raisons que des évaluations de fonctionnalités à court terme.

Lorsqu’une stratégie de conservation limitée dans le temps est appliquée à un conteneur, tous les objets blob de ce conteneur conserveront l’état immuable pendant la période de conservation *effective*. La durée de rétention effective pour les objets Blob existants est égale à la différence entre le temps de création de l’objet Blob et l’intervalle de rétention spécifié par l’utilisateur.

Pour les nouveaux objets Blob, la durée de rétention effective est égale à l’intervalle de rétention spécifié par l’utilisateur. Dans la mesure où les utilisateurs peuvent étendre la période de conservation, le stockage immuable utilise la valeur la plus récente de la période de conservation spécifiée par l’utilisateur pour calculer la durée de conservation effective.

> [!TIP]
> Exemple :
>
> Un utilisateur crée une stratégie de conservation limitée dans le temps avec une période de conservation de cinq ans.
>
> L’objet blob qui se trouve dans ce conteneur, testblob1, a été créé un an plus tôt. La période de conservation effective de testblob1 est de quatre ans.
>
> Un nouvel objet blob, testblob2, est maintenant chargé dans le conteneur. La période de conservation effective de ce nouvel objet blob est de cinq ans.

### <a name="legal-holds"></a>Conservation juridique

En cas de définition d’une stratégie d’archivage juridique, tous les objets blob (anciens et nouveaux) conservent l’état immuable jusqu’à ce qu’elle soit levée. Pour savoir comment définir et lever une stratégie de conservation à des fins juridiques, voir la section [Bien démarrer](#getting-started).

Un conteneur peut avoir à la fois une stratégie d’archivage juridique et une stratégie de conservation limitée dans le temps. Tous les objets blob de ce conteneur conservent l’état immuable jusqu’à ce que toutes les stratégies d’archivage juridique aient été levées, même si leur période de conservation effective est écoulée. À l’inverse, un objet blob demeure dans un état immuable jusqu’à expiration de la période de conservation effective, même si toutes les stratégies d’archivage juridique ont été levées.

Le tableau suivant montre les types d’opérations blob désactivées dans chaque scénario immuable. Pour plus d’informations, voir la documentation [API du service BLOB Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scénario  |État des objets blob  |Opérations blob non autorisées  |
|---------|---------|---------|
|L’intervalle de rétention effective sur l’objet Blob n’a pas encore expiré et/ou une conservation juridique est définie     |Immuable : non modifiable et non supprimable         |Delete Container, Delete Blob, Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|L’intervalle de rétention effective sur l’objet Blob a expiré     |Non modifiable seulement (suppressions autorisées)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Stratégies d’archivage juridique levées ; aucune stratégie de conservation limitée dans le temps dans le conteneur     |Mutable         |Aucun         |
|Aucune stratégie WORM créée (conservation limitée dans le temps ou archivage juridique)     |Mutable         |Aucun         |

<sup>1</sup> L’application peut appeler cette opération une seule fois pour créer un objet blob. Les opérations suivantes sur l’objet blob ne sont pas autorisées.

> [!NOTE]
>
> Le stockage immuable est disponible uniquement dans les comptes de stockage Blob et comptes de stockage universels V2. Le compte doit être créé avec [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Tarifs

L’utilisation de cette fonctionnalité n’entraîne aucun coût supplémentaire. Les données immuables sont facturées au même tarif que les données modifiables classiques. Pour plus d’informations sur les prix du stockage Blob Azure, consultez la [page des tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).


## <a name="getting-started"></a>Prise en main

Les dernières versions du [portail Azure](http://portal.azure.com) et [d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ainsi que la préversion [d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018) prennent en charge le stockage immuable pour le stockage Blob Azure.

### <a name="azure-portal"></a>Portail Azure

1. Créez un nouveau conteneur ou sélectionnez un conteneur existant pour stocker des objets Blob qui doivent demeurer dans l’état immuable.
 Le conteneur doit se trouver dans un compte de stockage Blob ou un compte de stockage universel V2.
2. Sélectionnez **Stratégie d’accès** dans les paramètres du conteneur. Ensuite, sélectionnez **+ Ajouter une stratégie** sous **Stockage Blob immuable**.

    ![Paramètres du conteneur sur le portail](media/storage-blob-immutable-storage/portal-image-1.png)

3. Pour activer la conservation limitée dans le temps, sélectionnez **Conservation limitée dans le temps** dans le menu déroulant.

    ![Sélection de « Conservation limitée dans le temps » sous « Type de stratégie »](media/storage-blob-immutable-storage/portal-image-2.png)

4. Entrez la période de conservation en jours (un jour minimum).

    ![Zone « Mettre à jour la période de conservation »](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Comme on le voit sur la capture d’écran, l’état initial de la stratégie est déverrouillé. Vous pouvez tester la fonctionnalité avec une période de conservation plus courte, et apporter des modifications à la stratégie avant de la verrouiller. Le verrouillage est essentiel pour la conformité à la réglementation SEC 17a-4 notamment.

5. Verrouillez la stratégie. Cliquez avec le bouton droit sur les points de suspension (**…**) ; le menu suivant s’affiche :

    ![« Verrouiller la stratégie » dans le menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Sélectionnez **Verrouiller la stratégie** ; l’état de la stratégie devient Verrouillé. Une fois la stratégie verrouillée, sa suppression n’est plus possible ; seules les extensions de la période de conservation seront autorisées.

6. Pour activer l’archivage juridique, sélectionnez **+ Ajouter une stratégie**. Sélectionnez **Archivage juridique** dans le menu déroulant.

    ![« Archivage juridique » dans le menu sous « Type de stratégie »](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Créez une stratégie d’archivage juridique avec une ou plusieurs balises.

    ![Zone « Nom de balise » sous le type de stratégie](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. Pour supprimer un archivage juridique, supprimez simplement la balise.

### <a name="azure-cli"></a>Azure CLI

La fonctionnalité est incluse dans les groupes de commandes suivants : `az storage container immutability-policy` et `az storage container legal-hold`. Exécutez `-h` sur ces groupes pour afficher les commandes.

### <a name="powershell"></a>PowerShell

[PowerShell version 4.4.0-preview](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180) prend en charge le stockage immuable.
Pour activer cette fonctionnalité, suivez les étapes ci-dessous :

1. Vérifiez que la dernière version de PowerShellGet est installée : `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Supprimez les installations précédentes d’Azure PowerShell.
3. Installez AzureRM : `Install-Module AzureRM –Repository PSGallery –AllowClobber`. Il est possible d’installer Azure de la même façon sur ce référentiel.
4. Installez la préversion des cmdlets du plan de gestion du stockage : `Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`.

La section [Exemple de code PowerShell](#sample-powershell-code) plus loin dans cet article illustre l’utilisation de cette fonctionnalité.

## <a name="client-libraries"></a>Bibliothèques clientes

Les bibliothèques de client suivantes prennent en charge le stockage immuable pour le Stockage Blob Azure :

- [Bibliothèque de client .NET version 7.2.0-preview et versions ultérieures](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Bibliothèque de client Node.js version 4.0.0 et versions ultérieures](https://www.npmjs.com/package/azure-arm-storage)
- [Bibliothèque de client Python version 2.0.0 Release Candidate 2 et versions ultérieures](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Bibliothèque cliente Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>Valeurs prises en charge

- La période de conservation minimale est d’un jour. Le maximum est de 400 ans.
- Le nombre maximal par compte de stockage de conteneurs avec stratégies immuables verrouillées est de 1 000.
- Le nombre maximal par compte de stockage de conteneurs avec paramètre d’archivage juridique est de 1 000.
- Le nombre maximal par conteneur de balises d’archivage juridique est de 10.
- La longueur maximale d’une balise d’archivage juridique est de 23 caractères alphanumériques. La longueur minimale est de trois caractères.
- Le nombre maximal par conteneur d’extensions autorisées de la période de conservation de stratégies immuables verrouillées est de trois.
- Pour un conteneur avec stratégie immuable verrouillée, cinq journaux de stratégie de conservation limitée dans le temps et 10 journaux de stratégie d’archivage juridique au maximum sont conservés pour la durée du conteneur.

## <a name="faq"></a>Forum Aux Questions

**Cette fonctionnalité s’applique-t-elle uniquement aux objets blob de blocs, ou aussi aux objets blob de page et d’ajout ?**

Le stockage immuable peut être utilisé avec n’importe quel type d’objet blob, mais nous recommandons de l’utiliser principalement pour les objets blob de blocs. Contrairement aux objets blob de blocs, les objets blob de page et d’ajout doivent être créés hors d’un conteneur WORM, avant d’y être copiés. Une fois la copie faite, il n’est plus possible de faire des *ajouts* à un objet blob d’ajout ni de modifier un objet blob de pages.

**Dois-je toujours créer un compte de stockage pour utiliser cette fonctionnalité ?**

Vous pouvez utiliser le stockage immuable avec tous les comptes de stockage universels V2 ou comptes de stockage Blob, qu’ils soient nouveaux ou existants. Cette fonctionnalité est disponible uniquement pour le stockage Blob.

**Que se passe-t-il si j’essaie de supprimer un conteneur avec une stratégie de rétention basée sur le temps *verrouillée* ou une conservation juridique ?**

L’opération Supprimer le conteneur échoue s’il existe un ou plusieurs objets blob ayant une stratégie de conservation limitée dans le temps verrouillée ou un archivage juridique. L’opération Supprimer le conteneur réussit uniquement s’il n’y a aucun objet blob avec une période de conservation ou avec un archivage juridique. Il est nécessaire de supprimer les objets blob pour pouvoir supprimer le conteneur.

**Que se passe-t-il si j’essaie de supprimer un compte de stockage avec un conteneur WORM disposant d’une stratégie de rétention basée sur le temps *verrouillée* ou d’une conservation juridique ?**

La suppression du compte de stockage échoue s’il s’agit d’un conteneur WORM disposant d’une stratégie de rétention basée sur le temps verrouillée ou d’une conservation juridique.  Il est nécessaire de supprimer tous les conteneurs WORM pour pouvoir supprimer le compte de stockage. Pour plus d’informations sur la suppression des conteneurs, voir la question précédente.

**Puis-je déplacer les données sur différents niveaux d’objets blob (chaud, froid, archive) lorsque l’objet blob est dans l’état immuable ?**

Oui, vous pouvez utiliser la commande Set Blob Tier pour déplacer des données sur d’autres niveaux d’objets blob tant en conservant leur état immuable. Le stockage immuable est pris en charge sur les niveaux d’objets blob chaud, froid et archive.

**Que se passe-t-il si je ne paie pas et que mon intervalle de rétention n’a pas expiré ?**

En cas de défaut de paiement, les stratégies de conservation des données normales s’appliquent, comme le stipulent les conditions générales de votre contrat avec Microsoft.

**Proposez-vous une période d’essai ou de grâce pour essayer la fonctionnalité ?**

Oui. Lors de la définition initiale d’une stratégie de conservation limitée dans le temps, celle-ci a pour état *déverrouillé*. Dans cet état, vous pouvez apporter les modifications souhaitées à la période de conservation, par exemple, l’augmenter, la diminuer ou même supprimer la stratégie. Une fois que la stratégie est verrouillée, elle reste verrouillée jusqu’à l’expiration de l’intervalle de rétention. Ceci empêche la suppression et la modification de l’intervalle de rétention. Il est vivement recommandé de n’utiliser l’état *déverrouillé* qu’à des fins d’évaluation et de verrouiller la stratégie dans les 24 heures. Ces pratiques permettent de respecter entre autres la réglementation SEC 17a-4(f).

**Cette fonctionnalité est-elle disponible dans les clouds nationaux et gouvernementaux ?**

Le stockage immuable n’est à l’heure actuelle disponible que dans les régions publiques Azure. Si un cloud national vous intéresse en particulier, envoyez un e-mail à azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Exemple de code PowerShell

L’exemple de script PowerShell suivant est fourni à titre de référence. Il crée un compte de stockage et un conteneur. Ensuite, il montre comment définir et lever des stratégies d’archivage juridique, créer et verrouiller une stratégie de conservation limitée dans le temps (ou stratégie d’immuabilité) et étendre la période de conservation.

Créer et tester le compte de stockage Azure :

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzureRMAccount
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzureRmResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzureRmStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzureRmStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzureRmStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzureRmStorageContainer -InputObject $containerObject2
```

Définir et supprimer des archivages juridiques :

```powershell
# Set a legal hold
Add-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzureRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzureRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Créer ou mettre à jour des stratégies d’immuabilité :
```powershell
# with an account name or container name
Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Récupérer des stratégies d’immuabilité :
```powershell
# Get an immutability policy
Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Verrouiller des stratégies d’immuabilité (ajouter - Force pour ignorer l’invite) :
```powershell
# with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Étendre des stratégies d’immuabilité :
```powershell

# with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Supprimer une stratégie d’immuabilité (ajouter - Force pour ignorer l’invite) :
```powershell
# with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
