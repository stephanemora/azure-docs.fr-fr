---
title: Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)
titleSuffix: Azure Storage
description: Actions et attributs pris en charge pour les conditions d’attribution de rôle Azure et le contrôle d’accès en fonction des attributs Azure (Azure ABAC) dans le stockage Azure.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 45a0fbac0fbe1bd513717ba1ab1d30b3cffbf78e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110065941"
---
# <a name="actions-and-attributes-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article décrit les dictionnaires d’attributs pris en charge qui peuvent être utilisés dans des conditions sur les attributions de rôle Azure pour chaque [DataAction](../../role-based-access-control/role-definitions.md#dataactions) du Stockage Azure. Pour obtenir la liste des opérations de service blob qui sont affectées par une autorisation ou un DataAction spécifique, consultez [Autorisations pour les opérations de service blob](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-blob-service-operations).

Pour comprendre le format de la condition d’attribution de rôle, consultez la section [Format et syntaxe des conditions d’attribution de rôle Azure](../../role-based-access-control/conditions-format.md).

## <a name="suboperations"></a>Sous-opérations

Plusieurs opérations de service de stockage peuvent être associées à une seule autorisation ou DataAction. Toutefois, chacune de ces opérations associées à la même autorisation peut prendre en charge des paramètres différents. Les *sous-opérations* vous permettent de différencier les opérations de service qui requièrent la même autorisation, mais prennent en charge différents ensembles d’attributs pour les conditions. Ainsi, en utilisant une sous-opération, vous pouvez spécifier une condition d’accès à un sous-ensemble d’opérations qui prennent en charge un paramètre donné. Ensuite, vous pouvez utiliser une autre condition d’accès pour les opérations avec la même action sans prise en charge de ce paramètre.

Par exemple, l’action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` est requise pour plus d’une douzaine d’opérations de service différentes. Certaines de ces opérations peuvent accepter des balises d’index d’objet blob en tant que paramètre de requête, contrairement à d’autres. Pour les opérations qui acceptent les balises d’index d’objet blob en tant que paramètre, vous pouvez utiliser des balises d’index d’objet blob dans une condition de demande. Toutefois, si une telle condition est définie sur l’action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`, toutes les opérations qui n’acceptent pas de balises en tant que paramètre de demande ne peuvent pas évaluer cette condition et échouent à la vérification de l’accès à l’autorisation.

Dans ce cas, la sous-opération facultative `Blob.Write.WithTagHeaders` peut être utilisée pour appliquer une condition uniquement aux opérations qui prennent en charge les balises d’index d’objet blob en tant que paramètre de requête.

De même, seules les opérations de sélection sur l’action `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` peuvent avoir la prise en charge des balises d’index d’objet blob comme condition préalable pour l’accès. Ce sous-ensemble d’opérations est identifié par la sous-opération `Blob.Read.WithTagConditions`.

> [!NOTE]
> Les objets blob prennent également en charge la possibilité de stocker des $$$métadonnées clé/valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux étiquettes d’index d’objet blob, vous devez utiliser des étiquettes d’index d’objet blob avec les conditions. Pour plus d’informations, consultez [Gérer et rechercher des données sur le stockage d’objets blob Azure avec un index d’objets blob (préversion)](../blobs/storage-manage-find-blobs.md).

Dans cette préversion, les comptes de stockage prennent en charge les sous-opérations suivantes :

> [!div class="mx-tableFixed"]
> | DataAction | Sous-opération | Nom complet | Description |
> | :--- | :--- | :--- | :--- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.Read.WithTagConditions` | Opérations de lecture d’objets blob qui prennent en charge les conditions sur les balises | Comprend les opérations REST Obtenir un objet blob, Obtenir les métadonnées de l’objet blob, Obtenir les propriétés de l’objet blob, Obtenir la liste de blocage, Obtenir les plages de pages et le Contenu des objets blob. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` <br/> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `Blob.Write.WithTagHeaders` | Écritures d’objets blob pour le contenu avec des balises facultatives | Comprend les opérations REST Placer l’objet blob, Copier l’objet blob, Copier l’objet blob à partir de l’URL et Placer en liste de blocage. |

## <a name="actions-and-suboperations"></a>Actions et sous-opérations

Le tableau suivant répertorie les actions et sous-opérations prises en charge pour les conditions dans le stockage Azure.

> [!div class="mx-tableFixed"]
> | Nom complet | Description | DataAction |
> | --- | --- | --- |
> | Supprimer un objet blob | DataAction pour la suppression d’objets blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` |
> | Lire un objet blob | DataAction pour la lecture d’objets blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | Lire du contenu à partir d’un objet blob avec des conditions de balise  | Opérations REST Obtenir un objet blob, Obtenir les métadonnées de l’objet blob, Obtenir les propriétés de l’objet blob, Obtenir la liste de blocage, Obtenir les plages de pages et Contenu des objets blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**Sous-opération**<br/>`Blob.Read.WithTagConditions` | 
> | Écrire dans un objet blob | DataAction pour l’écriture dans des objets blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | Écrire dans un objet blob avec des balises d’index d’objet blob | Opérations REST : Placer l’objet blob, Placer en liste de blocage, Copier l’objet blob et copier l’objet blob à partir de l’URL. |`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**Sous-opération**<br/>`Blob.Write.WithTagHeaders` | 
> | Créer un objet blob ou un instantané, ou ajouter des données | DataAction pour la création d’objets blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | Écrire du contenu dans un objet blob avec des balises d’index d’objet blob | Opérations REST : Placer l’objet blob, Placer en liste de blocage, Copier l’objet blob et copier l’objet blob à partir de l’URL. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**Sous-opération**<br/>`Blob.Write.WithTagHeaders` | 
> | Supprimer une version d’un objet blob | DataAction pour la suppression d’une version d’un objet blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` |
> | Modifier la propriété d’un objet blob | DataAction pour la modification de la propriété d’un objet blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` |
> | Modifier les autorisations d’un objet blob | DataAction pour la modification des autorisations d’un objet blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` |
> | Renommer un fichier ou un répertoire | DataAction pour renommer des fichiers ou des répertoires. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` |
> | Supprimer définitivement un objet blob en remplaçant la suppression réversible | DataAction pour la suppression définitive d’un objet blob en remplaçant la suppression réversible. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` |
> | Toutes les opérations de données pour les comptes avec HNS | DataAction pour toutes les opérations de données sur les comptes de stockage avec HNS. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` |
> | Lire des balises d’index blob | DataAction pour la lecture des balises d’index d’objet blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` |
> | Écrire des balises d’index blob | DataAction pour l’écriture des balises d’index d’objet blob. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` |

## <a name="attributes"></a>Attributs

Le tableau suivant répertorie les descriptions des attributs pris en charge pour les conditions dans le stockage Azure.

> [!div class="mx-tableFixed"]
> | Nom complet | Description | Attribut |
> | --- | --- | --- |
> | Nom du conteneur| Nom d’un conteneur de stockage ou d’un système de fichiers. Utilisez lorsque vous souhaitez vérifier le nom du conteneur. | `containers:name` |
> | Chemin d’accès d’objet blob | Chemin d’accès d’un répertoire virtuel, d’un objet blob, d’un dossier ou d’une ressource de fichier. Utilisez lorsque vous souhaitez vérifier le nom ou les dossiers de l’objet blob dans un chemin d’accès d’objet blob. | `blobs:path` |
> | Balises d’index d’objet blob [Clés] | Balises d’index sur une ressource d’objet blob. Propriétés de valeur de clé définies par l’utilisateur arbitraires que vous pouvez stocker avec une ressource d’objet blob. Utilisez lorsque vous souhaitez vérifier la clé dans les balises d’index d’objet blob. | `tags&$keys$&` |
> | Balises d’index d’objet blob [Valeurs dans la clé] | Balises d’index sur une ressource d’objet blob. Propriétés de valeur de clé définies par l’utilisateur arbitraires que vous pouvez stocker avec une ressource d’objet blob. Utilisez lorsque vous souhaitez vérifier la valeur de la clé (sensible à la casse) et la valeur dans les balises d’index d’objet blob. | `tags:`*keyname*`<$key_case_sensitive$>` |

> [!NOTE]
> Les attributs et les valeurs répertoriés sont considérés comme non sensibles à la casse, sauf indication contraire.

> [!NOTE]
> Lorsque vous spécifiez des conditions pour l’attribut `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path`, les valeurs ne doivent pas inclure le nom du conteneur ou commencer par un caractère « / ». Utilisez les caractères de chemin d’accès sans encodage d’URL.

> [!NOTE]
> Les étiquettes d’index d’objet blob ne sont pas prises en charge pour les comptes de stockage Data Lake Storage Gen2, qui ont un [espace de noms hiérarchique](../blobs/data-lake-storage-namespace.md) (HNS). Vous ne devez pas créer de conditions d’attribution de rôles à l’aide d’étiquettes d’index sur les comptes de stockage pour lesquels HNS est activé.

## <a name="attributes-available-for-each-action"></a>Attributs disponibles pour chaque action

Le tableau suivant répertorie les attributs que vous pouvez utiliser dans vos expressions de condition, en fonction de l’action que vous ciblez. Si vous sélectionnez plusieurs actions pour une même condition, vous pouvez choisir moins d’attributs pour votre condition, car ceux-ci doivent être disponibles au sein des actions sélectionnées.

> [!div class="mx-tableFixed"]
> | DataAction | Attribut | Type | S’applique à |
> | --- | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**Sous-opération**<br/>`Blob.Read.WithTagConditions` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**Sous-opération**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**Sous-opération**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` | `containers:name` | string | ResourceAttributeOnly |
> |  | `blobs:path` | string | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |

## <a name="see-also"></a>Voir aussi

- [Exemples de conditions d’attribution de rôle Azure (préversion)](storage-auth-abac-examples.md)
- [Format et syntaxe des conditions d’attribution de rôle Azure (préversion)](../../role-based-access-control/conditions-format.md)
- [Qu’est-ce que le contrôle d’accès basé sur les attributs Azure (Azure ABAC) ? (préversion)](../../role-based-access-control/conditions-overview.md)

