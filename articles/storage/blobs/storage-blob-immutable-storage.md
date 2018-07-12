---
title: Fonctionnalité de stockage immuable de stockage Blob Azure (préversion) | Microsoft Docs
description: Le stockage Azure offre dorénavant la prise en charge WORM pour le stockage d’objets Blob, vous permettant de stocker des données dans un état non modifiable et non supprimable pendant un intervalle de temps spécifié par l’utilisateur. Cette fonctionnalité permet à des organisations de nombreux secteurs, particulièrement les courtiers/revendeurs, de stocker des données conformément à SEC 17a-4(f) et d’autres législations.
services: storage
author: sangsinh
manager: twooley
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: sangsinh
ms.openlocfilehash: 195537b271c442b954d6d6e6fa8d1491c07822e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970242"
---
# <a name="immutable-storage-feature-of-azure-blob-storage-preview"></a>Fonctionnalité de stockage immuable de stockage Blob Azure (préversion)

La fonctionnalité de stockage immuable pour les objets Blob Azure permet aux utilisateurs de stocker des données commerciales sensibles dans un stockage d’objets Blob Azure dans un état WORM (non réinscriptible). Cet état rend ces données non supprimables et non modifiables pendant un intervalle de temps spécifié par l’utilisateur. Les objets Blob peuvent être créés et lus, mais ne peuvent être modifiés ni supprimés pendant la durée de l’intervalle de rétention.

## <a name="overview"></a>Vue d'ensemble

Cette fonctionnalité permet à des organisations de nombreux secteurs, particulièrement les courtiers/revendeurs, de stocker des données conformément à SEC 17a-4(f) et d’autres législations.

Les applications typiques incluent :

- **Conformité aux normes** : la fonctionnalité de stockage immuable pour les objets Blob Azure est conçue pour aider les institutions financières et autres secteurs similaires à répondre aux normes SEC 17a-4(f), CFTC 1.31©-(d), FINRA etc.

- **Rétention des documents sécurisée** : les utilisateurs profitent d’une protection des données maximale. Le service de stockage d’objets Blob s’assure que les données ne puissent être modifiées ni supprimées par un utilisateur, y compris un utilisateur disposant de droits d’administrateur.

- **Conservation à des fins juridiques** : le stockage immuable pour les objets Blob Azure permet aux utilisateurs de stocker des informations sensibles dans une enquête de litige ou pénale dans un état de protection inviolable pour la durée désirée.

Cette fonctionnalité offre :

- **Prise en charge de stratégie de rétention basée sur le temps :** les utilisateurs définissent des stratégies pour stocker les données pendant un intervalle de temps spécifique.

- **Prise en charge de stratégie de conservation à des fins juridiques :** lorsque l’intervalle de rétention est inconnu, les utilisateurs peuvent définir des stratégies de conservation à des fins juridiques pour stocker des données de façon immuable jusqu’à ce quelles soient levées.  Lorsqu’une conservation juridique est définie, des objets Blob peuvent être créés et lus, mais ils ne peuvent pas être modifiés ni supprimés. Chaque conservation juridique est associée à une balise alphanumérique définie par l’utilisateur, utilisée comme chaîne d’identificateur (comme pour l’ID d’un cas).

- **Prise en charge de tous les niveaux d’objets Blob :** les stratégies WORM sont indépendantes du niveau de stockage d’objets Blob Azure en vigueur et s’appliquent à tous les niveaux : chaud, froid et archive. Cela permet aux clients de stocker des données dans le niveau au coût le plus adapté à leurs charges de travail tant en conservant l’immuabilité des données.

- **Configuration du niveau du conteneur :** la fonctionnalité de stockage immuable permet aux utilisateurs de configurer des stratégies de rétention basée sur le temps et des balises de conservations juridiques au niveau du conteneur.  Ils peuvent créer et verrouiller des stratégies de rétention basée sur le temps, étendre des intervalles de rétention, définir et supprimer des conservations juridiques, etc, via des paramètres simples au niveau du conteneur.  Ces stratégies s’appliquent à tous les objets Blob du conteneur, qu’ils soient existants ou nouveaux.

- **Prise en charge du journal d'audit :** chaque conteneur contient un journal d'audit affichant jusqu’à cinq commandes de rétention basée sur le temps pour des stratégies de rétention basée sur le temps, avec un maximum de trois journaux pour des extensions d’intervalle de rétention.  Pour une rétention basée sur le temps, le journal contient un ID d’utilisateur, un type de commande, des timestamps et l’intervalle de rétention. Pour une conservation juridique, le journal contient un ID d’utilisateur, un type de commande, des timestamps et les balises de conservation juridique. Ce journal est conservé pendant la durée de vie du conteneur, selon la norme SEC 17a-4(f). Un journal plus complet de toutes les activités du panneau de configuration peut être consulté dans le [journal d'activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Il est de la responsabilité de l’utilisateur de stocker ces journaux de manière récurrente. Ils peuvent être nécessaires à des fins réglementaires ou autres.

 La fonctionnalité est activée dans toutes les régions Azure publiques.

## <a name="how-it-works"></a>Fonctionnement

Le stockage immuable pour des objets Blob prend en charge deux types de stratégies WORM ou immuables : la rétention basée sur le temps et la conservation juridique. Consultez la section [Prise en main](#Getting-started) pour plus de détails sur la création de ces stratégies immuables.
Lorsqu’une stratégie de rétention basée sur le temps ou une conservation juridique est appliquée sur un conteneur, tous les objets Blob existants seront déplacés à l’état immuable (non modifiable et non supprimable). Tous les nouveaux objets Blob chargés dans le conteneur seront aussi déplacés à cet état.

> [!IMPORTANT]
> Une stratégie de rétention basée sur le temps doit être *verrouillée* pour que l’objet Blob soit dans l’état immuable (non modifiable et non supprimable), selon la norme SEC 17a-4(f) et autres règlementations. Il est recommandé de verrouiller la stratégie pour une durée raisonnable, en général 24 heures. Nous recommandons de ne pas utiliser l’état *verrouillé* pour d’autres raisons que des essais de fonctionnalité à court terme.

 Lorsqu’une stratégie de rétention basée sur le temps est appliquée à un conteneur, tous les objets Blob de ce conteneur conserveront l’état immuable pour la durée *effective* de la rétention. La durée de rétention effective pour les objets Blob existants est égale à la différence entre le temps de création de l’objet Blob et l’intervalle de rétention spécifié par l’utilisateur. Pour les nouveaux objets Blob, la durée de rétention effective est égale à l’intervalle de rétention spécifié par l’utilisateur. Comme les utilisateurs peuvent modifier l’intervalle de rétention, la valeur la plus récente de l’intervalle de rétention spécifiée par l’utilisateur sera utilisée pour le calcul de la durée de rétention effective.

> [!TIP]
> Exemple : un utilisateur crée une stratégie de rétention basée sur le temps avec un intervalle de rétention de cinq ans.
> Il y a un objet blob existant, testblob1, dans ce conteneur, créé un an plus tôt. La période de rétention de l’objet testblob1 sera de quatre ans.
> Un nouvel objet blob, testblob2, est maintenant chargé dans le conteneur. La période de rétention de ce nouvel objet Blob sera de cinq ans.

### <a name="legal-holds"></a>Conservation juridique

En cas de conservation juridique, tous les objets Blob (existants et nouveaux) demeureront dans l’état immuable jusqu’à ce que la conservation soit levée.
Pour savoir comment définir et lever une conservation juridique, consultez la section [Prise en main](#Getting-started) pour plus de détails.

Un conteneur peut disposer d’une conservation juridique et d’une stratégie de rétention basée sur le temps. Tous les objets Blob de ce conteneur demeureront dans l’état immuable jusqu’à ce que toutes les conservations juridiques aient été levées, même si leur période de rétention a expiré. À l’inverse, un objet Blob demeure dans un état immuable jusqu’à expiration de la période de rétention effective, même si toutes les conservations juridiques ont été levées.
Le tableau suivant montre les types d’opérations d’objets Blob qui seront désactivées dans chaque scénario immuable.
Reportez-vous à la documentation [API du Service Blob Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) pour en savoir plus sur l’API REST d’objets Blob.

|Scénario  |État d'objets blob  |Opérations d’objets Blob non autorisées  |
|---------|---------|---------|
|L’intervalle de rétention effective sur l’objet Blob n’a pas encore expiré et/ou une conservation juridique est définie     |Immuable : non modifiable et non supprimable         |Delete Container, Delete Blob, Put Blob1, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|L’intervalle de rétention effective sur l’objet Blob a expiré     |Non modifiable seulement (suppressions autorisées)         |Put Blob, Put Block, Put Block List, Set Blob Metadata, Put Page, Set Blob Properties,  Snapshot Blob, Incremental Copy Blob, Append Block         |
|Conservations juridiques levées, et aucune stratégie de rétention basée sur le temps définie sur le conteneur     |Mutable         |Aucun         |
|Aucune stratégie WORM créée (rétention basée sur le temps ou conservation juridique)     |Mutable         |Aucun         |

> [!NOTE]
> Les premières opérations Put Blob, Put Block List et Put Block nécessaires pour créer un objet Blob sont autorisées dans les deux premiers scénarios du tableau ci-dessus. Toutes les autres opérations ne sont pas autorisées.
> La fonctionnalité de stockage immuable n’est disponible que dans des comptes GPv2 et de stockage d’objets Blob, et elle doit être créée via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Tarifs

Il n’y a pas de frais supplémentaire pour utiliser cette fonctionnalité, et le coût des données immuables est évalué de la même manière que pour les données normales et mutables. Reportez-vous à la [page des tarifications du Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) pour connaître les détails des coûts.

### <a name="restrictions"></a>Restrictions

Les exigences suivantes s’appliquent dans la préversion publique :

- **Ne pas stocker de données de production ou de données commerciales sensibles**
- Toutes exigences et accord de confidentialité de la préversion s’appliquent

## <a name="getting-started"></a>Prise en main

Le stockage Azure immuable pour les objets Blob Azure est pris en charge sur les versions les plus récentes du [portail Azure](http://portal.azure.com), d’Azure [CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et d’Azure [PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018).

### <a name="azure-portal"></a>Portail Azure

1. Créez un nouveau conteneur ou sélectionnez un conteneur existant pour stocker des objets Blob qui doivent demeurer dans l’état immuable.
 Le conteneur doit se trouver dans un compte de stockage GPv2.
2. Cliquez sur Stratégie d'accès dans les paramètres du conteneur, puis cliquez sur **+Ajouter stratégie** sous la stratégie de **Stockage d’objets Blob immuable**, comme montré ci-dessous.

    ![Portail](media/storage-blob-immutable-storage/portal-image-1.png)

3. Pour activer la rétention basée sur le temps, choisissez Rétention basée sur le temps dans le menu déroulant.

    ![Rétention](media/storage-blob-immutable-storage/portal-image-2.png)

4. Saisissez l’intervalle de rétention désiré en jour (un jour minimum)

    ![Intervalle de rétention](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Comme vous le voyez ci-dessus, l’état initial de la stratégie est déverrouillé. Cela vous permet de tester la fonctionnalité avec un intervalle de rétention plus petit, et d’effectuer les modifications à la stratégie avant de la verrouiller. Le verrouillage est essentiel selon la norme SEC 17a-4.

5. Verrouillez la stratégie en cliquant avec le bouton droit sur les ..., et le menu suivant s’affiche :

    ![Verrouiller une stratégie](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Cliquez sur Verrouiller une stratégie et l’état de la stratégie affichera Verrouillée. Une fois verrouillée, la stratégie ne peut plus être supprimée et seules les extensions de l’intervalle de rétention seront autorisées.

6. Pour activer les conservations juridiques, cliquez sur +Ajouter une stratégie et choisissez Conservation juridique dans le menu déroulant.

    ![Conservation juridique](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Créer une conservation juridique avec une ou plusieurs balises

    ![Définir les balises d’une conservation juridique](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="cli-20"></a>CLI 2.0

Installer l’[extension CLI](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) avec `az extension add -n storage-preview`

Si vous avez déjà installé l’extension, exécutez la commande suivante pour activer la fonctionnalité de stockage immuable : `az extension update -n storage-preview`

La fonctionnalité est incluse dans les groupes de commandes suivants (exécutez « h » sur ces groupes pour voir les commandes) : `az storage container immutability-policy` et `az storage container legal-hold`.

### <a name="powershell"></a>PowerShell

La fonctionnalité de stockage immuable est prise en charge sur la [version 4.4.0 de PowerShell (en préversion)](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180).
Pour activer la fonctionnalité, procédez comme suit :

1. Vérifiez que la dernière version de PowerShellGet est installée à l’aide de `Install-Module PowerShellGet –Repository PSGallery –Force`
2. Supprimez les installations précédentes d’Azure PowerShell
3. Installez AzureRM (Azure peut être installé de façon similaire sur ce référentiel) `Install-Module AzureRM –Repository PSGallery –AllowClobber`
4. Installez la préversion des cmdlets de la gestion de stockage (préversion)`Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

Un exemple de code PowerShell illustrant l’utilisation de la fonctionnalité est fourni ci-dessous.

## <a name="client-libraries"></a>Bibliothèques clientes

Le stockage immuable pour les objets Blob Azure est pris en charge dans les versions de bibliothèques de client suivantes

- [Bibliothèque de client .net (version 7.2.0-preview et versions ultérieures](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Bibliothèque de client node.js (version 4.0.0 et ultérieures)](https://www.npmjs.com/package/azure-arm-storage)
- [Bibliothèque de client Python (version 2.0.0 Release Candidate 2 et versions ultérieures)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Valeurs prises en charge

- L’intervalle de rétention minimum est d’un jour, et le maximum est de 400 ans
- Pour un compte de stockage donné, le nombre de conteneurs maximum par compte de stockage avec stratégies immuables verrouillées est de 1000
- Pour un compte de stockage donné, le nombre de conteneurs maximum par compte de stockage avec le paramètre de conservation juridique est de 1000
- Pour un conteneur donné, le nombre de balises de conservation juridique maximum est de 10
- La longueur maximale d’une balise de conservation juridique est de 23 caractères alphanumériques, et la longueur minimale est de trois caractères.
- Pour un conteneur donné, le nombre maximum d’extensions d’intervalle de rétention autorisées pour des stratégies immuables verrouillées est de trois
- Pour un conteneur donné avec une stratégie immuable verrouillée, il y a un maximum de cinq journaux de stratégie de rétention basée sur le temps et de 10 journaux de conservation juridique conservés pour la durée du conteneur.

## <a name="faq"></a>Forum Aux Questions

**Cette fonctionnalité s’applique-t-elle uniquement aux objets blob de blocs, ou aussi aux objets blob de page et d’ajout ?**

La fonctionnalité de stockage immuable pour les objets blob peut être utilisée avec n’importe quel type d’objet blob.  Toutefois, il est recommandé d’utiliser cette fonctionnalité avec des objets blob de blocs principalement. Contrairement aux objets blob de blocs, les objets blob de page et d’ajout doivent être créés hors d’un conteneur WORM, avant d’y être copiés.  Une fois copiés dans un conteneur WORM, il n’est plus possible de faire des *ajouts* à un objet blob d’ajout ni de modifier un objet blob de pages.

**Dois-je toujours créer un compte de stockage pour utiliser cette fonctionnalité ?**

Vous pouvez utiliser la fonctionnalité de stockage immuable avec tout compte GPv2 existant ou tout nouveau compte de stockage si le type est de compte est GPv2. Cette fonctionnalité est uniquement disponible avec le stockage d’objets blob.

**Que se passe-t-il si j’essaie de supprimer un conteneur avec une stratégie de rétention basée sur le temps *verrouillée* ou une conservation juridique ?**

L’opération Delete Container échoue s’il s’agit d’un objet blob disposant d’une stratégie de rétention basée sur le temps verrouillée ou d’une conservation juridique. L’opération Delete Container réussit si aucun objet blob ne dispose d’un intervalle de rétention actif ou d’une conservation juridique. Vous devez d’abord supprimer les objets blob avant de pouvoir supprimer le conteneur.

**Que se passe-t-il si j’essaie de supprimer un compte de stockage avec un conteneur WORM disposant d’une stratégie de rétention basée sur le temps *verrouillée* ou d’une conservation juridique ?**

La suppression du compte de stockage échoue s’il s’agit d’un conteneur WORM disposant d’une stratégie de rétention basée sur le temps verrouillée ou d’une conservation juridique.  Tous les conteneurs WORM doivent être supprimés avant de pouvoir supprimer le compte de stockage.  Voir question #2 pour en savoir plus sur la suppression de conteneur.

**Puis-je déplacer les données sur différents niveaux d’objets blob (chaud, froid, archive) lorsque l’objet blob est dans l’état immuable ?**

Oui, vous pouvez utiliser la commande Set Blob Tier pour déplacer des données sur d’autres niveaux d’objets blob tant en conservant leur état immuable. La fonctionnalité de stockage immuable est prise en charge sur les niveaux d’objets blob chaud, froid et archive.

**Que se passe-t-il si je ne paie pas et que mon intervalle de rétention n’a pas expiré ?**

En cas d’absence de paiement, les stratégies de rétention des données normales s’appliquent, comme mentionné dans les conditions générales de votre contrat avec Microsoft.

**Proposez-vous une période d’essai ou de grâce pour essayer la fonctionnalité ?**

Oui, lorsqu’une stratégie de rétention basée sur le temps est créée, elle sera dans l’état *déverrouillée*. Dans cet état, vous pouvez effectuer les changements que vous voulez à l’intervalle de rétention, comme augmenter ou diminuer la stratégie, et même la supprimer. Une fois la stratégie verrouillée, elle le reste pour toujours afin d’en empêcher la suppression. De plus, l’intervalle de rétention ne peut plus être diminué si la stratégie est verrouillée. Nous vous recommandons fortement d’utiliser l’état *déverrouillé* uniquement à des fins d’essai et de verrouiller la stratégie pour une période de 24 heures, afin d’assurer toute conformité à la norme SEC 17a-4(f) et autres normes.

**Cette fonctionnalité est-elle disponible dans les clouds nationaux et gouvernementaux ?**

La fonctionnalité de stockage immuable n’est actuellement disponible que dans les régions publiques Azure. Contactez azurestoragefeedback@microsoft.com pour toute demande relative à un cloud national spécifique.

## <a name="sample-code"></a>Exemple de code

Un exemple de script PowerShell est donné ci-dessous pour référence.
Ce script crée un compte de stockage et un conteneur et vous montre comment définir et lever des conservations juridiques, créer et verrouiller une stratégie de rétention basée sur le temps (ou stratégie d’immuabilité), étendre l’intervalle de rétention, etc.

```powershell
\$ResourceGroup = "\<Enter your resource group\>”

\$StorageAccount = "\<Enter your storage account name\>"

\$container = "\<Enter your container name\>"

\$container2 = "\<Enter another container name\>”

\$location = "\<Enter the storage account location\>"

\# Login to the Azure Resource Manager Account

Login-AzureRMAccount

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

\# Create your Azure Resource Group

New-AzureRmResourceGroup -Name \$ResourceGroup -Location \$location

\# Create your Azure storage account

New-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup -StorageAccountName
\$StorageAccount -SkuName Standard_LRS -Location \$location -Kind Storage

\# Create a new container

New-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Create Container 2 with Storage Account object

\$accountObject = Get-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

New-AzureRmStorageContainer -StorageAccount \$accountObject -Name \$container2

\# Get container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Get Container with Account object

\$containerObject = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container

\#list container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

\#remove container (Add -Force to dismiss prompt)

Remove-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container2

\#with Account object

Remove-AzureRmStorageContainer -StorageAccount \$accountObject -Name
\$container2

\#with Container object

\$containerObject2 = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container2

Remove-AzureRmStorageContainer -InputObject \$containerObject2

\#Set LegalHold

Add-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag1,tag2

\#with Account object

Add-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3

\#with Container object

Add-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4,tag5

\#Clear LegalHold

Remove-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag2

\#with Account object

Remove-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3,tag5

\#with Container object

Remove-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4

\# create/update ImmutabilityPolicy

\#\# with account/container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 10

\#with Account object

Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -ImmutabilityPeriod 1 -Etag \$policy.Etag

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 7

\#\# with ImmutabilityPolicy object

Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy
-ImmutabilityPeriod 5

\#get ImmutabilityPolicy

Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container

\#with Account object

Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container

\#with Container object

Get-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject

\#Lock ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -force

\#\# with account/container name

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -Etag \$policy.Etag

\#with Container object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -Etag \$policy.Etag -force

\#Extend ImmutabilityPolicy

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -ImmutabilityPeriod 11 -ExtendPolicy

\#\# with account/container name

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 11 -Etag \$policy.Etag -ExtendPolicy

\#with Account object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -ImmutabilityPeriod 12 -Etag
\$policy.Etag -ExtendPolicy

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 13 -Etag \$policy.Etag -ExtendPolicy

\#Remove ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy

\#\# with account/container name

Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -Etag \$policy.Etag

\#with Container object

Remove-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject
-Etag \$policy.Etag
```