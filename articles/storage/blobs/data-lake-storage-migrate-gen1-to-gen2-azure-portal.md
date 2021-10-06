---
title: Migrer avec le portail Azure (Data Lake Storage Gen1 vers Gen2)
description: Vous pouvez simplifier la tâche de migration entre Azure Data Lake Storage Gen1 et Azure Data Lake Storage Gen2 en utilisant le portail Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3d78ba06112fce8dff64f2091e434d8f502634d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662739"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2-by-using-the-azure-portal-preview"></a>Migrer d’Azure Data Lake Storage Gen1 vers Azure Data Lake Storage Gen2 à l’aide du portail Azure (préversion)

L’utilisation du portail Azure vous permet de réduire le nombre d’étapes nécessaires à la migration. Les données et les métadonnées (comme les horodatages et les listes de contrôle d’accès) sont automatiquement déplacées vers votre compte Gen2. Si vous effectuez une migration complète, vous n’aurez pas besoin de pointer vos charges de travail vers Gen2, car les demandes sont redirigées automatiquement.

> [!IMPORTANT]
> La migration de Gen1 vers Gen2 à l’aide du portail Azure est actuellement en préversion.
> Pour connaître les conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou plus généralement non encore en disponibilité générale, consultez [l’Avenant aux conditions d’utilisation des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article vous guide dans les tâches suivantes :

- Étape 1 : S’inscrire à la préversion

- Étape 2 : Créer un compte de stockage comprenant des fonctionnalités Gen2

- Étape 3 : Vérifier les attributions de rôles RBAC

- Étape 4 : Effectuer la migration

- Étape 5. Tester vos applications

- Étape 6 : Finaliser la migration

Vous devez lire les instructions générales sur la migration de Gen1 vers Gen2. Pour plus d’informations, consultez [Migrer Azure Data Lake Storage de Gen1 vers Gen2](data-lake-storage-migrate-gen1-to-gen2.md).

> [!NOTE]
> Pour faciliter la lecture de cet article, le terme *Gen1* est utilisé pour faire référence à Azure Data Lake Storage Gen1 tandis que le terme *Gen2* est utilisé pour faire référence à Azure Data Lake Storage Gen2.

## <a name="enroll-in-the-preview"></a>S’inscrire à la préversion

Pour vous inscrire à la préversion, voir [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4SeyCfCfrtBlHWFupvoz_BUMEFNQzBSQTE0OU1aM0hXMDlBNEwzVTYyRy4u&wdLOR=cBC075B83-9324-4399-B94E-05A919D007C9). Une fois inscrit, créez un compte de stockage comprenant des fonctionnalités Gen2 (voir la section suivante).

Microsoft vous contactera dans un délai de sept jours environ afin de vérifier que votre nouveau compte est activé pour la migration.

## <a name="create-a-storage-account-with-gen2-capabilities"></a>Créer un compte de stockage comprenant des fonctionnalités Gen2

Azure Data Lake Storage Gen2 n’est ni un compte de stockage dédié ni un type de service. Il s’agit d’un ensemble de fonctionnalités que vous pouvez obtenir en activant la fonctionnalité **Espace de noms hiérarchique** dans un compte de stockage Azure. Pour créer un compte comprenant des fonctionnalités Gen2, consultez [Créer un compte de stockage à utiliser avec Data Lake Storage Gen2](create-data-lake-storage-account.md).

Lorsque vous créez le compte, veillez à configurer les paramètres avec les valeurs suivantes.

| Paramètre | Valeur |
|--|--|
| **Nom du compte de stockage** | Nom de votre choix. Ce nom ne doit pas nécessairement correspondre à celui de votre compte Gen1, et peut se trouver dans l’abonnement de votre choix. |
| **Lieu** | La même région que celle utilisée par le compte Data Lake Storage Gen1 |
| **Réplication** | LRS ou ZRS |
| **Version TLS minimale** | 1.0 |
| **NFS v3** | Désactivé |
| **Espace de noms hiérarchique** | Activé |

> [!NOTE]
> L’outil de migration du portail Azure ne déplace pas les paramètres de compte. Par conséquent, une fois que vous avez créé le compte, vous devez configurer manuellement les paramètres tels que le chiffrement, les pare-feu réseau ou la protection des données.

## <a name="verify-rbac-role-assignments"></a>Vérifier les attributions de rôles RBAC

Pour Gen2, vérifiez que le rôle [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a été attribué à votre identité d’utilisateur Azure Active Directory (Azure AD) dans l’étendue du compte de stockage, du groupe de ressources parent ou de l’abonnement.

Pour Gen1, vérifiez que le rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) a été attribué à votre identité Azure AD dans l’étendue du compte Gen1, du groupe de ressources parent ou de l’abonnement.

## <a name="perform-the-migration"></a>Effectuer la migration

Avant de commencer, déterminez si vous souhaitez copier uniquement les données ou effectuer une migration complète.

Si vous copiez uniquement les données, les deux comptes resteront actifs une fois la migration terminée. Pendant la migration, votre compte Gen1 passe en lecture seule. Vous pouvez ensuite mettre à jour vos charges de travail de calcul pour qu’elles utilisent votre nouveau compte de stockage Gen2. Une fois que vous avez vérifié que vos applications et charges de travail fonctionnent comme prévu, vous pouvez désactiver le compte Gen1. Microsoft recommande cette option.

Si vous effectuez une migration complète, les données seront copiées de Gen1 vers Gen2. Ensuite, votre URI Gen1 sera redirigé vers votre URI Gen2. Une fois la migration terminée, vous n’aurez plus accès à votre compte Gen1, et toutes les demandes Gen1 seront redirigées vers votre compte Gen2. Votre compte Gen1 ne sera plus disponible pour les opérations de données, et les données de votre compte Gen1 ne vous seront plus facturées. Vous pourrez supprimer votre compte Gen1 une fois que vos pipelines s’exécuteront dans votre compte Gen2.

> [!NOTE]
> Gen2 ne prend pas en charge les applications Azure Data Lake Analytics. Si vous en avez, veillez à les déplacer vers Azure Synapse Analytics ou une autre charge de travail prise en charge avant d’effectuer la migration de Gen1 vers Gen2.

### <a name="option-1-copy-data-from-gen1-to-gen2"></a>Option 1 : Copier les données de Gen1 vers Gen2

1. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à votre compte Data Lake Storage Gen1 et affichez la vue d’ensemble du compte.

3. Sélectionnez le bouton **Migrer les données**.

   > [!div class="mx-imgBorder"]
   > ![Bouton pour effectuer la migration](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. Sélectionnez **Copier les données vers un nouveau compte Gen2**.

   > [!div class="mx-imgBorder"]
   > ![Option de copie des données](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-data-option.png)

5. Cochez la case pour accorder votre consentement à Microsoft pour effectuer la migration des données. Ensuite, cliquez sur le bouton **Appliquer**.

   > [!div class="mx-imgBorder"]
   > ![Case à cocher pour accorder son consentement](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   Pendant la migration de vos données, votre compte Gen1 passe en lecture seule et votre compte Gen2 est désactivé. Une fois la migration terminée, vous pouvez lire et écrire des données dans les deux comptes.

   Vous pouvez interrompre la migration à tout moment en sélectionnant le bouton **Arrêter la migration**.

   > [!div class="mx-imgBorder"]
   > ![Option pour arrêter la migration](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

### <a name="option-2-perform-a-complete-migration"></a>Option 2 : Effectuer une migration complète

1. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez à votre compte Data Lake Storage Gen1 et affichez la vue d’ensemble du compte.

3. Sélectionnez le bouton **Migrer les données**.

   > [!div class="mx-imgBorder"]
   > ![Bouton Migrer](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. Sélectionnez **Effectuer la migration vers un nouveau compte Gen2**.

   > [!div class="mx-imgBorder"]
   > ![Option de migration complète](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-complete-option.png)

5. Cochez la case pour accorder votre consentement à Microsoft pour effectuer la migration des données. Ensuite, cliquez sur le bouton **Appliquer**.

   > [!div class="mx-imgBorder"]
   > ![Case à cocher pour le consentement](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   - Pendant la migration de vos données, votre compte Gen1 passe en lecture seule et votre compte Gen2 est désactivé.
   - Pendant la redirection de l’URI Gen1, les deux comptes sont désactivés.
   - Une fois la migration terminée, votre compte Gen1 est désactivé, et vous pouvez lire et écrire des données dans votre compte Gen2.

   Vous pouvez interrompre la migration à tout moment avant la redirection de l’URI, en sélectionnant le bouton **Arrêter la migration**.

   > [!div class="mx-imgBorder"]
   > ![Bouton pour arrêter la migration](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

## <a name="migrate-workloads-and-applications"></a>Effectuer la migration des charges de travail et des applications

1. Configurez les [services de vos charges de travail](./data-lake-storage-supported-azure-services.md) pour qu’ils pointent vers votre point de terminaison Gen2.

2. Mettez à jour les applications pour qu’elles utilisent les API Gen2. Consultez les guides suivants :

   | Environnement | Article |
   |--------|-----------|
   |Explorateur de stockage Azure |[Utiliser l’Explorateur Stockage Azure pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
   |.NET |[Utiliser .NET pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
   |Java|[Utiliser Java pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
   |Python|[Utiliser Python pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
   |JavaScript (Node.js)|[Utiliser JavaScript SDK dans Node.js pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-javascript.md)|
   |API REST |[API REST Azure Data Lake Storage](/rest/api/storageservices/data-lake-storage-gen2)|

3. Mettez à jour les scripts pour qu’ils utilisent les [applets de commande PowerShell](data-lake-storage-directory-file-acl-powershell.md) Data Lake Storage Gen2 et les [commandes Azure CLI](data-lake-storage-directory-file-acl-cli.md).

4. Recherchez les références URI contenant la chaîne `adl://` dans les fichiers de code, les notebooks Databricks, les fichiers HQL Apache Hive ou tout autre fichier utilisé dans le cadre de vos charges de travail. Remplacez ces références par l’[URI formaté Gen2](data-lake-storage-introduction-abfs-uri.md) de votre nouveau compte de stockage. Par exemple, l’URI Gen1 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` peut devenir `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`.

## <a name="leverage-the-gen1-compatibility-layer-optional"></a>Utiliser la couche de compatibilité Gen1 (facultatif)

Microsoft fournit une compatibilité des applications avec des fonctionnalités limitées qui permettent à vos applications de continuer à utiliser les API Gen1 pour interagir avec les données de votre compte Gen2. La couche de compatibilité s’exécute sur le serveur. Vous n’avez donc rien à installer.

> [!IMPORTANT]
> Microsoft ne recommande pas cette fonctionnalité en remplacement de la migration de vos charges de travail et applications. La prise en charge de la couche de compatibilité Gen1 se terminera à la mise hors service de Gen1, le 29 février 2024.

Pour rencontrer le moins de problèmes possible avec la couche de compatibilité, vérifiez que vos SDK Gen1 utilisent les versions suivantes (ou une version ultérieure).

   | Langage | Version du SDK |
   |--|--|
   | **.NET** | [2.3.9](https://github.com/Azure/azure-data-lake-store-net/blob/master/CHANGELOG.md) |
   | **Java** | [1.1.21](https://github.com/Azure/azure-data-lake-store-java/blob/master/CHANGES.md) |
   | **Python** | [0.0 51](https://github.com/Azure/azure-data-lake-store-python/blob/master/HISTORY.rst) |

Les fonctionnalités suivantes ne sont pas prises en charge dans Gen2. Elles ne le sont donc pas non plus dans la couche de compatibilité.

- Option de l’API ListStatus permettant d’utiliser ListBefore avec une entrée.

- API ListStatus avec plus de 4 000 fichiers sans jeton de continuation.

- Encodage des blocs pour les opérations d’ajout.

- Tous les appels d’API qui utilisent https://management.azure.com/ comme audience du jeton Azure Active Directory (Azure AD).

- Noms de fichiers ou de répertoires avec uniquement des espaces ou des tabulations, se terminant par un `.`, contenant un `:` ou comportant plusieurs barres obliques consécutives (`//`).

## <a name="frequently-asked-questions"></a>Forum aux questions

#### <a name="how-much-does-the-data-migration-cost"></a>Combien coûte la migration des données ?

Pendant la migration des données, le stockage de données et les transactions du compte Gen1 vous seront facturés. Si vous choisissez de copier uniquement les données, le stockage des données et les transactions des deux comptes vous seront facturés une fois la migration terminée. Pour éviter que le compte Gen1 ne vous soit facturé, vous devez le supprimer. Supprimez le compte Gen1 une fois que vous avez terminé la mise à jour de vos applications. Si vous choisissez d’effectuer une migration complète, seuls le stockage de données et les transactions du compte Gen2 vous seront facturés après la migration.

#### <a name="after-the-migration-completes-can-i-choose-to-go-back-to-using-the-gen1-account"></a>Une fois la migration terminée, est-ce que je peux choisir de revenir au compte Gen1 ?

Cette opération n’est pas prise en charge. Une fois la migration terminée, les données de votre compte Gen1 ne seront plus accessibles. Vous pouvez toutefois continuer à voir le compte Gen1 dans le portail Azure et, lorsque vous serez prêt, vous pourrez le supprimer.

#### <a name="i-would-like-to-enable-geo-redundant-storage-grs-on-the-gen2-account-how-do-i-do-that"></a>Je souhaite activer le stockage géoredondant (GRS) dans le compte Gen2. Comment procéder ?

Une fois la migration terminée, aussi bien avec l’option « Copier les données » qu’avec l’option « Migration complète », vous pouvez remplacer l’option de redondance par GRS tant que vous n’envisagez pas d’utiliser la couche de compatibilité des applications. La compatibilité des applications ne fonctionnera pas dans les comptes qui utilisent la redondance GRS.

#### <a name="gen1-doesnt-have-containers-and-gen2-has-them---what-should-i-expect"></a>Gen1 ne comprend pas de conteneurs alors que Gen2 en contient. À quoi dois-je m’attendre ?

Lorsque nous copions les données vers votre compte Gen2, nous créons automatiquement un conteneur nommé `Gen1`. Si vous choisissez de copier uniquement les données, vous pourrez renommer ce conteneur une fois que la copie des données sera terminée. Si vous effectuez une migration complète et que vous envisagez d’utiliser la couche de compatibilité des applications, vous devez éviter de modifier le nom du conteneur. Lorsque vous ne souhaiterez plus utiliser la couche de compatibilité, vous pourrez modifier le nom du conteneur.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la migration en général. Pour plus d’informations, consultez [Migrer Azure Data Lake Storage de Gen1 vers Gen2](data-lake-storage-migrate-gen1-to-gen2.md).
