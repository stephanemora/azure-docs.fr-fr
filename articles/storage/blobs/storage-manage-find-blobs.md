---
title: Gérer et rechercher des données dans le stockage d’objets blob Azure avec un index d’objet blob (préversion)
description: Découvrez comment utiliser les étiquettes d’index d’objet blob afin de classer, gérer et interroger pour découvrir des objets blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/01/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: ed70a05e0a6213ce00a6e0514f0741e8abbaeef9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690372"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Gérer et rechercher des données dans le stockage d’objets blob Azure avec un index d’objet blob (préversion)

Plus les jeux de données croissent, plus la recherche d’un objet spécifique dans une mer de données peut s’avérer difficile et frustrante. Un index d’objet blob fournit des fonctionnalités de gestion et de découverte des données à l’aide des attributs des étiquettes d’index clé-valeur. Ceux-ci vous permettent de classer et de rechercher des objets dans un conteneur individuel ou dans tous les conteneurs de votre compte de stockage. Plus tard, lorsque les exigences relatives aux données changent, il est possible de reclasser les objets de manière dynamique en mettant à jour leurs étiquettes d’index tout en les maintenant en place dans l’organisation actuelle des conteneurs. L’utilisation d’un index d’objet blob peut simplifier le développement en consolidant vos données blob et les attributs d’index associés sur le même service. Cela vous permet de générer des applications efficaces et évolutives à l’aide de fonctionnalités natives. 

Un index d’objet blob vous permet de :

- Classer dynamiquement vos objets blob à l’aide d’étiquettes d’index clé-valeur pour la gestion des données
- Rechercher rapidement des objets blob spécifiques étiquetés dans un seul conteneur ou dans un compte de stockage entier
- Spécifier des comportements conditionnels pour les API d’objets blob en fonction de l’évaluation des étiquettes d’index
- Utiliser des étiquettes d’index pour les contrôles avancés sur les fonctionnalités de plateforme blob, telles que la [gestion du cycle de vie](storage-lifecycle-management-concepts.md)

Envisagez un scénario dans lequel vous pouvez écrire des millions d’objets blob dans votre compte de stockage et y accéder à l’aide de nombreuses applications différentes. Vous souhaitez rechercher toutes les données associées à partir d’un projet unique, mais vous ne savez pas ce qui figure dans l’étendue, car les données peuvent être réparties sur plusieurs conteneurs avec différentes conventions de nommage d’objets blob. Toutefois, vous savez que vos applications chargent toutes les données avec des étiquettes en fonction de leur projet respectif et de la description d’identification. Au lieu de rechercher parmi des millions d’objets blob et de comparer les noms et les propriétés, vous pouvez simplement utiliser `Project = Contoso` comme critère de découverte. Un index d’objet blob filtrera tous les conteneurs sur l’ensemble de votre compte de stockage pour rechercher et renvoyer rapidement un simple ensemble de cinquante objets blob à partir de `Project = Contoso`. 

Pour bien démarrer, consultez des exemples d’utilisation d’un index d’objet blob dans [Utiliser un index d’objet blob pour gérer et rechercher des données](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Étiquettes d’index d’objet blob et gestion des données

Les préfixes de nom d’objet blob et de conteneur constituent une catégorisation unidimensionnelle pour vos données stockées. Un index d’objet blob permet désormais une catégorisation multidimensionnelle de tous vos [types de données blob (de blocs, d’ajout ou de pages)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) avec des étiquettes d’attribut appliquées. Cette catégorisation multidimensionnelle est indexée en mode natif et mise à votre disposition pour vous permettre d’interroger et de rechercher rapidement vos données.

Considérez les cinq objets blob suivants dans votre compte de stockage :
>
> container1/transaction.csv  
> container2/campaign.docx  
> photos/bannerphoto.png  
> archives/completed/2019review.pdf  
> logs/2020/01/01/logfile.txt  
>

Ces objets blob sont actuellement séparés à l’aide d’un préfixe de conteneur/dossier virtuel/nom d’objet blob. Un index d’objet blob vous permet de définir un attribut d’étiquette d’index `Project = Contoso` sur ces cinq objets blob pour les classer ensemble tout en maintenant leur organisation de préfixe actuelle. Cela évite d’avoir à déplacer les données en garantissant le filtrage et la recherche des données à l’aide de l’index multidimensionnel de la plateforme de stockage.

## <a name="setting-blob-index-tags"></a>Définition des étiquettes d’un index d’objet blob

Les étiquettes d’un index d’objet blob sont des attributs clé-valeur pouvant être appliqués à des objets nouveaux ou existants dans votre compte de stockage. Vous pouvez spécifier les étiquettes d’index pendant le processus de chargement à l’aide des opérations PutBlob, PutBlockList ou CopyBlob et de l’en-tête x-ms-tags facultatif. Si vous avez déjà des objets blob dans votre compte de stockage, vous pouvez appeler SetBlobTags à l’aide d’un document XML mis en forme, en spécifiant les attributs des étiquettes d’index d’objet blob dans le corps de la demande. 

Considérez les exemples d’étiquettes suivants qui peuvent être définis

Vous pouvez appliquer une étiquette individuelle à votre objet blob pour décrire le moment où vos données finissent d’être traitées.
>
> "processedDate" = '2020-01-01'
>
Vous pouvez appliquer plusieurs étiquettes à votre objet blob pour mieux décrire les données.
>
> "Project" = 'Contoso'  
> "Classified" = 'True'  
> "Status" = 'Unprocessed'  
> "Priority" = '01' 
>

Pour modifier les attributs d’étiquette d’index existants, vous devez d’abord récupérer les attributs d’étiquette existants, les modifier et effectuer le remplacement dans une opération SetBlobTags. Pour supprimer toutes les étiquettes d’index de l’objet blob, appelez l’opération SetBlobTags sans spécifier d’attributs d’étiquette. Comme les étiquettes d’index d’objet blob sont une sous-ressource du contenu des données blob, SetBlobTags ne modifie pas le contenu sous-jacent et ne modifie pas la propriété Last-Modified-Time ou eTag (étiquette d’entité) de l’objet blob. Vous pouvez créer ou modifier des étiquettes d’index pour tous les objets BLOB de base actuels et les versions précédentes. Toutefois, les étiquettes sur les instantanés ou les objets BLOB supprimés de manière réversible ne peuvent pas être modifiées. 

Les limites suivantes s’appliquent aux étiquettes d’un index d’objet blob :
- Chaque objet blob peut avoir jusqu’à 10 étiquettes d’index d’objet blob.
- Les clés d’étiquette doivent comporter entre 1 et 128 caractères.
- Les valeurs d’étiquette doivent comporter entre 0 et 256 caractères.
- Les clés et les valeurs d’étiquette sont sensibles à la casse.
- Les clés et les valeurs d'étiquette prennent uniquement en charge les types de données de chaîne. Tous les nombres, dates, heures ou caractères spéciaux sont enregistrés sous forme de chaînes.
- Les clés et les valeurs d’étiquette doivent respecter les règles de nommage suivantes :
  - Caractères alphanumériques : a-z, A-Z, 0-9
  - Caractères spéciaux : espace, plus, moins, point, deux-points, égal, trait de soulignement, barre oblique

## <a name="getting-and-listing-blob-index-tags"></a>Obtention et affichage de la liste des étiquettes d’index d’objet blob

Les étiquettes d’index d’objet blob sont stockées en tant que sous-ressource avec les données blob et peuvent être récupérées indépendamment du contenu sous-jacent des données d’objet blob. Une fois définies, les étiquettes d’index d’objet blob d’un objet blob individuel peuvent être récupérées et consultées immédiatement avec l’opération GetBlobTags. L’opération ListBlobs avec le paramètre `include:tags` renverra également tous les objets blob dans un conteneur avec leurs étiquettes d’index d’objet blob appliquées. 

Pour tous les objets blob dotés d’au moins 1 étiquette d’index d’objet blob, la valeur x-ms-tag-count est retournée dans les opérations ListBlobs, GetBlob et GetBlobProperties, indiquant le nombre d’étiquettes d’index d’objet blob présentes sur l’objet blob.

## <a name="finding-data-using-blob-index-tags"></a>Recherche de données à l’aide d’étiquettes d’index d’objet blob

Quand des étiquettes d’index d’objet blob sont définies sur vos objets blob, le moteur d’indexation expose ces attributs clé/valeur dans un index multidimensionnel. Vos étiquettes d’index existent sur l’objet blob et peuvent être récupérées immédiatement, mais la mise à jour de l’index d’objet blob avec les attributs d’étiquette d’index actualisés peut prendre un certain temps. Une fois l’index d’objet blob mis à jour, vous pouvez tirer parti des fonctionnalités natives de requête et de découverte offertes par le stockage d’objets blob.

L’opération FindBlobsByTags vous permet d’obtenir un ensemble filtré d’objets blob retournés dont les étiquettes d’index correspondent à une expression donnée de requête d’index d’objet blob. L’index d’objet blob prend en charge le filtrage sur tous les conteneurs dans votre compte de stockage ou vous pouvez limiter le filtrage à un seul conteneur. Comme toutes les clés et valeurs des étiquettes d’index d’objet blob sont des chaînes, les opérateurs relationnels pris en charge utilisent un tri lexicographique des valeurs des étiquettes d’index.

Les critères suivants s’appliquent au filtrage de l’index d’objet blob :
-   Les clés des étiquettes doivent être placées entre guillemets doubles (").
-   Les valeurs des étiquettes et les noms des conteneurs doivent être placés entre guillemets simples (').
-   Seul le caractère @ est autorisé pour le filtrage sur un nom de conteneur spécifique (c’est-à-dire @container = 'ContainerName')
- Les filtres sont appliqués avec un tri lexicographique sur les chaînes.
-   Des opérations de comparaison dans un même sens sur la même clé ne sont pas valides (p. ex. "Rank" > '10' AND "Rank" >= '15')
- Lorsque vous utilisez REST pour créer une expression de filtre, les caractères doivent être encodés sous forme d’URI.

Le tableau ci-dessous montre tous les opérateurs valides pour FindBlobsByTags :

|  Opérateur  |  Description  | Exemple |
|------------|---------------|---------|
|     =      |     Égal à     | "Status" = 'In Progress' | 
|     >      |  Supérieur à |  "Date" > '2018-06-18' |
|     >=     |  Supérieur ou égal à | "Priority" >= '5' | 
|     <      |  Inférieur à    | "Age" < '32' |
|     <=     |  Inférieur ou égal à  | "Company" <= 'Contoso' |
|    AND     |  ET logique  | "Rank" >= '010' AND "Rank" < '100' |
| @container |  Limiter l’étendue à un conteneur spécifique   | @container = 'videofiles' AND "status" = 'done' |

> [!NOTE]
> Familiarisez-vous avec l'ordre lexicographique lors de la définition et de l'interrogation des étiquettes.
> - Les nombres sont triés avant les lettres. Les nombres sont triés sur la base du premier chiffre.
> - Les lettres majuscules sont triées avant les lettres minuscules.
> - Les symboles ne sont pas standard. Certains symboles sont triés avant les valeurs numériques. Les autres symboles sont triés avant ou après les lettres.
>

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Opérations d’objet blob conditionnelles avec étiquettes d’index d’objet blob
Dans les versions REST 2019-10-10 et ultérieures, la plupart des [API de service blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) prennent désormais en charge un en-tête conditionnel, x-ms-if-tags, tel que l’opération réussira uniquement si la condition d’index d’objet blob spécifiée est remplie. Si la condition n’est pas remplie, vous obtiendrez `error 412: The condition specified using HTTP conditional header(s) is not met`.

L’en-tête x-ms-if-tags peut être combiné avec les autres en-têtes conditionnels HTTP existants (If-Match, If-None-Match, etc.).  Si plusieurs en-têtes conditionnels sont fournis dans une demande, ils doivent tous équivaloir à true pour que l’opération réussisse.  Tous les en-têtes conditionnels sont combinés de manière efficace avec un ET logique. 

Le tableau ci-dessous montre tous les opérateurs valides pour les opérations conditionnelles :

|  Opérateur  |  Description  | Exemple |
|------------|---------------|---------|
|     =      |     Égal à     | "Status" = 'In Progress' |
|     <>     |   Non égal à   | "Status" <> 'Done'  | 
|     >      |  Supérieur à |  "Date" > '2018-06-18' |
|     >=     |  Supérieur ou égal à | "Priority" >= '5' | 
|     <      |  Inférieur à    | "Age" < '32' |
|     <=     |  Inférieur ou égal à  | "Company" <= 'Contoso' |
|    AND     |  ET logique  | "Rank" >= '010' AND "Rank" < '100' |
|     OR     |  OU logique   | "Status" = 'Done' OR "Priority" >= '05' |

> [!NOTE]
> Il existe deux opérateurs supplémentaires, Non égal à et OU logique, qui sont autorisés dans l’en-tête x-ms-if-tags conditionnel pour une opération d’objet blob, mais qui n’existent pas dans l’opération FindBlobsByTags.

## <a name="platform-integrations-with-blob-index-tags"></a>Intégrations de plateforme avec des étiquettes d’index d’objet blob

Les étiquettes d’index d’objet blob vous permettent de classer, de gérer et de rechercher vos données blob et fournissent également des intégrations avec d’autres fonctionnalités de service blob, telles que la [gestion du cycle de vie](storage-lifecycle-management-concepts.md). 

### <a name="lifecycle-management"></a>Gestion du cycle de vie

L’utilisation du nouveau blobIndexMatch en tant que filtre de règle dans la gestion du cycle de vie vous permet de déplacer les données vers des niveaux plus froids ou de supprimer des données en fonction des étiquettes d’index appliquées à vos objets blob. Cela vous permet d’être plus précis dans vos règles et de déplacer ou supprimer uniquement les données qui correspondent aux critères d’étiquette spécifiés.

Vous pouvez définir une correspondance d’index d’objet blob comme filtre autonome défini dans une règle de cycle de vie pour appliquer des actions aux données avec étiquettes. Vous pouvez également combiner une correspondance de préfixe et une correspondance d’index d’objet blob pour établir une correspondance avec des jeux de données plus spécifiques. L’application de plusieurs filtres à une règle de cycle de vie constitue une opération ET logique de sorte que l’action s’appliquera uniquement si tous les critères de filtre correspondent. 

L’exemple de règle de gestion du cycle de vie suivant s’applique aux objets blob de blocs dans le conteneur 'videofiles' et aux objets blob de niveau pour le stockage archive seulement si les données correspondent aux critères d’étiquette d’index d’objet blob ```"Status" = 'Processed' AND "Source" == 'RAW'```.

# <a name="portal"></a>[Portail](#tab/azure-portal)
![Exemple de règle de correspondance d’index d’objet blob pour la gestion du cycle de vie dans le portail Azure](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Autorisation et permissions

Vous pouvez autoriser l’accès à l’index d’objet blob à l’aide de l’une des approches suivantes :

- En utilisant le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité Azure Active Directory (Azure AD). Microsoft recommande d’utiliser Azure AD pour une meilleure sécurité et une plus grande facilité d’utilisation. Pour plus d’informations sur l’utilisation d’Azure AD avec les opérations d’objet blob, consultez [Autoriser l’accès aux objets blob et aux files d’attente avec Azure Active Directory](../common/storage-auth-aad.md).
- En utilisant une signature d’accès partagé (SAS) pour déléguer l’accès à l’index d’objet blob. Pour plus d’informations sur les signatures d’accès partagé, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../common/storage-sas-overview.md).
- En utilisant les clés d’accès au compte pour autoriser les opérations avec une clé partagée. Pour plus d’informations, consultez [Autoriser avec une clé partagée](/rest/api/storageservices/authorize-with-shared-key).

Les étiquettes d’index d’objet blob sont une sous-ressource des données blob. Un utilisateur disposant d’autorisations ou d’un jeton SAS pour lire ou écrire des objets blob peut ne pas avoir accès aux étiquettes d’index d’objet blob. 

### <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle 
Les appelants utilisant une [identité AAD](../common/storage-auth-aad.md) peuvent se voir accorder les autorisations suivantes pour effectuer des opérations sur les étiquettes d’index d’objet blob. 

|   Opérations d’objet blob   |  Action RBAC   |
|---------------------|----------------|
| Rechercher des objets blob par étiquettes  | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |
| Définir des étiquettes d’objet blob         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | 
| Obtenir les étiquettes d’objet blob         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read |

Des autorisations supplémentaires distinctes des données blob sous-jacentes sont requises pour effectuer des opérations sur les étiquettes. Le rôle de contributeur aux données Blob du stockage se verra accorder ces trois autorisations. Le lecteur des données blob du stockage se verra accorder les seules autorisations Rechercher des objets blob par étiquettes et Obtenir les étiquettes d’objet blob.

### <a name="sas-permissions"></a>Autorisations SAS 
Les appelants utilisant une [signature d’accès partagé (SAS)](../common/storage-sas-overview.md) peuvent se voir accorder des autorisations délimitées pour effectuer des opérations sur les étiquettes d’objet blob.
#### <a name="blob-sas"></a>Signature d’accès partagé d’objet blob
Les autorisations suivantes peuvent être accordées dans une signature d’accès partagé de service blob pour permettre l’accès aux étiquettes d’index d’objet blob. Les autorisations de lecture et d’écriture d’objet blob seules ne sont pas suffisantes pour permettre la lecture ou l’écriture de ses étiquettes d’index.

|  Autorisation  |  Symbole d’URI  | Opérations autorisées |
|--------------|--------------|--------------------|
|  Étiquettes d’index  |      t         | Obtenir et définir des étiquettes d’index d’objet blob pour un objet blob |

#### <a name="container-sas"></a>SAP de conteneur
Les autorisations suivantes peuvent être accordées dans une signature d’accès partagé de service conteneur pour permettre le filtrage sur les étiquettes d’objet blob.  L’autorisation de liste d’objets blob n’est pas suffisante pour permettre le filtrage des objets blob par leurs étiquettes d’index.

|  Autorisation  |  Symbole d’URI  | Opérations autorisées |
|--------------|--------------|--------------------|
| Étiquettes d’index     |      f       | Rechercher des objets blob avec les étiquettes d’index d’objet blob | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Choisir entre les métadonnées et les étiquettes d’index d’objet blob 
Les étiquettes et les métadonnées d’index d’objet blob permettent de stocker des propriétés de clé/valeur arbitraires définies par l’utilisateur en même temps qu’une ressource d’objet blob. Les deux peuvent être récupérés et définis directement, sans retourner ni modifier le contenu de l’objet blob. Il est possible d’utiliser à la fois des étiquettes d’index et des métadonnées.

Toutefois, seules les étiquettes d’index d’objet blob sont indexées automatiquement et peuvent être interrogées par le service blob natif. Les métadonnées ne peuvent pas être indexées en mode natif et interrogées, sauf si vous utilisez un service distinct, tel que [Recherche Azure](../../search/search-blob-ai-integration.md). Les étiquettes d’index d’objet blob disposent également d’autorisations supplémentaires pour la lecture/le filtrage et l’écriture qui sont distinctes des données d’objet blob sous-jacentes. Les métadonnées utilisent les mêmes autorisations que l’objet blob et sont retournées sous forme d’en-têtes HTTP dans les opérations GetBlob ou GetBlobProperties. Les étiquettes d’index d’objet blob sont chiffrées au repos à l’aide d’une [clé gérée par Microsoft](../common/storage-service-encryption.md), tandis que les métadonnées sont chiffrées au repos à l’aide de la même clé de chiffrement spécifiée pour les données blob. 

Le tableau suivant récapitule les différences entre les métadonnées et les étiquettes d’index d’objet blob :

|              |   Métadonnées   |   Étiquettes d’index d’objet blob  |
|--------------|--------------|--------------------|
| **Limites**         | Aucune limite numérique ; 8 Ko au total ; ne respectent pas la casse | 10 étiquettes par objet blob au max. ; 768 octets par étiquette ; respectent la casse |
| **Mises à jour**      | Non autorisées sur le niveau Archive ; SetBlobMetadata remplace toutes les métadonnées existantes ; SetBlobMetadata modifie la propriété Last-Modified-Time de l’objet blob | Autorisées pour tous les niveaux d’accès ; SetBlobTags remplace toutes les étiquettes existantes ; SetBlobTags ne modifie pas la propriété Last-Modified-Time de l’objet blob |
| **Stockage**        | Stockées avec les données blob |  Sous-ressource des données blob | 
| **Indexation et interrogation** | N/A en mode natif ; doit utiliser un service distinct comme Recherche Azure | Oui, les fonctionnalités natives d’indexation et d’interrogation sont intégrées au stockage d’objets blob |
| **Chiffrement** | Chiffrées au repos avec la même clé de chiffrement que celle utilisée pour les données blob |  Chiffrées au repos avec une clé de chiffrement gérée par Microsoft |
| **Tarification**   | La taille des métadonnées est incluse dans les coûts de stockage d’un objet blob |    Coût fixe par étiquette d’index | 
| **Réponse d’en-tête** | Métadonnées retournées en tant qu’en-têtes dans GetBlob et GetBlobProperties | TagCount retourné dans GetBlob ou GetBlobProperties ; étiquettes retournées uniquement dans GetBlobTags et ListBlobs |
| **autorisations**  |    Les autorisations de lecture ou d’écriture sur les données blob s’étendent aux métadonnées |    Des autorisations supplémentaires sont requises pour lire/filtrer ou écrire des étiquettes |

## <a name="pricing"></a>Tarifs
Les prix de l’index d’objet blob sont actuellement en préversion publique et sujets à modification en ce qui concerne la disponibilité générale. Le nombre total des étiquettes d’index d’objet blob figurant dans un compte de stockage est facturé aux clients, moyenné sur le mois. Aucun coût ne s’applique au moteur d’indexation. Les demandes adressées à SetBlobTags, GetBlobTags et FindBlobsByTags sont facturées conformément à leurs types d’opération respectifs. Consultez [Tarification d’objet blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/) pour en savoir plus.

## <a name="regional-availability-and-storage-account-support"></a>Disponibilité régionale et prise en charge des comptes de stockage

L'index d'objet blob n'est actuellement disponible que sur les comptes Usage général v2 (GPv2) sur lesquels l'espace de noms hiérarchique est désactivé. Les comptes Usage général (GPV1) ne sont pas pris en charge, mais vous pouvez mettre à niveau n'importe quel compte GPv1 pour le transformer en compte GPv2. Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

En préversion publique, l’index d’objet blob est actuellement disponible uniquement dans les régions suivantes :
- Centre du Canada
- Est du Canada
- France Centre
- France Sud

Pour commencer, consultez [Utiliser un index d’objet blob pour gérer et rechercher des données](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Consultez la section des conditions de cet article. Pour vous inscrire à la préversion, consultez la section Inscrire votre abonnement, dans cet article. Vous devez inscrire votre abonnement avant de pouvoir utiliser l’index d’objet blob sur vos comptes de stockage.

### <a name="register-your-subscription-preview"></a>Inscrire votre abonnement (préversion)
Étant donné que l’index d’objet blob est uniquement en préversion publique, vous devez inscrire votre abonnement avant de pouvoir utiliser la fonctionnalité. Pour soumettre une requête, exécutez les commandes PowerShell ou CLI suivantes.

#### <a name="register-by-using-powershell"></a>S’inscrire à l’aide de PowerShell
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>S’inscrire à l’aide d’Azure CLI
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Conditions et problèmes connus (préversion)
Cette section décrit les problèmes connus et les conditions de la préversion publique actuelle de l’index d’objet blob. Comme pour la plupart des préversions, cette fonctionnalité ne doit pas être utilisée pour les charges de travail de production avant d’avoir atteint la disponibilité générale, car les comportements peuvent changer.

-   Pour la préversion, vous devez d’abord inscrire votre abonnement avant de pouvoir utiliser l’index d’objet blob pour votre compte de stockage dans les régions en préversion.
-   Seuls les comptes GPv2 sont actuellement pris en charge dans la préversion. Les comptes d’objet blob, BlockBlobStorage et DataLake Gen2 avec HNS activé ne sont actuellement pas pris en charge avec l’index d’objet blob. Les comptes GPv1 ne seront pas pris en charge.
-   Le chargement d’objets blob de pages avec des étiquettes d’index ne conserve pas les étiquettes. Vous devez définir les étiquettes après le chargement d’un objet blob de pages.
-   Lorsque le filtrage est limité à un conteneur individuel, il est possible de passer @container seulement si toutes les étiquettes d’index dans l’expression de filtre sont des contrôles d’égalité (clé=valeur). 
-   Lorsque vous utilisez l’opérateur de comparaison avec la condition AND, vous pouvez uniquement spécifier le même nom de clé d’étiquette d’index (Age > '013' AND Age < '100').
-   La gestion de versions et l’index d’objet blob ne sont actuellement pas pris en charge. Les étiquettes d’index d’objet blob sont conservées pour les versions mais ne sont actuellement pas passées au moteur d’index d’objet blob.
-   Le basculement de compte n’est pas pris en charge pour le moment. L’index d’objet blob peut ne pas être mis à jour correctement après le basculement.
-   La gestion du cycle de vie prend actuellement en charge uniquement les contrôles d’égalité avec correspondance d’index d’objet blob.
-   CopyBlob ne copie pas les étiquettes d’index d’objet blob de l’objet blob source vers le nouvel objet blob de destination. Vous pouvez spécifier les étiquettes que vous souhaitez appliquer à l’objet blob de destination pendant l’opération de copie. 
- En cas d’utilisation de CopyBlob (copie asynchrone) à partir d’un autre compte de stockage avec des étiquettes appliquées sur l’objet blob de destination, le moteur d’index d’objets blob ne retourne pas l’objet blob ni ses balises dans le jeu de filtres. Il est recommandé d’utiliser CopyBlob à partir de l’URL (copie synchrone) en attendant.
-   Les étiquettes sont conservées lors de la création d’une capture instantanée. Toutefois, la promotion d’une capture instantanée n’est actuellement pas prise en charge et peut aboutir à un ensemble d’étiquettes vide.

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>L’index d’objet blob peut-il m’aider à filtrer et à interroger le contenu de mes objets blob ? 
Non, les balises d’index d’objet blob peuvent vous aider à trouver les objets blob que vous recherchez. Si vous devez rechercher dans vos objets blob, utilisez l’accélération des requêtes ou la recherche Azure.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Y a-t-il des considérations particulières concernant les valeurs des étiquettes d'index d'objet blob ?
Les étiquettes d'index d'objet blob ne prennent en charge que les types de données de chaîne, et l'interrogation renvoie les résultats dans l'ordre lexicographique. Pour les nombres, il est recommandé de procéder à un remplissage à l'aide de zéros. Pour la date et les heures, il est recommandé de les stocker dans un format compatible avec la norme ISO 8601.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Les étiquettes d’index d’objet blob et les étiquettes Azure Resource Manager sont-elles liées ?
Non, les étiquettes Azure Resource Manager aident à organiser les ressources de plan de contrôle telles que les abonnements, les groupes de ressources et les comptes de stockage. Les étiquettes d’index d’objet blob fournissent la découverte et la gestion d’objets sur des ressources de plan de données telles que des objets blob dans un compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un exemple d’utilisation d’un index d’objet blob, consultez [Utiliser un index d’objet blob pour gérer et rechercher des données](storage-blob-index-how-to.md).

En savoir plus sur la [gestion du cycle de vie](storage-lifecycle-management-concepts.md) et définir une règle avec correspondance d’index d’objet blob.

