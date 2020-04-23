---
title: azcopy copy| Microsoft Docs
description: Cet article fournit des informations de référence sur la commande azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253337"
---
# <a name="azcopy-copy"></a>azcopy copy

Copie les données sources vers un emplacement de destination.

## <a name="synopsis"></a>Synopsis

Copie les données sources vers un emplacement de destination. Les directions prises en charge sont les suivantes :

  - Local <-> Objet blob Azure (authentification SAP ou OAuth)
  - Local <-> Azure Files (authentification SAS pour répertoire/partage)
  - Local <-> ADLS Gen 2 (authentification SAP, OAuth ou SharedKey)
  - Objet blob Azure (SAS ou public) -> Objet blob Azure (authentification SAS ou OAuth)
  - Objet blob Azure (SAS ou public) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Objet blob Azure (authentification SAS ou OAuth)
  - AWS S3 (clé d’accès) -> Objet blob de blocs Azure (authentification SAP ou OAuth)

Pour plus d’informations, consultez les exemples.

## <a name="related-conceptual-articles"></a>Articles conceptuels associés

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)
- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
- [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avancé

AzCopy détecte automatiquement le type de contenu des fichiers lorsque vous les chargez à partir du disque local, en se basant sur l’extension du fichier ou sur son contenu (si aucune extension n’est spécifiée).

La table de recherche intégrée contient peu de données. Toutefois, sur UNIX, elle est augmentée par le ou les fichiers mime.types (si disponibles) du système local, qui peuvent porter les noms suivants :

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Sur Windows, les types MIME sont extraits du Registre. Cette fonctionnalité peut être désactivée à l’aide d’un indicateur. Pour plus d’informations, référez-vous à la section relative aux indicateurs.

Si vous définissez une variable d’environnement à l’aide de la ligne de commande, la variable sera lisible dans votre historique de ligne de commande. Vous pouvez supprimer de l’historique de la ligne de commande les variables qui contiennent des informations d’identification. Pour empêcher l’affichage des variables dans votre historique, vous pouvez utiliser un script qui invite l’utilisateur à entrer ses informations d’identification et qui définit la variable d’environnement.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemples

Charger un fichier à l’aide de l’authentification OAuth. Si vous ne vous êtes pas encore connecté à AzCopy, exécutez la commande azcopy login avant d’exécuter la commande suivante.

- azcopy cp "/path/to/file.txt" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]"

Comme ci-dessus, mais cette fois-ci, calcule également le hachage MD5 du contenu du fichier et l’enregistre en tant que propriété Content-MD5 de l’objet blob :

- azcopy cp "/path/to/file.txt" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]" --put-md5

Charger un seul fichier à l’aide d’un jeton SAS :

- azcopy cp "/path/to/file.txt" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]"

Charger un seul fichier à l’aide d’un jeton SAS et du piping (objets blob de blocs uniquement) :
  
- cat "/path/to/file.txt" | azcopy cp "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]"

Charger un répertoire entier à l’aide d’un jeton SAS :
  
- azcopy cp "/path/to/dir" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" --recursive=true

or

- azcopy cp "/path/to/dir" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" --recursive=true --put-md5

Charger un ensemble de fichiers à l’aide d’un jeton SAS et de caractères génériques (*) :

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]"

Charger des fichiers et des répertoires à l’aide d’un jeton SAS et de caractères génériques (*) :

- azcopy cp "/path/*foo/* bar*" "https://[compte].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" --recursive=true

Télécharger un seul fichier à l’aide de l’authentification OAuth. Si vous ne vous êtes pas encore connecté à AzCopy, exécutez la commande azcopy login avant d’exécuter la commande suivante.

- azcopy cp "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]" "/path/to/file.txt"

Télécharger un seul fichier à l’aide d’un jeton SAS :

- azcopy cp "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]" "/path/to/file.txt"

Télécharger un seul fichier à l’aide d’un jeton SAS, puis diriger la sortie vers un fichier (objets blob de blocs uniquement) :
  
- azcopy cp "https://[compte].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]" > "/path/to/file.txt"

Télécharger un répertoire entier à l’aide d’un jeton SAS :
  
- azcopy cp "https://[compte].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" "/path/to/dir" --recursive=true

Remarque sur l’utilisation d’un caractère générique (*) dans les URL :

Il existe seulement deux façons d’utiliser un caractère générique dans une URL. 

- Vous pouvez en utiliser un juste après la barre oblique finale (/) d’une URL. Cela copie tous les fichiers d’un répertoire directement vers la destination sans les placer dans un sous-répertoire.

- Vous pouvez également en utiliser un dans le nom d’un conteneur, tant que l’URL fait uniquement référence à un conteneur et non à un objet blob. Vous pouvez adopter cette approche pour obtenir des fichiers à partir d’un sous-ensemble de conteneurs.

Télécharger le contenu d’un répertoire sans copier le répertoire contenant lui-même.

- azcopy cp "https://[comptesrc].blob.core.windows.net/[conteneur]/[chemin/dossier]/*?[SAS]" "/path/to/dir"

Télécharger l’intégralité d’un compte de stockage.

- azcopy cp "https://[comptesrc].blob.core.windows.net/" "/path/to/dir" --recursive

Télécharger un sous-ensemble de conteneurs dans un compte de stockage en utilisant un caractère générique (*) dans le nom du conteneur.

- azcopy cp "https://[comptesrc].blob.core.windows.net/[nom*conteneur]" "/path/to/dir" --recursive

Copier un seul objet blob vers un autre objet blob à l’aide d’un jeton SAS.

- azcopy cp "https://[comptesrc].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]" "https://[comptedest].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]"

Copier un seul objet blob dans un autre objet blob à l’aide d’un jeton SAS et d’un jeton OAuth. Vous devez utiliser un jeton SAS à la fin de l’URL du compte source, mais le compte de destination n’en a pas besoin si vous vous connectez à AzCopy à l’aide de la commande azcopy login. 

- azcopy cp "https://[comptesrc].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]" "https://[comptedest].blob.core.windows.net/[conteneur]/[chemin/blob]"

Copier un répertoire virtuel d’objets blob vers un autre à l’aide d’un jeton SAS :

- azcopy cp "https://[comptesrc].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" "https://[comptedest].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" --recursive=true

Copier tous les conteneurs d’objets blob, les répertoires et les objets blob du compte de stockage vers un autre à l’aide d’un jeton SAS :

- azcopy cp "https://[comptesrc].blob.core.windows.net?[SAS]" "https://[comptedest].blob.core.windows.net?[SAS]" --recursive=true

Copier un seul objet dans le stockage d’objets blob à partir d’Amazon Web Services (AWS) S3 à l’aide d’une clé d’accès et d’un jeton SAS. Tout d’abord, définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source AWS S3.
  
- azcopy cp "https://s3.amazonaws.com/ [compartiment]/[objet]" "https://[comptedest].blob.core.windows.net/[conteneur]/[chemin/blob]?[SAS]"

Copier l’intégralité d’un répertoire dans le stockage d’objets blob à partir d’AWS S3 à l’aide d’une clé d’accès et d’un jeton SAS. Tout d’abord, définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source AWS S3.

- azcopy cp "https://s3.amazonaws.com/ [compartiment]/[dossier]" "https://[comptedest].blob.core.windows.net/[conteneur]/[chemin/répertoire]?[SAS]" --recursive=true

Reportez-vous à https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html pour mieux comprendre l’espace réservé [dossier].

Copier tous les compartiments dans le stockage d’objets blob à partir d’Amazon Web Services (AWS) à l’aide d’une clé d’accès et d’un jeton SAS. Tout d’abord, définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source AWS S3.

- azcopy cp "https://s3.amazonaws.com/ " "https://[comptedest].blob.core.windows.net?[SAS]" --recursive=true

Copier tous les compartiments dans le stockage d’objets blob à partir d’une région Amazon Web Services (AWS) à l’aide d’une clé d’accès et d’un jeton SAS. Tout d’abord, définissez les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source AWS S3.

- azcopy cp "https://s3- [région].amazonaws.com/" "https://[comptedest].blob.core.windows.net?[SAS]" --recursive=true

Copier un sous-ensemble de compartiments en utilisant un caractère générique (*) dans le nom du compartiment. Comme dans les exemples précédents, vous aurez besoin d’une clé d’accès et d’un jeton SAS. N’oubliez pas de définir les variables d’environnement AWS_ACCESS_KEY_ID et AWS_SECRET_ACCESS_KEY pour la source S3.

- azcopy cp "https://s3.amazonaws.com/ [nom*compartiment]/" "https://[comptedest].blob.core.windows.net?[SAS]" --recursive=true

## <a name="options"></a>Options

**--backup**                               Active le privilège SeBackupPrivilege de Windows pour les chargements, ou SeRestorePrivilege pour les téléchargements, afin de permettre à AzCopy de lire tous les fichiers, indépendamment de leurs autorisations de système de fichiers, et de restaurer toutes les autorisations. Nécessite que le compte qui exécute AzCopy dispose déjà de ces autorisations (par exemple, il doit disposer de droits d’administrateur ou être membre du groupe « Opérateurs de sauvegarde »). Cet indicateur ne fait qu’activer les privilèges dont le compte dispose déjà.

**--blob-type** string                     Définit le type d’objet blob au niveau de la destination. Utilisé pour charger des objets blob et lors de la copie de données d’un compte à l’autre (par défaut « Detect »). Les valeurs valides sont « Detect », « BlockBlob », « PageBlob » et « AppendBlob ». Lors de la copie entre comptes, la valeur « Detect » contraint AzCopy à utiliser le type de l’objet blob source pour déterminer le type de l’objet blob de destination. Lors du chargement d’un fichier, « Detect » détermine s’il s’agit d’un fichier VHD ou VHDX en fonction de l’extension de fichier. S’il s’agit d’un fichier VHD ou VHDX, AzCopy traite le fichier comme un objet blob de pages. (par défaut : « Detect »)

Chaîne **--block-blob-tier**               Vous permet de charger des objets blob de blocs au [niveau d'accès](../blobs/storage-blob-storage-tiers.md) de votre choix. (par défaut : « None »). Les valeurs valides sont « None », « Hot », « Cool » et « Archive ». Si la valeur « None » est transmise, ou si aucun niveau n'est transmis, l'objet blob hérite du niveau du compte de stockage.

**--block-size-mb** float                  Taille de bloc (spécifiée en Mio) utilisée lors du chargement dans Stockage Azure et le téléchargement à partir de Stockage Azure. La valeur par défaut est calculée automatiquement en fonction de la taille du fichier. Les fractions décimales sont autorisées (par exemple : 0,25).

**--cache-control** string                 Définir l’en-tête cache-control. Retourné au moment du téléchargement.

**--check-length**                         Vérifier la longueur d’un fichier sur la destination après le transfert. En cas d’incompatibilité entre la source et la destination, le transfert est marqué comme ayant échoué. (par défaut : true)

**--check-md5** string                     Spécifie la manière dont les hachages MD5 doivent être validés lors du téléchargement. Disponible uniquement lors du téléchargement. Options disponibles : NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (par défaut « FailIfDifferent »)

**--content-disposition** string           Définir l’en-tête content-disposition. Retourné au moment du téléchargement.

**--content-encoding** string           Définir l’en-tête content-encoding. Retourné au moment du téléchargement.

**--content-language** string           Définir l’en-tête content-language. Retourné au moment du téléchargement.

**--content-type** string                  Spécifie le type de contenu du fichier. Implique l’utilisation de no-guess-mime-type. Retourné au moment du téléchargement.

**--decompress**                           Décompresser automatiquement les fichiers lors du téléchargement, si content-encoding indique qu’ils sont compressés. Les valeurs de content-encoding prises en charge sont « gzip » et « deflate ». Les extensions de fichiers « .gz »/« .gzip » ou « .zz » ne sont pas nécessaires, mais seront supprimées si elles sont présentes.

**--exclude-attributes** string            (Windows uniquement) Exclut les fichiers dont les attributs correspondent à la liste d’attributs. Par exemple : A;S;R

**--exclude-blob-type** string             (Facultatif) Spécifie le type d’objet blob (BlockBlob/PageBlob/AppendBlob) à exclure lors de la copie d’objets blob à partir du conteneur ou du compte. L’utilisation de cet indicateur ne s’applique pas à la copie de données à partir d’un service non Azure. Si vous avez plusieurs objets blob, vous devez les séparer par un point-virgule (« ; »).

**--exclude-path** string                  Exclut ces chemins lors de la copie. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe de chemin relatif (Par exemple : myFolder;myFolder/subDirName/file.pdf.) En cas d’utilisation combinée avec la traversée de comptes, les chemins n’incluent pas le nom du conteneur.

**--exclude-pattern** string               Exclure ces fichiers lors de la copie. Cette option prend en charge les caractères génériques (*).

**--follow-symlinks**                      Suit les liens symboliques quand vous effectuez un chargement à partir d’un système de fichiers local.

**--from-to** string                       (Facultatif) Spécifie la combinaison source-destination. Par exemple : LocalBlob, BlobLocal, LocalBlobFS.

**-h, --help**                                 Aide pour la copie

**--include-attributes** string            (Windows uniquement) Inclut les fichiers dont les attributs correspondent à la liste d’attributs. Par exemple : A;S;R

**--include-path** string                  Inclut uniquement ces chemins lors de la copie. Cette option ne prend pas en charge les caractères génériques (*). Vérifie le préfixe de chemin relatif (Par exemple : myFolder;myFolder/subDirName/file.pdf.).

**--include-pattern** string                  Inclut uniquement ces fichiers lors de la copie. Cette option prend en charge les caractères génériques (*). Séparez les fichiers à l’aide d’un signe « ; ».

**--log-level** string                     Définir le niveau de détail pour le fichier journal. Niveaux disponibles : INFO (toutes les requêtes/réponses), WARNING (réponses lentes), ERROR (uniquement les échecs de requêtes) et NONE (aucun journal de sortie) (par défaut : « INFO »)

**--metadata** string                      Charge dans Stockage Azure avec ces paires clé-valeur en tant que métadonnées.

**--no-guess-mime-type**                   Empêche AzCopy de détecter le type de contenu (content-type) en fonction de l’extension ou du contenu du fichier.

**--overwrite** string                     Si cet indicateur a la valeur true, remplace les fichiers et objets blob qui sont en conflit dans la destination. Les valeurs possibles sont « true », « false », « ifSourceNewer » et « prompt ». (par défaut : « true »)

**--page-blob-tier** string               Charger un objet blob de pages dans Stockage Azure à l’aide de ce niveau d’objet blob. (par défaut : « None »).

**--preserve-last-modified-time**          Disponible uniquement quand la destination est un système de fichiers.

Chaîne **--preserve-smb-permissions**     False par défaut. Conserve les listes de contrôle d’accès (ACL) SMB entre les ressources prenant en charge SMB (Windows et Azure Files). Pour les téléchargements, vous devez également utiliser l’indicateur `--backup` pour restaurer les autorisations où le nouveau propriétaire n’est pas l’utilisateur qui exécute AzCopy. Cet indicateur s’applique aux fichiers et aux dossiers, à moins qu’un filtre de fichier uniquement soit spécifié (par exemple, `include-pattern`).

Chaîne **--preserve-smb-info**             False par défaut. Conserve les informations de propriété SMB (heure de la dernière écriture, heure de création, bits d’attribut) entre les ressources prenant en charge SMB (Windows et Azure Files). Seuls les bits d’attribut pris en charge par Azure Files sont transférés ; les autres sont ignorés. Cet indicateur s’applique aux fichiers et aux dossiers, à moins qu’un filtre de fichier uniquement soit spécifié (par exemple, include-pattern). Les informations transférées pour les dossiers sont les mêmes que pour les fichiers, à l’exception de l’heure de la dernière écriture, qui n’est jamais conservée pour les dossiers.

**--preserve-owner**                       N’a d’effet que lors du téléchargement de données et uniquement quand `--preserve-smb-permissions` est utilisé. Si la valeur est true (valeur par défaut), le groupe et le propriétaire des fichiers sont conservés dans les téléchargements. Si cet indicateur est défini sur false, `--preserve-smb-permissions` conserve toujours les listes de contrôle d’accès, mais le propriétaire et le groupe sont basés sur l’utilisateur qui exécute AzCopy.

**--put-md5**                             Créer un hachage MD5 de chaque fichier, puis enregistre le hachage en tant que propriété Content-MD5 de l’objet blob ou du fichier de destination (par défaut, le hachage n’est pas créé.) Disponible uniquement lors du chargement.

**--recursive**                            Examiner le contenu des sous-répertoires de manière récursive lors d’un chargement à partir du système de fichiers local.

**--s2s-detect-source-changed**           Vérifier si la source a changé après l’énumération.

**--s2s-handle-invalid-metadata** string   Spécifie la manière dont les clés de métadonnées non valides sont gérées. Options disponibles : ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid (par défaut : « ExcludeIfInvalid »)

**--s2s-preserve-access-tier**             Conserver le niveau d’accès lors d’une copie de service à service Pour vérifier si le compte de stockage de destination prend en charge la définition du niveau d’accès, consultez [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Si la définition du niveau d’accès n’est pas prise en charge, utilisez s2sPreserveAccessTier=false pour contourner la copie du niveau d’accès. (par défaut : true)

**--s2s-preserve-properties**             Conserver l’intégralité des propriétés lors d’une copie de service à service. Pour une source de fichier non unique Azure Files et AWS S3, l’opération de liste ne retourne pas les propriétés complètes des objets et des fichiers. Pour conserver l’intégralité des propriétés, AzCopy doit envoyer une requête supplémentaire par objet ou fichier. (par défaut : true)

## <a name="options-inherited-from-parent-commands"></a>Options héritées des commandes parentes

**--cap-mbps uint32**      Limite la vitesse de transfert, en mégabits par seconde. Par moment, le débit peut dépasser légèrement cette limite. Si cette option est définie sur zéro ou si elle est omise, le débit n’est pas limité.

**--output-type** string   Met en forme la sortie de la commande. Les formats possibles sont « text » et « JSON ». La valeur par défaut est « text ». (par défaut : « text »).

## <a name="see-also"></a>Voir aussi

- [azcopy](storage-ref-azcopy.md)
