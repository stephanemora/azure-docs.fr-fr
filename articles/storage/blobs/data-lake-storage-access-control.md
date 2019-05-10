---
title: Vue d’ensemble du contrôle d’accès dans Azure Data Lake Storage Gen2 | Microsoft Docs
description: Comprendre le fonctionnement du contrôle d’accès dans Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 0b8139f11f937ddae30e25f4153e35287289a4d1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233958"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Contrôle d’accès dans Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implémente un modèle de contrôle d’accès qui prend en charge le contrôle d’accès en fonction du rôle Azure (RBAC) et de listes de contrôle d’accès POSIX (ACL). Cet article présente les notions de base du modèle de contrôle d’accès pour Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

RBAC utilise les attributions de rôles appliquer efficacement les jeux d’autorisations *principaux de sécurité*. Un *principal de sécurité* est un objet qui représente un utilisateur, un groupe, un principal de service ou une identité gérée qui est définie dans Azure Active Directory (AD) qui demande l’accès aux ressources Azure.

En règle générale, ces ressources Azure sont limitées aux ressources de niveau supérieur (par exemple : Comptes de stockage Azure). Avec le Stockage Azure, et par conséquent Azure Data Lake Storage Gen2, ce mécanisme a été étendu à la ressource du système de fichiers.

Pour savoir comment affecter des rôles aux entités de sécurité dans l’étendue de votre compte de stockage, consultez [authentifier l’accès à Azure d’objets BLOB et files d’attente à l’aide d’Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>L’impact des affectations de rôle sur les listes de contrôle d’accès au niveau fichier et répertoire

À l’aide d’affectations de rôle RBAC est un mécanisme puissant pour contrôler les autorisations d’accès, mais il est un mécanisme très grossièrement plus précise par rapport aux ACL. La granularité la plus fine que gère la fonction RBAC se trouve au niveau du système de fichiers, et cela sera évalué en priorité par rapport aux ACL. Par conséquent, si vous attribuez un rôle à un principal de sécurité dans l’étendue d’un système de fichiers, cette entité de sécurité a le niveau d’autorisation associé à ce rôle pour tous les répertoires et fichiers dans ce système de fichiers, indépendamment des attributions d’ACL.

Lorsqu’un principal de sécurité est autorisations RBAC données via un [rôle intégré](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues), ou via un rôle personnalisé, ces autorisations sont évaluées en premier lors de l’autorisation d’une demande. Si l’opération demandée n’est autorisée par les attributions de RBAC de l’entité de sécurité, l’autorisation est immédiatement résolu et aucune supplémentaire ACL vérifications sont effectuées. Vous pouvez également, si l’entité de sécurité n’a pas d’une affectation RBAC ou opération de la demande ne correspond pas à l’autorisation, vérifier des listes ACL est effectuées pour déterminer si l’entité de sécurité est autorisée à effectuer l’opération demandée.

> [!NOTE]
> Si l’entité de sécurité a reçu l’attribution de rôle intégré propriétaire des données de stockage Blob, le principal de sécurité est considéré comme un *super utilisateur* et disposent d’un accès complet à toutes les opérations de mutation, y compris le paramètre est le propriétaire d’un répertoire ou fichier ainsi que les ACL pour les répertoires et fichiers pour lesquels ils ne sont pas le propriétaire. L’accès de super utilisateur constitue la seule manière autorisée de modifier le propriétaire d’une ressource.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Authentification de clé partagée et de la Signature d’accès partagé (SAP)

Azure Data Lake Storage Gen2 prend en charge les méthodes de la clé partagée et SAS pour l’authentification. Une caractéristique de ces méthodes d’authentification est qu’aucune identité n’est associée à l’appelant et par conséquent autorisation basée sur les autorisations du principal de sécurité ne peut pas être effectuée.

Dans le cas des clés partagées, l’appelant bénéficie effectivement d’un accès de super utilisateur, autrement dit d’un accès complet à toutes les opérations sur toutes les ressources, y compris la définition du propriétaire et la modification des ACL.

Les jetons SAP incluent les autorisations accordées dans le jeton. Les autorisations incluses dans le jeton SAP sont appliquées à toutes les décisions d’autorisation, mais aucune vérification ACL supplémentaire n’est effectuée.

## <a name="access-control-lists-on-files-and-directories"></a>Listes de contrôle d’accès sur les fichiers et répertoires

Vous pouvez associer une entité de sécurité à un niveau d’accès pour les fichiers et répertoires. Ces associations sont capturées dans un *liste de contrôle d’accès (ACL)*. Chaque fichier et répertoire dans votre compte de stockage a une liste de contrôle d’accès.

Si un rôle est attribué à un principal de sécurité au niveau de compte de stockage, vous pouvez utiliser des listes de contrôle d’accès à accorder à que ce principal de sécurité un accès élevé aux répertoires et fichiers spécifiques.

Vous ne pouvez pas utiliser les listes de contrôle d’accès pour fournir un niveau d’accès qui est inférieur à un niveau accordé par une attribution de rôle. Par exemple, si vous assignez le [contributeur aux données stockage Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview) répertorie de rôle à une entité, vous ne pouvez pas utiliser le contrôle d’accès de sécurité pour empêcher l’écriture dans un répertoire de ce principal de sécurité.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Définir des autorisations au niveau fichier et répertoire à l’aide de listes de contrôle d’accès

Pour définir des autorisations au niveau des fichiers et de répertoires, consultez les articles suivants :

|Si vous souhaitez utiliser cet outil :    |Consultez cet article :    |
|--------|-----------|
|Azure Storage Explorer    |[Définir des autorisations au niveau de fichiers et de répertoires à l'aide de l'Explorateur Stockage Azure avec Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|API REST    |[Chemin d’accès - mise à jour](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Si l’entité de sécurité est un *service* principal, il est important d’utiliser l’ID d’objet du principal du service et pas l’ID d’objet de l’inscription d’application connexes. Pour accéder l’ID d’objet du principal du service, ouvrez l’interface CLI Azure, puis utiliser cette commande : `az ad sp show --id <Your App ID> --query objectId`. Veillez à remplacer le `<Your App ID>` espace réservé avec l’ID d’application de l’inscription de votre application.

### <a name="types-of-access-control-lists"></a>Types de listes de contrôle d’accès

Il existe deux types de listes de contrôle d’accès : *ACL d’accès* et *ACL par défaut*.

Les ACL d’accès contrôlent l’accès à un objet. Les fichiers et les répertoires ont tous des ACL d’accès.

ACL par défaut est des modèles d’ACL associé à un répertoire qui déterminent l’accès (ACL) pour tous les éléments enfants qui sont créés sous ce répertoire. Les fichiers n’ont pas d’ACL par défaut.

Les ACL d’accès et les ACL par défaut ont la même structure.

Les ACL d’accès et les ACL par défaut ont la même structure.

> [!NOTE]
> La modification de l’ACL par défaut d’un parent n’affecte pas l’ACL d’accès ni l’ACL par défaut des éléments enfants qui existent déjà.

### <a name="levels-of-permission"></a>Niveaux d’autorisation

Les autorisations sur un objet de système de fichiers sont **Lecture**, **Écriture** et **Exécution**. Elles peuvent être utilisées sur les fichiers et les répertoires comme l’indique le tableau ci-dessous :

|            |    Fichier     |   Répertoire |
|------------|-------------|----------|
| **Lecture (R)** | Permet de lire le contenu d’un fichier | Requiert les autorisations **Lecture** et **Exécution** pour répertorier le contenu du répertoire |
| **Écriture (W)** | Permet d’écrire ou d’ajouter du contenu dans un fichier | Requiert les autorisations **Écriture** et **Exécution** pour créer des éléments enfants dans un répertoire |
| **Exécution (X)** | Cela ne signifie rien dans le contexte de Data Lake Storage Gen2 | Requise pour parcourir les éléments enfants d’un répertoire |

#### <a name="short-forms-for-permissions"></a>Formes abrégées des autorisations

**RWX** correspond à **Lecture + Écriture + Exécution**. Il existe une forme numérique plus condensée dans laquelle **Lecture = 4**, **Écriture = 2** et **Exécution = 1**. Les autorisations sont représentées par la somme de ces chiffres. Voici quelques exemples.

| Forme numérique | Forme abrégée |      Signification     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lecture + Écriture + Exécution |
| 5.            | `R-X`        | Lecture + Exécution         |
| 4            | `R--`        | Lire                   |
| 0            | `---`        | Aucune autorisation         |

#### <a name="permissions-inheritance"></a>Héritage des autorisations

Dans le modèle POSIX utilisé par Data Lake Storage Gen2, les autorisations d’un élément sont stockées sur l’élément lui-même. En d’autres termes, les autorisations sur un élément ne peuvent pas être héritées à partir des éléments parents si les autorisations sont définies après la création de l’élément enfant. Les autorisations sont héritées uniquement si les autorisations par défaut ont été définies sur les éléments parents avant la création des éléments enfants.

### <a name="common-scenarios-related-to-permissions"></a>Scénarios courants liés aux autorisations

Le tableau suivant répertorie quelques scénarios courants pour vous aider à comprendre quelles autorisations sont nécessaires pour effectuer certaines opérations sur un compte de stockage.

|    Opération             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Lire Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Ajouter à Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Supprimer Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Créer Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Répertorier /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Répertorier /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Répertorier /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Les autorisations d’écriture ne sont pas nécessaires pour supprimer le fichier, tant que les deux conditions précédentes sont remplies.

### <a name="users-and-identities"></a>Utilisateurs et identités

Chaque fichier et répertoire dispose d’autorisations distinctes pour ces identités :

- L’utilisateur propriétaire
- Le groupe propriétaire
- Les utilisateurs nommés
- Les groupes nommés
- Les principaux de service nommés
- Nommé identités gérées
- Tous les autres utilisateurs

Les identités des utilisateurs et des groupes sont des identités Azure Active Directory (Azure AD). C’est le cas sauf indication contraire, un *utilisateur*, dans le contexte de Data Lake Storage Gen2, peut faire référence à un utilisateur Azure AD, identité du principal, managée ou groupe de sécurité de service.

#### <a name="the-owning-user"></a>L’utilisateur propriétaire

L’utilisateur qui a créé l’élément est automatiquement l’utilisateur propriétaire de l’élément. Les utilisateurs propriétaires peuvent :

* modifier les autorisations de leurs fichiers ;
* modifier le groupe propriétaire d’un fichier détenu, tant que l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> L’utilisateur propriétaire *ne peut pas* modifier l’utilisateur propriétaire d’un fichier ou d’un répertoire. Seuls les super utilisateurs peuvent modifier l’utilisateur propriétaire d’un fichier ou d’un répertoire.

#### <a name="the-owning-group"></a>Le groupe propriétaire

Dans les ACL POSIX, chaque utilisateur est associé à un *groupe principal*. Par exemple, l’utilisateur « Alice » peut appartenir au groupe « finance ». Alice peut appartenir à plusieurs groupes, mais un groupe est toujours désigné comme son groupe principal. Dans POSIX, lorsqu’Alice crée un fichier, son groupe principal est défini comme groupe propriétaire de ce fichier (en l’occurrence, « finance »). Sinon, le groupe propriétaire se comporte comme pour les autorisations assignées à d’autres utilisateurs/groupes.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Affectation du groupe propriétaire pour un nouveau fichier ou répertoire

* **Cas n° 1** : Répertoire racine "/". Ce répertoire est créé lors de la création d’un système de fichiers Data Lake Storage Gen2. Dans ce cas, le groupe propriétaire est celui de l’utilisateur qui a créé le système de fichiers si l’opération est réalisée avec OAuth. Si le système de fichiers est créé à l’aide de la clé partagée, une SAP de compte ou une SAP de Service, le propriétaire et le groupe propriétaire sont définis sur **$superuser**.
* **Cas 2** (tous les autres cas) : lorsqu’un nouvel élément est créé, le groupe propriétaire est copié à partir du répertoire parent.

##### <a name="changing-the-owning-group"></a>Modification du groupe propriétaire

Le groupe propriétaire peut être modifié par :
* un super utilisateur ;
* l’utilisateur propriétaire, si l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> Le groupe propriétaire ne peut pas modifier les ACL d’un fichier ou d’un répertoire.  Alors que le groupe propriétaire est défini à l’utilisateur qui a créé le compte dans le cas le répertoire racine, **cas 1** ci-dessus, un seul compte d’utilisateur n’est pas valide pour accorder des autorisations via le groupe propriétaire. Si applicable, vous pouvez assigner cette autorisation à un groupe d’utilisateurs valide.

### <a name="access-check-algorithm"></a>Algorithme de vérification des accès

Le pseudo-code suivant représente l’algorithme de vérification d’accès pour les comptes de stockage.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>Le masque

Comme illustré dans l’algorithme de vérification des accès, le masque limite l’accès pour les utilisateurs nommés, le groupe propriétaire et les groupes nommés.  

> [!NOTE]
> Pour un nouveau système de fichiers Data Lake Storage Gen2, la valeur par défaut du masque d’ACL du répertoire racine ("/") est de 750 pour les répertoires et 640 pour les fichiers. Les fichiers ne reçoivent pas le bit X, car il est sans importance pour les fichiers dans un système de stockage uniquement.
>
> Le masque peut être spécifié par appel. Ainsi, différents systèmes de consommation, tels que les clusters, peuvent être associés à différents masques plus efficaces pour leurs opérations de fichier. Si un masque est spécifié sur une requête donnée, il remplace complètement le masque par défaut.

#### <a name="the-sticky-bit"></a>Le sticky bit

Le sticky bit est une fonctionnalité avancée d’un système de fichiers POSIX. Dans le contexte de Data Lake Storage Gen2, il est peu probable que le sticky bit soit nécessaire. En résumé, si le sticky bit est activé sur un répertoire, un élément enfant peut uniquement être supprimé ou renommé par l’utilisateur propriétaire de l’élément enfant.

Le sticky bit n’est pas affiché dans le portail Azure.

### <a name="default-permissions-on-new-files-and-directories"></a>Autorisations par défaut sur les nouveaux fichiers et répertoires

Lorsqu’un nouveau fichier ou répertoire est créé dans un dossier existant, l’ACL par défaut sur le répertoire parent détermine :

- L’ACL par défaut et l’ACL d’accès d’un répertoire enfant.
- L’ACL d’accès d’un fichier enfant (les fichiers n’ont pas d’ACL par défaut).

#### <a name="umask"></a>umask

Quand vous créez un fichier ou répertoire, l’umask est utilisé pour modifier la façon dont les ACL par défaut sont définies sur l’élément enfant. L’umask est une valeur 9 bits sur les répertoires parents qui contient une valeur RWX pour **l’utilisateur propriétaire**, le **groupe propriétaire** et **d’autres rôles**.

L’umask pour Azure Data Lake Storage Gen2 est une valeur constante définie sur 007. Cette valeur se traduit par :

| Composant umask     | Forme numérique | Forme abrégée | Signification |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Pour l’utilisateur propriétaire, copiez l’ACL par défaut du parent dans l’ACL d’accès de l’enfant | 
| umask.owning_group  |    0         |   `---`      | Pour le groupe propriétaire, copiez l’ACL par défaut du parent dans l’ACL d’accès de l’enfant | 
| umask.other         |    7         |   `RWX`      | Pour d’autres rôles, supprimez toutes les autorisations de l’ACL d’accès de l’enfant |

La valeur umask utilisée par Azure Data Lake Storage Gen2 signifie effectivement que la valeur pour d’**autres** rôles n’est jamais transmise par défaut sur les nouveaux enfants, quelle que soit l’indication de l’ACL par défaut. 

Le pseudocode suivant montre comment l’umask est appliqué lors de la création des ACL pour un élément enfant.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Questions les plus fréquentes sur les ACL dans Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>Dois-je activer la prise en charge des ACL ?

Non. Contrôle d’accès via les ACL est activé pour un compte de stockage tant que le Namespace (HNS hiérarchique) fonctionnalité est activée.

Si cette fonctionnalité est désactivée, les règles d’autorisation Azure RBAC s’appliquent toujours.

### <a name="what-is-the-best-way-to-apply-acls"></a>Quelle est la meilleure façon d’appliquer des ACL ?

Utilisez toujours les groupes de sécurité Azure AD comme principal affecté dans les listes ACL. Résistez à la possibilité d’attribuer directement des utilisateurs ou des principaux de service individuels. L’utilisation de cette structure vous permettra d’ajouter et de supprimer des utilisateurs ou des principaux de service sans devoir réappliquer les ACL sur la structure de répertoires dans son ensemble. Au lieu de cela, vous devez simplement les ajouter ou les supprimer du groupe de sécurité Azure AD approprié. N’oubliez pas que les ACL ne sont pas héritées. Par conséquent, la réapplication des ACL requiert la mise à jour de ces dernières sur chaque fichier et sous-répertoire. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quelles sont les autorisations nécessaires pour supprimer de manière récursive un répertoire et son contenu ?

- L’appelant dispose des autorisations de « super utilisateur »,

Ou

- L’utilisateur doit disposer des autorisations Écriture + Exécution sur le répertoire parent.
- L’utilisateur doit disposer des autorisations Lecture + Écriture + Exécution sur le répertoire à supprimer et sur tous ses sous-répertoires.

> [!NOTE]
> L’autorisation Écriture n’est pas nécessaire pour supprimer des fichiers situés dans des répertoires. En outre, le répertoire racine « / » ne peut jamais être supprimé.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Qui est le propriétaire d’un fichier ou d’un répertoire ?

Le créateur d’un fichier ou d’un répertoire en devient le propriétaire. Dans le cas du répertoire racine, il s’agit de l’identité de l’utilisateur ayant créé le système de fichiers.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Quel groupe est propriétaire d’un fichier ou d’un répertoire lors de sa création ?

Le groupe propriétaire est copié à partir du groupe propriétaire du répertoire dans lequel le fichier ou le répertoire est créé.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Je suis l’utilisateur propriétaire d’un fichier, mais je n’ai pas les autorisations RWX dont j’ai besoin. Que faire ?

L’utilisateur propriétaire peut modifier les autorisations du fichier pour s’accorder les autorisations RWX dont il a besoin.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Pourquoi vois-je parfois des GUID dans les ACL ?

Un GUID s’affiche si l’entrée représente un utilisateur et que ce dernier n’existe plus dans Azure AD. Cela se produit généralement lorsque l’utilisateur a quitté l’entreprise ou que son compte a été supprimé dans Azure AD. En outre, les principaux de service et les groupes de sécurité ne sont identifiés par aucun UPN. Par conséquent, ils sont représentés par leur attribut OID (un GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Comment définir ACL correctement pour un service principal ?

Lorsque vous définissez des ACL pour les principaux de service, il est important d’utiliser l’ID d’objet (OID) de la *principal du service* pour l’inscription d’application que vous avez créé. Il est important de noter que les applications inscrites ont un principal de service distinct dans spécifique au locataire Azure AD. Applications inscrites ont un OID qui est visible dans le portail Azure, mais la *principal du service* a un autre OID (différent).

Pour obtenir l’OID du principal du service qui correspond à une inscription d’application, vous pouvez utiliser la `az ad sp show` commande. Spécifiez l’ID d’Application comme paramètre. Voici un exemple sur l’obtention de l’OID du principal du service qui correspond à une inscription d’application avec l’ID d’application = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Dans Azure CLI, exécutez la commande suivante :

`az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>`

Si vous avez l’OID correct pour le principal du service, accédez à l’Explorateur de stockage **gérer l’accès** page pour ajouter l’OID et attribuer des autorisations appropriées pour l’OID. Veillez à sélectionner **enregistrer**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 prend-il en charge l’héritage des ACL ?

Les affectations RBAC Azure peuvent être héritées. Les affectations passent de l’abonnement, du groupe de ressources et des ressources du compte de stockage à la ressource du système de fichiers.

Les ACL ne peuvent pas être héritées. Cependant, les ACL par défaut peuvent être utilisées pour définir les ACL des sous-répertoires et fichiers enfants nouvellement créés sous le répertoire parent. 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Comment en savoir plus sur le modèle de contrôle d’accès POSIX ?

* [Listes de contrôle d’accès (ACL) POSIX sur Linux](https://www.linux.com/news/posix-acls-linux)
* [HDFS Permission Guide (Guide des autorisations HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Forum aux questions POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [ACL POSIX sous Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL using access control lists on Linux (ACL utilisant des listes de contrôle d’accès sous Linux)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Voir aussi

* [Présentation d’Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
