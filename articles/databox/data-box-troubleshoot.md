---
title: Résoudre les problèmes sur votre Azure Data Box, lourd de zone de données Azure | Microsoft Docs
description: Décrit comment résoudre les problèmes rencontrés dans Azure Data Box et lourdes de zone de données Azure lors de la copie des données sur ces appareils.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 0c454c5f19ebefc7f91df62511448dbedb93dfc4
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257284"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Résoudre les problèmes liés à Azure Data Box et lourdes de zone de données Azure

Cet article fournit des informations sur la façon de résoudre les problèmes peut s’afficher lorsque vous utilisez la Boxn de données Azure ou lourd de zone de données Azure détaillées.

## <a name="errors-during-data-copy"></a>Erreurs lors de la copie des données

Toutes les erreurs sont observés pendant la copie des données sont résumées dans les sections suivantes.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Description de l’erreur :** Le nom du conteneur ou du partage doit comporter entre 3 et 63 caractères. 

**Résolution suggérée :** Le dossier sous le share(SMB/NFS) Data Box ou élevée de zone de données à laquelle vous avez copié des données devient un conteneur dans votre compte de stockage Azure. 

- Sur le **Connect et copie** page de l’interface utilisateur web locale de périphérique, téléchargement et examinez les fichiers d’erreur pour identifier le dossier des noms avec des problèmes.
- Modifier le nom du dossier sous le partage de Data Box ou élevée de zone de données pour vous assurer que :

    - Le nom a entre 3 et 63 caractères.
    - Les noms ne peuvent avoir que des lettres, des chiffres et des traits d’union.
    - Les noms ne peut pas commencer ou finir par des traits d’union.
    - Les noms ne peuvent pas avoir des traits d’union consécutifs.
    - Exemples de noms valides : `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemples de noms qui ne sont pas valides : `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour [les noms de conteneur](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) et [partagent noms](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Description de l’erreur :** Le nom du conteneur ou du partage doit uniquement comporter des lettres, des chiffres ou des traits d’union.

**Résolution suggérée :** Le dossier sous le share(SMB/NFS) Data Box ou élevée de zone de données à laquelle vous avez copié des données devient un conteneur dans votre compte de stockage Azure. 

- Sur le **Connect et copie** page de l’interface utilisateur web locale de périphérique, téléchargement et examinez les fichiers d’erreur pour identifier le dossier des noms avec des problèmes.
- Modifier le nom du dossier sous le partage de Data Box ou élevée de zone de données pour vous assurer que :

    - Le nom a entre 3 et 63 caractères.
    - Les noms ne peuvent avoir que des lettres, des chiffres et des traits d’union.
    - Les noms ne peut pas commencer ou finir par des traits d’union.
    - Les noms ne peuvent pas avoir des traits d’union consécutifs.
    - Exemples de noms valides : `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemples de noms qui ne sont pas valides : `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour [les noms de conteneur](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) et [partagent noms](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Description de l’erreur :** Les noms de conteneur et les noms de partage ne peut pas commencer ou se terminer par des traits d’union et ne peut pas avoir des traits d’union consécutifs.

**Résolution suggérée :** Le dossier sous le share(SMB/NFS) Data Box ou élevée de zone de données à laquelle vous avez copié des données devient un conteneur dans votre compte de stockage Azure. 

- Sur le **Connect et copie** page de l’interface utilisateur web locale de périphérique, téléchargement et examinez les fichiers d’erreur pour identifier le dossier des noms avec des problèmes.
- Modifier le nom du dossier sous le partage de Data Box ou élevée de zone de données pour vous assurer que :

    - Le nom a entre 3 et 63 caractères.
    - Les noms ne peuvent avoir que des lettres, des chiffres et des traits d’union.
    - Les noms ne peut pas commencer ou finir par des traits d’union.
    - Les noms ne peuvent pas avoir des traits d’union consécutifs.
    - Exemples de noms valides : `my-folder-1`, `my-really-extra-long-folder-111`
    - Exemples de noms qui ne sont pas valides : `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour [les noms de conteneur](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) et [partagent noms](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Description de l’erreur :** Les noms de conteneur incorrecte sont spécifiés pour les partages de disque géré.

**Résolution suggérée :** Pour les disques gérés, au sein de chaque partage, les dossiers suivants sont créés qui correspondent aux conteneurs dans votre compte de stockage : Disque SSD Premium, Standard HDD et SSD Standard. Ces dossiers correspondent au niveau de performances pour le disque géré.

- Assurez-vous que vous copiez vos données d’objet blob de page (disques durs virtuels) dans un de ces dossiers existants. Seules les données à partir de ces conteneurs existants sont téléchargées vers Azure.
- N’importe quel autre dossier est créé au même niveau que Premium SSD Standard HDD et SSD Standard ne correspond pas à un niveau de performances valide et ne peut pas être utilisé.
- Supprimer des fichiers ou dossiers créés en dehors des niveaux de performance.

Pour plus d’informations, consultez [copie vers des disques gérés](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Description de l’erreur :** Partage de fichiers Azure limite un partage à 5 To de données. Cette limite a dépassé certaines actions.

**Résolution suggérée :** Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.

Identifiez les dossiers qui ont ce problème dans les journaux d’erreur et assurez-vous que les fichiers dans ce dossier sont de taille inférieure à 5 To.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Description de l’erreur :** Les noms d’objet blob ou fichier contient des caractères de contrôle non pris en charge.

**Résolution suggérée :** Les objets BLOB ou les fichiers que vous avez copié contiennent les noms avec des caractères non pris en charge.

Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
Supprimez ou renommez les fichiers pour supprimer des caractères non pris en charge.

Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour [noms d’objets blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) et [des noms de fichier](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Description de l’erreur :** Les noms d’objet blob ou fichier contiennent des caractères non autorisés.

**Résolution suggérée :** Les objets BLOB ou les fichiers que vous avez copié contiennent les noms avec des caractères non pris en charge.

Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
Supprimez ou renommez les fichiers pour supprimer des caractères non pris en charge.

Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour [noms d’objets blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) et [des noms de fichier](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Description de l’erreur :** Les noms d’objet blob ou fichier sont terminent avec des caractères non valides.

**Résolution suggérée :** Les objets BLOB ou les fichiers que vous avez copié contiennent les noms avec des caractères non pris en charge.

Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
Supprimez ou renommez les fichiers pour supprimer des caractères non pris en charge.

Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour [noms d’objets blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) et [des noms de fichier](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Description de l’erreur :** L’objet blob ou nom de fichier contient trop de segments de chemin d’accès.

**Résolution suggérée :** Les objets BLOB ou les fichiers que vous avez copié dépassent le nombre maximal de segments de chemin d’accès. Un segment de chemin d’accès est la chaîne de caractères de séparation consécutifs, par exemple, la barre oblique /.

- Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
- Assurez-vous que le [noms d’objets blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) et [les noms de fichiers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) est conforme aux conventions d’affectation de noms Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Description de l’erreur :** Le nom du blob ou du fichier est trop long.

**Résolution suggérée :** L’objet blob ou les noms de fichiers dépassent la longueur maximale.

- Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
- Le nom d’objet blob ne doit pas dépasser 1 024 caractères.
- Supprimez ou renommez l’objet blob ou les fichiers afin que les noms de ne pas dépasser 1 024 caractères.

Pour plus d’informations, consultez les conventions d’affectation de noms Azure pour les noms d’objets blob et les noms de fichiers.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Description de l’erreur :** L’un des segments du nom de blob ou de fichier est trop long.

**Résolution suggérée :** Un des segments de chemin d’accès dans l’objet blob ou nom de fichier dépasse le nombre maximal de caractères. Un segment de chemin d’accès est la chaîne de caractères de séparation consécutifs, par exemple, la barre oblique /.

- Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
- Assurez-vous que le [noms d’objets blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) et [les noms de fichiers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) est conforme aux conventions d’affectation de noms Azure.

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Description de l’erreur :** La taille du fichier dépasse la taille de fichier maximale autorisée pour le chargement.

**Résolution suggérée :** L’objet blob ou la taille des fichiers dépasse la limite maximale autorisée pour le chargement.

- Sur le **Connect et copie** page de l’interface utilisateur web locale, téléchargez et examinez les fichiers de l’erreur.
- Assurez-vous que les tailles des objets blob et fichier ne dépassent pas les limites de taille d’objet Azure.

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Description de l’erreur :** L’alignement du blob ou du fichier est incorrect.

**Résolution suggérée :** Le partage de blob de page sur Data Box ou élevée de zone de données uniquement prend en charge les fichiers qui sont 512 octets alignés (par exemple, VHD/VHDX). Toutes les données copiées vers le partage d’objets blob de page sont téléchargées vers Azure en tant qu’objets BLOB de pages.

Supprimer toutes les données non-VHD/VHDX à partir du partage d’objets blob de page. Vous pouvez utiliser des partages pour l’objet blob de blocs ou de fichiers Azure pour les données génériques.

Pour plus d’informations, consultez [Page de vue d’ensemble d’objets BLOB](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Description de l’erreur :** Un type de fichier non pris en charge est présent dans un partage de disque géré. Les disques durs virtuels fixes uniquement sont autorisées.

**Résolution suggérée :**

- Assurez-vous que vous téléchargez uniquement les disques durs virtuels fixes pour créer des disques gérés.
- Les fichiers VHDX ou **dynamique** et **différenciation** disques durs virtuels ne sont pas pris en charge.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Description de l’erreur :** Un répertoire n’est pas autorisé dans un dossier existant pour les disques gérés. Les disques durs virtuels fixes uniquement sont autorisées dans ces dossiers.

**Résolution suggérée :** Pour les disques gérés, au sein de chaque partage, les trois dossiers suivants sont créés qui correspondent aux conteneurs dans votre compte de stockage : Disque SSD Premium, Standard HDD et SSD Standard. Ces dossiers correspondent au niveau de performances pour le disque géré.

- Assurez-vous que vous copiez vos données d’objet blob de page (disques durs virtuels) dans un de ces dossiers existants.
- Un dossier ou un répertoire n’est pas autorisée dans ces dossiers existants. Supprimez tous les dossiers que vous avez créé à l’intérieur des dossiers déjà existants.

Pour plus d’informations, consultez [copie vers des disques gérés](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [configuration système requise du stockage Blob de données boîte](data-box-system-requirements-rest.md).
