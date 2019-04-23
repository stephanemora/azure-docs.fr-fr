---
title: Stockage immuable pour les objets blob du Stockage Azure | Microsoft Docs
description: Le Stockage Azure assure la prise en charge des disques optiques non réinscriptibles (WORM) pour le Stockage (d’objets) Blob, qui permettent aux utilisateurs de stocker des données dans un état immuable et non effaçable sur une période donnée.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 04/18/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 7fd9992db79b2517256d85ca3fd8f3bf409afa48
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996020"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Stocker des données vitales pour l’entreprise dans le stockage Blob Azure

Stockage immuable pour stockage Blob Azure permet aux utilisateurs de stocker des objets de données critiques dans un état WORM (Write Once, Read Many). Cet état les rend non effaçables et immuables pour une durée spécifiée par l’utilisateur. Objets BLOB peuvent être créés et lues, mais pas modifiées ou supprimés pendant la durée de l’intervalle de rétention. Stockage immuable est activé pour un usage général v2 et comptes de stockage d’objets Blob dans toutes les régions Azure.

## <a name="overview"></a>Présentation

Stockage immuable permet l’établissement de santé, les institutions financières et des industries connexes--particulièrement courtier les organisations--pour stocker les données en toute sécurité. Elle peut également être exploitée dans n’importe quel scénario pour protéger les données stratégiques contre toute modification ou suppression. 

Les applications typiques incluent :

- **Conformité réglementaire** : Le stockage immuable pour le Stockage Blob Azure permet aux organisations de respecter entre autres les réglementations SEC 17a-4(f), CFTC 1.31(d) et FINRA. Un livre blanc technique par Cohasset Associates décrit en détail comment immuable adresses de stockage de ces réglementations peut être téléchargé via le [portail Microsoft Service Trust](https://aka.ms/AzureWormStorage). Le [centre de confidentialité Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contient des informations détaillées sur nos certifications de conformité.

- **Conservation sécurisée des documents** : Stockage immuable pour stockage Blob Azure permet de s’assurer que les données ne peut pas être modifiées ou supprimées par n’importe quel utilisateur, y compris les utilisateurs avec des privilèges d’administrateur de compte.

- **Conservation légale** : Stockage immuable pour stockage Blob Azure permet aux utilisateurs de stocker des informations sensibles qui doivent impérativement à utiliser en cas de litige ou d’entreprise dans un état infalsifiable pour la durée souhaitée jusqu'à ce que le. Cette fonctionnalité n’est pas limitée aux cas d’usage légal, mais peut également être imaginée comme une attente basée sur un événement ou un verrou de l’entreprise, où la nécessité de protéger les données en fonction de déclencheurs d’événements ou de la stratégie d’entreprise est requise.

Stockage immuable prend en charge les éléments suivants :

- **[Prise en charge de stratégie de rétention temporelle](#time-based-retention)**: Les utilisateurs peuvent définir des stratégies pour stocker les données pendant un intervalle spécifié. Quand une stratégie de rétention temporelle est définie, objets BLOB peut être créé et lire, mais pas modifié ou supprimé. Une fois la période de rétention a expiré, les objets BLOB peut être supprimé, mais ne pas remplacé.

- **[Prise en charge de la stratégie de conservation légale](#legal-holds)**: Si l’intervalle de rétention n’est pas connu, les utilisateurs peuvent définir légale pour stocker les données immuable jusqu'à ce que la conservation légale est désactivée.  Quand une stratégie de conservation légale est définie, objets BLOB peut être créé et lire, mais pas modifié ou supprimé. Chaque légale est associé à une défini par l’utilisateur alphanumériques balise (par exemple, un ID de cas, nom de l’événement, etc.) qui est utilisée comme une chaîne d’identificateur. 

- **Prise en charge de tous les niveaux d’objets blob** : Les stratégies WORM sont indépendantes du niveau de Stockage Blob Azure et s’appliquent à tous les niveaux (chaud, froid et archive). Les clients peuvent transférer les données au niveau ayant le coût le plus adapté à leurs charges de travail tout en conservant l’immuabilité des données.

- **Configuration au niveau du conteneur** : Les utilisateurs peuvent configurer des stratégies de conservation limitée dans le temps et des balises de conservation légale au niveau du conteneur. À l’aide des paramètres au niveau du conteneur simples, les utilisateurs peuvent créer et verrouiller des stratégies de rétention temporelle, étendre les intervalles de rétention, set et légale pour effacer et bien plus encore. Ces stratégies s’appliquent à tous les objets blob du conteneur, anciens ou nouveaux.

- **Prise en charge des enregistrements d’audit** : Chaque conteneur comprend un journal d’audit de stratégie. Il indique à sept temporels rétention commandes pour les stratégies de rétention temporelle de verrouillé et contient l’ID utilisateur, type de commande, date et heure indiquées et intervalle de rétention. En ce qui concerne les stratégies d’archivage juridique, il comporte l’identifiant utilisateur, le type de commande, les timestamps et les balises d’archivage juridique. Ce journal est conservé pour la durée de vie de la stratégie, conformément aux directives réglementaires 17a-4(f) s. Le [journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md) montre un journal plus complète de toutes les activités de plan de contrôle ; lors de l’activation [journaux de Diagnostic Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) conserve et affiche les opérations de plan de données. Il est de la responsabilité de l’utilisateur de stocker ces journaux d’activité de manière persistante, car ceux-ci peuvent être nécessaires à des fins réglementaires ou autres.

## <a name="how-it-works"></a>Fonctionnement

Le stockage immuable du Stockage Blob Azure prend en charge deux types de stratégies WORM ou immuables : la conservation limitée dans le temps et l’archivage juridique. Lorsqu’une stratégie de rétention temporelle ou la conservation légale est appliquée sur un conteneur, tous les objets BLOB existants déplacement dans un état immuable de ver en moins de 30 secondes. Tous les nouveaux objets BLOB qui sont chargés dans ce conteneur seront également déplacés dans l’état immuable. Une fois que tous les objets BLOB ont déplacé dans l’état immuable, la stratégie immuable est confirmée et toutes les remplacent ou supprimer les opérations pour les objets nouveaux et existants dans le conteneur immuable ne sont pas autorisées.

Conteneur et suppression du compte ne sont également pas autorisées s’il existe des objets BLOB protégés par une stratégie immuable. L’opération Supprimer le conteneur échoue s’il existe un ou plusieurs objets blob ayant une stratégie de conservation limitée dans le temps verrouillée ou un archivage juridique. La suppression du compte de stockage échoue s’il s’agit d’un conteneur WORM disposant d’une stratégie de rétention basée sur le temps verrouillée ou d’une conservation juridique. 

### <a name="time-based-retention"></a>Rétention temporelle

> [!IMPORTANT]
> Une stratégie de conservation limitée dans le temps doit être *verrouillée* pour que l’objet blob ait un état immuable (protégé contre l’écriture et la suppression) conformément entre autres à la réglementation SEC 17a-4(f). Il est recommandé de verrouiller la stratégie au bout d’un délai raisonnable, en général dans les 24 heures. Nous conseillons de ne pas utiliser l’état *déverrouillé* pour d’autres raisons que des évaluations de fonctionnalités à court terme.

Lorsqu’une stratégie de conservation limitée dans le temps est appliquée à un conteneur, tous les objets blob de ce conteneur conserveront l’état immuable pendant la période de conservation *effective*. La période de rétention efficace pour les objets BLOB existants est égale à la différence entre l’heure de modification d’objets blob et l’intervalle de rétention spécifiée par l’utilisateur.

Pour les nouveaux objets Blob, la durée de rétention effective est égale à l’intervalle de rétention spécifié par l’utilisateur. Dans la mesure où les utilisateurs peuvent étendre la période de conservation, le stockage immuable utilise la valeur la plus récente de la période de conservation spécifiée par l’utilisateur pour calculer la durée de conservation effective.

> [!TIP]
> **Exemple :** Un utilisateur crée une stratégie de conservation limitée dans le temps avec une période de conservation de cinq ans.
>
> L’objet blob existant dans ce conteneur, _testblob1_, a été créé il y a un an. La période de rétention efficace pour _testblob1_ est de quatre ans.
>
> Un nouvel objet blob, _testblob2_, est maintenant téléchargé vers le conteneur. La période de conservation effective de ce nouvel objet blob est de cinq ans.

### <a name="legal-holds"></a>Conservation juridique

En cas de définition d’une stratégie d’archivage juridique, tous les objets blob (anciens et nouveaux) conservent l’état immuable jusqu’à ce qu’elle soit levée. Pour savoir comment définir et lever une stratégie de conservation à des fins juridiques, voir la section [Bien démarrer](#getting-started).

Un conteneur peut avoir à la fois une stratégie d’archivage juridique et une stratégie de conservation limitée dans le temps. Tous les objets blob de ce conteneur conservent l’état immuable jusqu’à ce que toutes les stratégies d’archivage juridique aient été levées, même si leur période de conservation effective est écoulée. À l’inverse, un objet blob demeure dans un état immuable jusqu’à expiration de la période de conservation effective, même si toutes les stratégies d’archivage juridique ont été levées.

Le tableau suivant montre les types d’opérations blob désactivées dans chaque scénario immuable. Pour plus d’informations, voir la documentation [API du service BLOB Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Scénario  |État des objets blob  |Opérations blob non autorisées  |
|---------|---------|---------|
|L’intervalle de rétention effective sur l’objet Blob n’a pas encore expiré et/ou une conservation juridique est définie     |Immuable : non modifiable et non supprimable         | Placer l’objet Blob<sup>1</sup>, placez le bloc<sup>1</sup>, Put Block List<sup>1</sup>, supprimer les métadonnées d’objets Blob du conteneur, supprimer l’objet Blob, ensemble, Put Page, définissez les propriétés d’objet Blob, Snapshot Blob, copie incrémentielle d’objets Blob, Ajoutez le bloc         |
|L’intervalle de rétention effective sur l’objet Blob a expiré     |Non modifiable seulement (suppressions autorisées)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Set Blob Properties, Snapshot Blob, Incremental Copy Blob, Append Block         |
|Stratégies d’archivage juridique levées ; aucune stratégie de conservation limitée dans le temps dans le conteneur     |Mutable         |Aucun         |
|Aucune stratégie WORM créée (conservation limitée dans le temps ou archivage juridique)     |Mutable         |Aucun         |

<sup>1</sup> l’application permet de créer un nouvel objet blob une fois ces opérations. Toutes les remplacer les opérations sur un chemin d’objet blob existant dans un conteneur immuable ne sont pas autorisées.

## <a name="supported-values"></a>Valeurs prises en charge

### <a name="time-based-retention"></a>Rétention temporelle
- Pour un compte de stockage, le nombre maximal de conteneurs avec verrouillé immuables stratégies basées sur le temps est 1 000.
- L’intervalle de rétention minimale est 1 jour. La valeur maximale est 146,000 jours (400 ans).
- Pour un conteneur, le nombre maximal de modifications pour étendre un intervalle de rétention pour verrouillé immuables stratégies basées sur le temps est 5.
- Pour un conteneur, un maximum de journaux d’audit de stratégie de rétention temporelle 7 sont conservés pendant la durée de la stratégie.

### <a name="legal-hold"></a>Légale
- Le nombre maximal par compte de stockage de conteneurs avec paramètre d’archivage juridique est de 1 000.
- Le nombre maximal par conteneur de balises d’archivage juridique est de 10.
- La longueur d’une balise légale minimale est de 3 caractères alphanumériques. La longueur maximale est de 23 caractères alphanumériques.
- Pour un conteneur, un maximum de 10 juridique contenir les journaux sont conservés pendant la durée de la stratégie d’audit des stratégies.

## <a name="pricing"></a>Tarifs

L’utilisation de cette fonctionnalité n’entraîne aucun coût supplémentaire. Les données immuables sont facturées au même tarif que les données modifiables classiques. Pour plus d’informations sur les prix du stockage Blob Azure, consultez la [page des tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Prise en main
Stockage immuable est disponible uniquement pour les comptes de stockage d’objets Blob et usage général v2. Ces comptes doivent être gérés via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Pour plus d’informations sur la mise à niveau d’un compte de stockage v1 à usage général, consultez [mise à niveau d’un compte de stockage](../common/storage-account-upgrade.md).

Les versions les plus récentes de la [Azure portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), et [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) prennent en charge le stockage immuable pour stockage Blob Azure. [Prise en charge de la bibliothèque client](#client-libraries) est également fourni.

### <a name="azure-portal"></a>Portail Azure

1. Créez un nouveau conteneur ou sélectionnez un conteneur existant pour stocker des objets Blob qui doivent demeurer dans l’état immuable.
 Le conteneur doit se trouver dans un compte de stockage Blob ou un compte de stockage universel V2.
2. Sélectionnez **Stratégie d’accès** dans les paramètres du conteneur. Ensuite, sélectionnez **+ Ajouter une stratégie** sous **Stockage Blob immuable**.

    ![Paramètres du conteneur sur le portail](media/storage-blob-immutable-storage/portal-image-1.png)

3. Pour activer la conservation limitée dans le temps, sélectionnez **Conservation limitée dans le temps** dans le menu déroulant.

    ![Sélection de « Conservation limitée dans le temps » sous « Type de stratégie »](media/storage-blob-immutable-storage/portal-image-2.png)

4. Entrez l’intervalle de rétention en jours (valeurs acceptables de 1 à 146000 jours).

    ![Zone « Mettre à jour la période de conservation »](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    L’état initial de la stratégie est déverrouillé, ce qui vous permet de tester la fonctionnalité et d’apporter des modifications à la stratégie avant que vous la verrouiller. La stratégie de verrouillage est essentielle pour la conformité aux réglementations telles que SEC 17 a-4.

5. Verrouillez la stratégie. Cliquez sur le bouton de sélection (**...** ), et le menu suivant s’affiche avec des actions supplémentaires :

    ![« Verrouiller la stratégie » dans le menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Sélectionnez **stratégie de verrouillage** et confirmez le verrou. La stratégie est désormais verrouillée et ne peut pas être supprimée, seules les extensions de l’intervalle de rétention seront autorisées. Supprime des objets BLOB et les substitutions ne sont pas autorisées. 

    ![Confirmer la « Stratégie de verrouillage » dans le menu](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Pour activer l’archivage juridique, sélectionnez **+ Ajouter une stratégie**. Sélectionnez **Archivage juridique** dans le menu déroulant.

    ![« Archivage juridique » dans le menu sous « Type de stratégie »](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Créez une stratégie d’archivage juridique avec une ou plusieurs balises.

    ![Zone « Nom de balise » sous le type de stratégie](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Pour effacer un légale, supprimez simplement la balise d’identificateur appliqué légale.

### <a name="azure-cli"></a>Azure CLI

La fonctionnalité est incluse dans les groupes de commandes suivants : `az storage container immutability-policy` et `az storage container legal-hold`. Exécutez `-h` sur ces groupes pour afficher les commandes.

### <a name="powershell"></a>PowerShell

Le module de la préversion d’Az.Storage prend en charge le stockage immuable.  Pour activer cette fonctionnalité, suivez les étapes ci-dessous :

1. Vérifiez que la dernière version de PowerShellGet est installée : `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Supprimez les installations précédentes d’Azure PowerShell.
3. Installez Azure PowerShell : `Install-Module Az –Repository PSGallery –AllowClobber`.
4. Installez la version préliminaire du module Azure PowerShell Storage : `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

La section [Exemple de code PowerShell](#sample-powershell-code) plus loin dans cet article illustre l’utilisation de cette fonctionnalité.

## <a name="client-libraries"></a>Bibliothèques clientes

Les bibliothèques de client suivantes prennent en charge le stockage immuable pour le Stockage Blob Azure :

- [Bibliothèque de client .NET version 7.2.0-preview et versions ultérieures](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Bibliothèque de client Node.js version 4.0.0 et versions ultérieures](https://www.npmjs.com/package/azure-arm-storage)
- [Bibliothèque de client Python version 2.0.0 Release Candidate 2 et versions ultérieures](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Bibliothèque cliente Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>Forum Aux Questions

**Pourrez vous fournir une documentation de la conformité du ver ?**

Oui. Conformité du document, Microsoft conservées un cabinet d’évaluation indépendante spécialisée dans les enregistrements gestion et les informations de gouvernance, Cohasset Associates, à évaluer sa conformité aux exigences spécifiques et stockage d’objets Blob Azure immuable pour le secteur des services financiers. Cohasset validé qu’immuable stockage Blob Azure, lorsqu’il est utilisé pour conserver temporels les objets BLOB dans un état ver, remplit les conditions de stockage qui vous intéressent CFTC règle 1.31(c)-(d) FINRA règle 4511 et règle SEC 17 a-4. Microsoft ciblé cet ensemble de règles, car ils représentent les recommandations plus dans le monde entier pour la rétention des enregistrements pour les institutions financières. Le rapport Cohasset est disponible dans le [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage).

**Cette fonctionnalité s’applique-t-elle uniquement aux objets blob de blocs, ou aussi aux objets blob de page et d’ajout ?**

Le stockage immuable peut être utilisé avec n’importe quel type d’objet blob, mais nous recommandons de l’utiliser principalement pour les objets blob de blocs. Contrairement aux objets blob de blocs, les objets blob de page et d’ajout doivent être créés hors d’un conteneur WORM, avant d’y être copiés. Après avoir copié ces objets BLOB dans un conteneur ver, ne plus *ajoute* à un ajout blob ou les modifications apportées à un objet blob de pages sont autorisées.

**Je dois créer un compte de stockage pour utiliser cette fonctionnalité ?**

Non, vous pouvez utiliser stockage immuable avec n’importe quel existant ou nouvellement créé usage général v2 ou les comptes de stockage d’objets Blob. Cette fonctionnalité est destinée à une utilisation avec les objets BLOB de blocs dans les comptes GPv2 et de stockage d’objets Blob. Comptes stockage v1 à usage générales ne sont pas pris en charge, mais peuvent être facilement mis à niveau à usage général v2. Pour plus d’informations sur la mise à niveau d’un compte de stockage v1 à usage général, consultez [mise à niveau d’un compte de stockage](../common/storage-account-upgrade.md).

**Puis-je appliquer une conservation légale et la stratégie de rétention temporelle ?**

Oui, un conteneur peut avoir un légale et une stratégie de rétention temporelle en même temps. Tous les objets blob de ce conteneur conservent l’état immuable jusqu’à ce que toutes les stratégies d’archivage juridique aient été levées, même si leur période de conservation effective est écoulée. À l’inverse, un objet blob demeure dans un état immuable jusqu’à expiration de la période de conservation effective, même si toutes les stratégies d’archivage juridique ont été levées.

**Sont des stratégies de conservation légale uniquement pour des poursuites judiciaires ou existe-t-il d’autres scénarios d’utilisation ?**

Non, le gel est simplement le terme général utilisé pour une stratégie de rétention temporelle non. Il est inutile être utilisé uniquement pour les litiges liés d’une procédure. Les stratégies de blocage légales sont utiles pour la désactivation de remplacement et suppressions pour protéger l’entreprise importante données ver, où la période de rétention est inconnue. Vous pouvez l’utiliser en tant qu’une stratégie d’entreprise à protéger votre ver charges de travail critiques ou utilisez-la comme une stratégie intermédiaire avant un déclencheur d’événement personnalisé nécessite l’utilisation d’une stratégie de rétention temporelle. 

**Que se passe-t-il si j’essaie de supprimer un conteneur avec une stratégie de rétention basée sur le temps *verrouillée* ou une conservation juridique ?**

L’opération Supprimer le conteneur échoue s’il existe un ou plusieurs objets blob ayant une stratégie de conservation limitée dans le temps verrouillée ou un archivage juridique. L’opération Supprimer le conteneur réussit uniquement s’il n’y a aucun objet blob avec une période de conservation ou avec un archivage juridique. Il est nécessaire de supprimer les objets blob pour pouvoir supprimer le conteneur.

**Que se passe-t-il si j’essaie de supprimer un compte de stockage avec un conteneur WORM disposant d’une stratégie de rétention basée sur le temps *verrouillée* ou d’une conservation juridique ?**

La suppression du compte de stockage échoue s’il s’agit d’un conteneur WORM disposant d’une stratégie de rétention basée sur le temps verrouillée ou d’une conservation juridique. Il est nécessaire de supprimer tous les conteneurs WORM pour pouvoir supprimer le compte de stockage. Pour plus d’informations sur la suppression des conteneurs, voir la question précédente.

**Puis-je déplacer les données sur différents niveaux d’objets blob (chaud, froid, archive) lorsque l’objet blob est dans l’état immuable ?**

Oui, vous pouvez utiliser la commande de définir le niveau objet Blob pour déplacer des données tout en conservant les données dans un état immuable conforme au sein des niveaux d’objets blob. Le stockage immuable est pris en charge sur les niveaux d’objets blob chaud, froid et archive.

**Que se passe-t-il si je ne paie pas et que mon intervalle de rétention n’a pas expiré ?**

En cas de défaut de paiement, les stratégies de conservation des données normales s’appliquent, comme le stipulent les conditions générales de votre contrat avec Microsoft.

**Proposez-vous une période d’essai ou de grâce pour essayer la fonctionnalité ?**

Oui. Création d’une stratégie de rétention temporelle est tout d’abord, il est dans un *déverrouillé* état. Dans cet état, vous pouvez apporter les modifications souhaitées à la période de conservation, par exemple, l’augmenter, la diminuer ou même supprimer la stratégie. Une fois que la stratégie est verrouillée, elle reste verrouillée jusqu’à l’expiration de l’intervalle de rétention. Cette stratégie verrouillée empêche la suppression et modification de l’intervalle de rétention. Il est vivement recommandé de n’utiliser l’état *déverrouillé* qu’à des fins d’évaluation et de verrouiller la stratégie dans les 24 heures. Ces pratiques permettent de respecter entre autres la réglementation SEC 17a-4(f).

**Puis-je utiliser la suppression réversible en même temps que les stratégies de blob non modifiable ?**

Oui. [Suppression réversible pour le stockage Blob Azure](storage-blob-soft-delete.md) s’applique à tous les conteneurs au sein d’un compte de stockage, quel que soit un légale ou d’une stratégie de rétention temporelle. Nous vous recommandons d’activer la suppression réversible pour une protection supplémentaire avant que toutes les stratégies ver immuables soient appliqués et confirmés. 

**Où se trouve la fonctionnalité disponible ?**

Le stockage immuable est disponible dans les régions Azure publiques, Chine et Administration. Si immuable stockage n’est pas disponible dans votre région, contactez le support et messagerie azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Exemple de code PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L’exemple de script PowerShell suivant est fourni à titre de référence. Il crée un compte de stockage et un conteneur. Ensuite, il montre comment définir et lever des stratégies d’archivage juridique, créer et verrouiller une stratégie de conservation limitée dans le temps (ou stratégie d’immuabilité) et étendre la période de conservation.

Créer et tester le compte de stockage Azure :

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Définir et supprimer des archivages juridiques :

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Créer ou mettre à jour des stratégies d’immuabilité :
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Récupérer des stratégies d’immuabilité :
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

Verrouiller des stratégies d’immuabilité (ajouter - Force pour ignorer l’invite) :
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Étendre des stratégies d’immuabilité :
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Supprimer une stratégie d’immuabilité (ajouter - Force pour ignorer l’invite) :
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
