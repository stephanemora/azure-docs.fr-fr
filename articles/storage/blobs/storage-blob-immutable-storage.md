---
title: Stockage non modifiable pour le Stockage Blob Azure (préversion) | Microsoft Docs
description: Le Stockage Azure assure la prise en charge des disques optiques non réinscriptibles (WORM) pour le Stockage (d’objets) Blob, qui permettent aux utilisateurs de stocker des données dans un état non modifiable et non effaçable sur une période donnée.
services: storage
author: sangsinh
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: sangsinh
ms.component: blobs
ms.openlocfilehash: cfc25906e926e8dd6687eeccd311a38653772c4d
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398996"
---
# <a name="store-business-critical-data-in-azure-blob-storage-preview"></a>Stocker des données critiques pour l’entreprise dans le Stockage Blob Azure (préversion)

Le stockage non modifiable pour le Stockage (d’objets) Blob Azure permet aux utilisateurs de stocker des données critiques pour l’entreprise dans un état WORM (disque optique non réinscriptible). Cet état les rend non effaçables et non modifiables pour une durée spécifiée par l’utilisateur. Au cours de cette période de rétention, il est possible de créer et de lire des objets blob, mais non de les modifier ou de les supprimer.

## <a name="overview"></a>Vue d’ensemble

Le stockage non modifiable permet aux institutions financières et aux secteurs associés, en particulier les organisations de courtage, de stocker des données en toute sécurité.

Les applications typiques incluent :

- **Conformité réglementaire** : le stockage non modifiable pour le Stockage Blob Azure permet aux organisations de respecter entre autres les réglementations SEC 17a-4(f), CFTC 1.31(d) et FINRA.

- **Rétention sécurisée des documents** : avec le Stockage Blob, les données ne sont ni modifiables ni supprimables par les utilisateurs, même s’ils disposent d’un compte avec privilèges administratifs.

- **Conservation à des fins juridiques** : le stockage non modifiable pour le Stockage Blob Azure permet aux utilisateurs de stocker dans un état de protection inviolable des informations sensibles critiques dans le cadre d’une enquête criminelle ou portant sur un litige, pour la durée souhaitée.

Le stockage non modifiable permet les prises en charge suivantes :

- **Prise en charge de la stratégie de rétention à durée définie** : les utilisateurs définissent des stratégies pour stocker les données pendant une période donnée.

- **Prise en charge de la stratégie de conservation à des fins juridiques** : lorsque la période de rétention est inconnue, les utilisateurs peuvent définir des stratégies de conservation à des fins juridiques pour stocker les données de telle sorte qu’elles ne soient pas modifiables tant que les stratégies n’ont pas été levées.  Lorsqu’une conservation juridique est définie, des objets Blob peuvent être créés et lus, mais ils ne peuvent pas être modifiés ni supprimés. Chaque stratégie de conservation à des fins juridiques est associée à une balise alphanumérique définie par l’utilisateur et servant de chaîne d’identificateur (comme un ID de cas).

- **Prise en charge de tous les niveaux d’objets blob** : les stratégies WORM sont indépendantes du niveau de Stockage Blob Azure et s’appliquent à tous les niveaux (chaud, froid et archive). Les clients peuvent stocker les données dans le niveau dont le coût est optimisé pour leurs charges de travail tout en empêchant toute modification des données.

- **Configuration du niveau du conteneur** : les utilisateurs peuvent configurer des stratégies de rétention à durée définie et des balises de conservation à des fins juridiques au niveau du conteneur. Grâce à de simples paramètres au niveau du conteneur, ils ont la possibilité de créer et verrouiller des stratégies de rétention à durée définie, d’étendre les périodes de rétention, de définir et supprimer des stratégies de conservation à des fins juridiques, etc. Ces stratégies s’appliquent à tous les objets blob du conteneur, anciens ou nouveaux.

- **Prise en charge des enregistrements d’audit** : chaque conteneur comprend un journal d’audit. Il présente jusqu’à cinq commandes pour les stratégies de rétention à durée définie verrouillées, avec un maximum de trois journaux pour les extensions de la période de rétention. Dans le cas de la rétention à durée définie, il contient l’identifiant utilisateur, le type de commande, les timestamps et la période de rétention. En ce qui concerne les stratégies de conservation à des fins juridiques, il comporte l’identifiant utilisateur, le type de commande, les timestamps et les balises de conservation à des fins juridiques. Ce journal est conservé pendant toute la durée de vie du conteneur, conformément aux instructions réglementaires SEC 17a-4(f). Le [Journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) est un journal plus complet de toutes les activités du plan de contrôle. Il est de la responsabilité de l’utilisateur de stocker ces journaux de manière permanente, si les obligations réglementaires ou autres l’imposent.

Le stockage non modifiable est activé dans toutes les régions publiques Azure.

## <a name="how-it-works"></a>Fonctionnement

Le stockage non modifiable du Stockage Blob Azure prend en charge deux types de stratégies WORM ou non modifiables : la rétention à durée définie et la conservation à des fins juridiques. Pour plus d’informations sur la création de ces stratégies non modifiables, voir la section [Bien démarrer](#Getting-started).

Lorsqu’une stratégie de rétention à durée définie ou de conservation à des fins juridiques est appliquée sur un conteneur, tous les objets blob existants prennent l’état non modifiable (protégé contre l’écriture et la suppression). Tous les nouveaux objets blob chargés dans le conteneur auront également cet état.

> [!IMPORTANT]
> Une stratégie de rétention à durée définie doit être *verrouillée* pour que l’objet blob ait un état non modifiable (protégé contre l’écriture et la suppression) conformément entre autres à la réglementation SEC 17a-4(f). Il est recommandé de verrouiller la stratégie au bout d’un délai raisonnable, en général dans les 24 heures. Nous conseillons de ne pas utiliser l’état *déverrouillé* pour d’autres raisons que des évaluations de fonctionnalités à court terme.

Lorsqu’une stratégie de rétention à durée définie est appliquée à un conteneur, tous les objets blob de ce conteneur conserveront l’état non modifiable pendant la période de rétention *effective*. La durée de rétention effective pour les objets Blob existants est égale à la différence entre le temps de création de l’objet Blob et l’intervalle de rétention spécifié par l’utilisateur. 

Pour les nouveaux objets Blob, la durée de rétention effective est égale à l’intervalle de rétention spécifié par l’utilisateur. Dans la mesure où les utilisateurs peuvent modifier la période de rétention, le stockage non modifiable utilise la valeur la plus récente de la période de rétention spécifiée par l’utilisateur pour calculer la durée de rétention effective.

> [!TIP]
> Exemple :
> 
> Un utilisateur crée une stratégie de rétention à durée définie avec une période de rétention de cinq ans.
>
> L’objet blob qui se trouve dans ce conteneur, testblob1, a été créé un an plus tôt. La période de rétention effective de testblob1 est de quatre ans.
>
> Un nouvel objet blob, testblob2, est maintenant chargé dans le conteneur. La période de rétention effective de ce nouvel objet blob est de cinq ans.

### <a name="legal-holds"></a>Conservation juridique

En cas de définition d’une stratégie de conservation à des fins juridiques, tous les objets blob (anciens et nouveaux) conservent l’état non modifiable jusqu’à ce qu’elle soit levée. Pour savoir comment définir et lever une stratégie de conservation à des fins juridiques, voir la section [Bien démarrer](#Getting-started).

Un conteneur peut avoir à la fois une stratégie de conservation à des fins juridiques et une stratégie de rétention à durée définie. Tous les objets blob de ce conteneur conservent l’état non modifiable jusqu’à ce que toutes les stratégies de conservation à des fins juridiques aient été levées, même si leur période de rétention effective est écoulée. À l’inverse, un objet blob demeure dans un état non modifiable jusqu’à expiration de la période de rétention effective, même si toutes les stratégies de conservation à des fins juridiques ont été levées.

Le tableau suivant montre les types d’opérations blob désactivées dans chaque scénario non modifiable. Pour plus d’informations, voir la documentation [API du service BLOB Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scénario  |État des objets blob  |Opérations blob non autorisées  |
|---------|---------|---------|
|L’intervalle de rétention effective sur l’objet Blob n’a pas encore expiré et/ou une conservation juridique est définie     |Immuable : non modifiable et non supprimable         |Delete Container, Delete Blob, Put Blob1, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|L’intervalle de rétention effective sur l’objet Blob a expiré     |Non modifiable seulement (suppressions autorisées)         |Put Blob, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|Stratégies de conservation à des fins juridiques levées ; aucune stratégie de rétention à durée définie dans le conteneur     |Mutable         |Aucun         |
|Aucune stratégie WORM créée (rétention à durée définie ou conservation à des fins juridiques)     |Mutable         |Aucun         |

> [!NOTE]
> Les premières opérations Put Blob, Put Block List et Put Block nécessaires pour créer un objet blob sont autorisées dans les deux premiers scénarios du tableau précédent. Les opérations suivantes ne sont pas autorisées.
>
> Le stockage non modifiable n’est disponible que dans les comptes de Stockage Blob et GPv2. Il doit être créé avec [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Tarifs

L’utilisation de cette fonctionnalité n’entraîne aucun coût supplémentaire. Les données non modifiables sont facturées au même tarif que les données modifiables classiques. Pour plus d’informations sur la tarification, voir la [page Prix du Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="restrictions"></a>Restrictions

Les exigences suivantes s’appliquent dans la préversion publique :

- *Ne stockez pas de données de production ou de données critiques pour l’entreprise.*
- Toutes les restrictions liées à l’accord de confidentialité et à la préversion s’appliquent.

## <a name="getting-started"></a>Prise en main

Les dernières versions du [Portail Azure](http://portal.azure.com), [d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et [d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018) prennent en charge le stockage non modifiable pour le Stockage Blob Azure.

### <a name="azure-portal"></a>Portail Azure

1. Créez un nouveau conteneur ou sélectionnez un conteneur existant pour stocker des objets Blob qui doivent demeurer dans l’état immuable.
 Le conteneur doit se trouver dans un compte de stockage GPv2.
2. Sélectionnez **Stratégie d’accès** dans les paramètres du conteneur. Ensuite, sélectionnez **+ Ajouter une stratégie** sous **Stockage Blob non modifiable**.

    ![Paramètres du conteneur sur le portail](media/storage-blob-immutable-storage/portal-image-1.png)

3. Pour activer la rétention à durée définie, sélectionnez **Rétention à durée définie** dans le menu déroulant.

    ![Sélection de « Rétention à durée définie » sous « Type de stratégie »](media/storage-blob-immutable-storage/portal-image-2.png)

4. Entrez la période de rétention en jours (un jour minimum).

    ![Zone « Mettre à jour la période de rétention »](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Comme on le voit sur la capture d’écran, l’état initial de la stratégie est déverrouillé. Vous pouvez tester la fonctionnalité avec une période de rétention plus courte, et apporter des modifications à la stratégie avant de la verrouiller. Le verrouillage est essentiel pour la conformité à la réglementation SEC 17a-4 notamment.

5. Verrouillez la stratégie. Cliquez avec le bouton droit sur les points de suspension (**…**) ; le menu suivant s’affiche :

    ![« Verrouiller la stratégie » dans le menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Sélectionnez **Verrouiller la stratégie** ; l’état de la stratégie devient Verrouillé. Une fois la stratégie verrouillée, sa suppression n’est plus possible ; seules les extensions de la période de rétention seront autorisées.

6. Pour activer la conservation à des fins juridiques, sélectionnez **+ Ajouter une stratégie**. Sélectionnez **Conservation à des fins juridiques** dans le menu déroulant.

    ![« Conservation à des fins juridiques » dans le menu sous « Type de stratégie »](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Créez une stratégie de conservation à des fins juridiques avec une ou plusieurs balises.

    ![Zone « Nom de balise » sous le type de stratégie](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="azure-cli-20"></a>Azure CLI 2.0

Installez [l’extension Azure CLI](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) avec `az extension add -n storage-preview`.

Si vous avez déjà installé l’extension, exécutez la commande suivante pour activer le stockage non modifiable : `az extension update -n storage-preview`.

La fonctionnalité est incluse dans les groupes de commandes suivants : `az storage container immutability-policy` et `az storage container legal-hold`. Exécutez `-h` sur ces groupes pour afficher les commandes.

### <a name="powershell"></a>PowerShell

[PowerShell version 4.4.0-preview](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180) prend en charge le stockage non modifiable.
Pour activer cette fonctionnalité, suivez les étapes ci-dessous :

1. Vérifiez que la dernière version de PowerShellGet est installée : `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Supprimez les installations précédentes d’Azure PowerShell.
3. Installez AzureRM : `Install-Module AzureRM –Repository PSGallery –AllowClobber`. Il est possible d’installer Azure de la même façon sur ce référentiel.
4. Installez la préversion des cmdlets du plan de gestion du stockage : `Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`.

La section [Exemple de code PowerShell](#sample-powershell-code) plus loin dans cet article illustre l’utilisation de cette fonctionnalité.

## <a name="client-libraries"></a>Bibliothèques clientes

Les bibliothèques de client suivantes prennent en charge le stockage non modifiable pour le Stockage Blob Azure :

- [Bibliothèque de client .NET version 7.2.0-preview et versions ultérieures](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Bibliothèque de client Node.js version 4.0.0 et versions ultérieures](https://www.npmjs.com/package/azure-arm-storage)
- [Bibliothèque de client Python version 2.0.0 Release Candidate 2 et versions ultérieures](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Valeurs prises en charge

- La période de rétention minimale est d’un jour. Le maximum est de 400 ans.
- Le nombre maximal par compte de stockage de conteneurs avec stratégies non modifiables verrouillées est de 1 000.
- Le nombre maximal par compte de stockage de conteneurs avec paramètre de conservation à des fins juridiques est de 1 000.
- Le nombre maximal par conteneur de balises de conservation à des fins juridiques est de 10.
- La longueur maximale d’une balise de conservation à des fins juridiques est de 23 caractères alphanumériques. La longueur minimale est de trois caractères.
- Le nombre maximal par conteneur d’extensions autorisées de la période de rétention de stratégies non modifiables verrouillées est de trois.
- Pour un conteneur avec stratégie non modifiable verrouillée, cinq journaux de stratégie de rétention à durée définie et 10 journaux de stratégie de conservation à des fins juridiques au maximum sont conservés pour la durée du conteneur.

## <a name="faq"></a>Forum Aux Questions

**Cette fonctionnalité s’applique-t-elle uniquement aux objets blob de blocs, ou aussi aux objets blob de page et d’ajout ?**

Le stockage non modifiable peut être utilisé avec n’importe quel type d’objet blob,  mais il est recommandé de s’en servir principalement pour les objets blob de blocs. Contrairement aux objets blob de blocs, les objets blob de page et d’ajout doivent être créés hors d’un conteneur WORM, avant d’y être copiés. Une fois la copie faite, il n’est plus possible de faire des *ajouts* à un objet blob d’ajout ni de modifier un objet blob de pages.

**Dois-je toujours créer un compte de stockage pour utiliser cette fonctionnalité ?**

Vous pouvez utiliser le stockage non modifiable avec des comptes GPv2 existants ou sur de nouveaux comptes de stockage de type GPv2. Cette fonctionnalité n’est disponible qu’avec le Stockage Blob.

**Que se passe-t-il si j’essaie de supprimer un conteneur avec une stratégie de rétention basée sur le temps *verrouillée* ou une conservation juridique ?**

L’opération Supprimer le conteneur échoue s’il s’agit d’au moins un objet blob assorti d’une stratégie de rétention à durée définie verrouillée ou d’une stratégie de conservation à des fins juridiques. Cette règle est vraie même si les données ont fait l’objet d’une [suppression réversible](storage-blob-soft-delete.md). L’opération Delete Container réussit si aucun objet blob ne dispose d’un intervalle de rétention actif ou d’une conservation juridique. Il est nécessaire de supprimer les objets blob pour pouvoir supprimer le conteneur. 

**Que se passe-t-il si j’essaie de supprimer un compte de stockage avec un conteneur WORM disposant d’une stratégie de rétention basée sur le temps *verrouillée* ou d’une conservation juridique ?**

La suppression du compte de stockage échoue s’il s’agit d’un conteneur WORM disposant d’une stratégie de rétention basée sur le temps verrouillée ou d’une conservation juridique.  Il est nécessaire de supprimer tous les conteneurs WORM pour pouvoir supprimer le compte de stockage. Pour plus d’informations sur la suppression des conteneurs, voir la question précédente.

**Puis-je déplacer les données sur différents niveaux d’objets blob (chaud, froid, archive) lorsque l’objet blob est dans l’état immuable ?**

Oui, vous pouvez utiliser la commande Set Blob Tier pour déplacer des données sur d’autres niveaux d’objets blob tant en conservant leur état immuable. Le stockage non modifiable est pris en charge sur les niveaux d’objets blob chaud, froid et archive.

**Que se passe-t-il si je ne paie pas et que mon intervalle de rétention n’a pas expiré ?**

En cas de défaut de paiement, les stratégies de conservation des données normales s’appliquent, comme le stipulent les conditions générales de votre contrat avec Microsoft.

**Proposez-vous une période d’essai ou de grâce pour essayer la fonctionnalité ?**

Oui. Lors de la définition initiale d’une stratégie de rétention à durée définie, celle-ci a pour état *déverrouillé*. Dans cet état, vous pouvez apporter les modifications souhaitées à la période de rétention, par exemple, l’augmenter, la diminuer ou même supprimer la stratégie. Une fois la stratégie verrouillée, elle le reste pour toujours afin d’en empêcher la suppression. De plus, l’intervalle de rétention ne peut plus être diminué si la stratégie est verrouillée. Il est vivement recommandé de n’utiliser l’état *déverrouillé* qu’à des fins d’évaluation et de verrouiller la stratégie dans les 24 heures. Ces pratiques permettent de respecter entre autres la réglementation SEC 17a-4(f).

**Cette fonctionnalité est-elle disponible dans les clouds nationaux et gouvernementaux ?**

Le stockage non modifiable n’est à l’heure actuelle disponible que dans les régions publiques Azure. Si un cloud national vous intéresse en particulier, envoyez un e-mail à azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Exemple de code PowerShell

L’exemple de script PowerShell suivant est fourni à titre de référence. Il crée un compte de stockage et un conteneur. Ensuite, il montre comment définir et lever des stratégies de conservation à des fins juridiques, créer et verrouiller une stratégie de rétention à durée définie (ou stratégie d’immuabilité) et étendre la période de rétention.

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
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind Storage

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

# Set a legal hold
Add-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag tag1,tag2

# Set a legal hold with an account object
Add-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag tag3

# Set a legal hold with a container object
Add-AzureRmStorageContainerLegalHold -Container $containerObject -Tag tag4,tag5

# Clear a legal hold
Remove-AzureRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag tag2

# Clear a legal hold with an account object
Remove-AzureRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag tag3,tag5

# Clear a legal hold with a container object
Remove-AzureRmStorageContainerLegalHold -Container $containerObject -Tag tag4

# Create or update an immutability policy
## with an account name or container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

## with an account object
Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

## with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

## with an immutability policy object
Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5

# Get an immutability policy
Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# Get an immutability policy with an account object
Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# Get an immutability policy with a container object
Get-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject

# Lock an immutability policy (add -Force to dismiss the prompt)
## with an immutability policy object

$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

## with an account name or container name
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

## with an account object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

## with a container object
$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force

# Extend an immutability policy
## with an immutability policy object

$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

## with an account name or container name
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

## with an account object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

## with a container object
$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy

# Remove an immutability policy (add -Force to dismiss the prompt)
## with an immutability policy object
$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

## with an account name or container name
Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

## with an account object
Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

## with a container object
Remove-AzureRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag
    
```
