---
title: Mise à niveau de Stockage Blob Azure vers Azure Data Lake Storage Gen2 | Microsoft Docs
description: La description s’affiche ici.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: 70f191a33eeb4d09ea8e4f14b17cd6ceadcec76e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003577"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Mise à niveau de Stockage Blob Azure avec les capacités d’Azure Data Lake Storage Gen2

Cet article vous aide à déverrouiller des capacités telles que la sécurité au niveau des fichiers et des répertoires et l’accélération des opérations. Ces capacités sont largement utilisées par les charges de travail d’analyse du Big Data et sont désignées collectivement Azure Data Lake Storage Gen2. Les capacités les plus populaires comprennent :

- Un débit, des opérations d’entrée/sortie par seconde (IOPS) et des limites de capacité de stockage plus élevés.

- Des opérations plus rapides (telles que les opérations de changement de nom), car vous pouvez opérer sur des URI de nœuds individuels.
 
- Un moteur de requête efficace qui transfère uniquement les données requises pour effectuer une opération donnée.

- La sécurité au niveau du conteneur, du répertoire et du fichier.

Pour en savoir plus, consultez [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

Cet article vous aide à évaluer l’impact sur les charges de travail, les applications, les coûts, les intégrations de service, les outils, les fonctionnalités et la documentation. Veillez à examiner attentivement ces impacts. Lorsque vous êtes prêt à mettre à niveau un compte, consultez ce guide étape par étape : [Mettre à niveau Stockage Blob Azure avec les capacités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md).

> [!IMPORTANT]
> La mise à niveau est irréversible. Il n’existe aucun moyen de rétablir la version antérieure de votre compte une fois que vous avez effectué la mise à niveau. Nous vous recommandons de valider votre mise à niveau dans un environnement de non production.

## <a name="impact-on-availability"></a>Impact sur la disponibilité

Veillez à prévoir un certain temps d’arrêt de votre compte pendant que le processus de mise à niveau se termine. Les opérations d’écriture sont désactivées lors de la mise à niveau de votre compte. Les opérations de lecture ne sont pas désactivées, mais nous vous recommandons vivement de suspendre les opérations de lecture, car elles peuvent déstabiliser le processus de mise à niveau.

## <a name="impact-on-workloads-and-applications"></a>Impact sur les charges de travail et les applications

Les API de blobs fonctionnent avec des comptes dotés d’un espace de noms hiérarchique. Ainsi, la plupart des applications qui interagissent avec votre compte en utilisant ces API continuent de fonctionner sans modification.

Pour obtenir une liste complète des problèmes et des solutions de contournement, consultez [Problèmes connus avec les API Stockage Blob](data-lake-storage-known-issues.md#blob-storage-apis).

Toutes les charges de travail Hadoop qui utilisent le pilote Windows Azure Storage Blob ou le pilote [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) doivent être modifiées pour utiliser le pilote [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html). Contrairement au pilote WASB qui effectue des demandes au point de terminaison du **service BLOB**, le pilote ABFS envoie des demandes au point de terminaison de **Data Lake Storage** de votre compte.

### <a name="data-lake-storage-endpoint"></a>Point de terminaison de Data Lake Storage

Votre compte mis à niveau aura un point de terminaison de Data Lake Storage. Vous pouvez trouver l’URL de ce point de terminaison dans le portail Azure en ouvrant la page **Propriétés** de votre compte.

> [!div class="mx-imgBorder"]
> ![Catégorie v2 universel](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

Vous n’êtes pas obligé de modifier vos applications et charges de travail existantes pour utiliser ce point de terminaison. L’[accès multiprotocole de Data Lake Storage](data-lake-storage-multi-protocol-access.md) vous permet d’utiliser soit le point de terminaison du service BLOB, soit le point de terminaison de Data Lake Storage pour interagir avec vos données. 

Les services et outils Azure (tels qu’AzCopy) peuvent utiliser le point de terminaison de Data Lake Storage pour interagir avec les données de votre compte de stockage. En outre, vous devrez utiliser ce nouveau point de terminaison pour toutes les opérations que vous effectuez à l’aide des [Kits de développement logiciel (SDK) Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md), des [commandes PowerShell](data-lake-storage-directory-file-acl-powershell.md) ou des [commandes Azure CLI](data-lake-storage-directory-file-acl-cli.md). 

### <a name="directories"></a>Répertoires

Un compte de stockage Blob qui n’a pas d’espace de noms hiérarchique organise les fichiers selon un paradigme plat, plutôt qu’un paradigme hiérarchique. Les blobs sont organisés en répertoires virtuels afin de simuler une structure de dossiers. Un répertoire virtuel fait partie du nom du blob et est indiqué par le caractère délimiteur. Comme un répertoire virtuel fait partie du nom du blob, il n’existe pas réellement en tant qu’objet indépendant.

Votre nouveau compte possède un espace de noms hiérarchique. Cela signifie que les répertoires ne sont pas virtuels. Il s’agit d’objets concrets et indépendants sur lesquels vous pouvez agir directement. Un répertoire peut exister sans contenir de fichiers. Lorsque vous supprimez un répertoire, tous les fichiers qu’il contient sont supprimés. Vous n’avez plus besoin de supprimer chaque blob individuel avant que le répertoire ne disparaisse. 

### <a name="blob-metadata"></a>Métadonnées d'objet blob

Avant la migration, les métadonnées du blob sont associées au nom du blob, ainsi qu’à son chemin d’accès virtuel complet. Après la migration, les métadonnées sont associées uniquement au blob. Le chemin d’accès virtuel au blob devient une collection de répertoires. Les métadonnées d’un blob ne sont appliquées à aucun de ces répertoires. 

### <a name="put-operations"></a>Opérations Put

Lorsque vous chargez un blob et que le chemin d’accès que vous spécifiez inclut un répertoire qui n’existe pas, l’opération crée ce répertoire, puis y ajoute un blob. Ce comportement est logique dans le contexte d’une structure hiérarchique de dossiers. Dans un compte de stockage Blob qui n’a pas d’espace de noms hiérarchique, l’opération ne crée pas de répertoire. En revanche, le nom du répertoire est ajouté à l’espace de noms du blob. 

### <a name="list-operations"></a>Lister les opérations

Une opération [List Blobs](/rest/api/storageservices/list-blobs) renvoie à la fois des répertoires et des fichiers. Chacun est répertorié séparément. Les répertoires apparaissent dans la liste sous forme de blobs de longueur nulle. Dans un compte de stockage Blob qui n’a pas d’espace de noms hiérarchique, une opération [List Blobs](/rest/api/storageservices/list-blobs) renvoie uniquement les blobs et non les répertoires. Si vous utilisez l’opération [Path - List](/rest/api/storageservices/datalakestoragegen2/path/list) de Data Lake Storage Gen2, les répertoires apparaîtront comme des entrées de répertoire et non comme des blobs de longueur nulle.

L’ordre de la liste est également différent. Les répertoires et les fichiers apparaissent dans l’ordre de *recherche en profondeur d’abord*. Un compte de stockage Blob qui n’a pas d’espace de noms hiérarchique liste les blobs par ordre *lexicographique*. 

### <a name="operations-to-rename-blobs"></a>Opérations de renommage des blobs

Renommer un blob est beaucoup plus efficace, car les applications clientes peuvent renommer un blob en une seule opération. Dans les comptes qui *n’ont pas* d’espace de noms hiérarchique, les outils et les applications doivent copier un blob, puis supprimer le blob source.  

> [!NOTE]
> Lorsque vous renommez un blob, l’heure de dernière modification du blob n’est pas mise à jour. Cela est dû au fait que le contenu du blob est inchangé. 

## <a name="impact-on-costs"></a>Impact sur les coûts

Les coûts de stockage ne sont pas concernés, mais les coûts de transaction le sont. Utilisez ces pages pour évaluer et comparer les coûts. 

- [Tarification d’objet blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/)

- [Tarification Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/)

Vous pouvez également utiliser l’option **Comptes de stockage** dans la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer l’impact des coûts après une mise à niveau. 

Outre les changements de tarification, tenez compte des économies associées aux capacités de Data Lake Storage Gen2. Le total global des coûts de possession diminue généralement en raison d’un débit plus élevé et d’opérations optimisées. Un débit plus élevé vous permet de transférer plus de données en moins de temps. Un espace de noms hiérarchique améliore l’efficacité des opérations.  

## <a name="impact-on-service-integrations"></a>Impact sur les intégrations de services

Si la plupart des intégrations de services Azure continueront à fonctionner après l’activation de ces capacités, certaines d’entre elles restent en préversion ou ne sont pas encore prises en charge. Consultez [Services Azure qui prennent en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md) pour comprendre la prise en charge actuelle des intégrations de services Azure avec Data Lake Storage Gen2.

## <a name="impact-on-tools-features-and-documentation"></a>Impact sur les outils, les fonctionnalités et la documentation

Après la mise à niveau, la façon dont vous interagissez avec certaines fonctionnalités va changer. Cette section décrit ces changements.

### <a name="blob-storage-feature-support"></a>Prise en charge des fonctionnalités Stockage Blob

Si la plupart des fonctionnalités de stockage Blob continueront à fonctionner après l’activation de ces capacités, certaines d’entre elles restent en préversion ou ne sont pas encore prises en charge. 

Consultez [Fonctionnalités de Stockage Blob disponibles dans Azure Data Lake Storage Gen2](./storage-feature-support-in-storage-accounts.md) pour comprendre la prise en charge actuelle des fonctionnalités de Stockage Blob avec Data Lake Storage Gen2. 

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Si vous activez [Journalisation Storage Analytics](../common/storage-analytics-logging.md), vous avez désormais la possibilité d’utiliser le format de journalisation de la version 2.0.  

Vous n’êtes pas obligé d’utiliser cette nouvelle version. Cependant, toutes les opérations appliquées au point de terminaison de Data Lake Storage sont enregistrées uniquement dans les journaux de la version 2.0. Certains services et outils que vous utilisez (par exemple, AzCopy) utiliseront ce point de terminaison pour effectuer des opérations sur votre compte. Pour vous assurer que vous capturez les informations de journalisation de toutes les activités, envisagez d’utiliser le format de journalisation de la version 2.0. 

### <a name="azure-lifecycle-management"></a>Gestion de cycle de vie Azure

Les stratégies qui déplacent ou suppriment tous les blobs d’un répertoire ne supprimeront pas le répertoire qui contient ces blobs avant le lendemain. En effet, le répertoire ne peut pas être supprimé tant que tous les blobs qui s’y trouvent n’ont pas été retirés. Le jour suivant, le répertoire sera supprimé. 

### <a name="event-grid"></a>Event Grid

Votre nouveau compte a deux points de terminaison : le point de terminaison de Data Lake Storage et le point de terminaison du service BLOB. Les services, les outils et les applications peuvent utiliser l’un ou l’autre de ces points de terminaison pour opérer sur vos données. Par conséquent, une réponse à un événement renvoyée par Event Grid peut indiquer l’un ou l’autre de ces deux points de terminaison dans le champ **url** qui décrit le blob concerné. 

Le code JSON suivant montre l’URL d’un blob qui apparaît dans la réponse à un événement lorsqu’un blob est créé en utilisant le point de terminaison du service BLOB.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

Le code JSON suivant montre l’URL d’un blob qui apparaît dans la réponse à un événement lorsqu’un blob est créé en utilisant le point de terminaison de Data Lake Storage.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

Si vos applications utilisent Event Grid, vous devrez peut-être les modifier pour en tenir compte. 

### <a name="storage-explorer"></a>Explorateur Stockage

Les boutons suivants n’apparaissent pas encore dans le ruban d’Explorateur Stockage Azure.

|Bouton|Motif|
|--|--|
|Copier l’URL|Pas encore implémenté|
|Gérer les instantanés|Pas encore implémenté|
|Annuler la suppression|Dépend des fonctionnalités de stockage Blob pas encore prises en charge avec Data Lake Storage Gen2 |


Les boutons suivants se comportent différemment dans votre nouveau compte.

|Bouton|Comportement du stockage Blob|Comportement de Data Lake Storage Gen2|
|---|---|---|
|Dossier|Le dossier est virtuel et disparaît si vous n’y ajoutez pas de fichiers. |Le dossier existe même si aucun fichier n’y est ajouté.| 
|Renommer|Entraîne une copie puis une suppression du blob source.|Renomme le même blob. Bien plus efficace.|

### <a name="documentation"></a>Documentation

Vous trouverez des conseils concernant l’utilisation des capacités de Data Lake Storage Gen2 ici : [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md). 

Rien n’a changé en ce qui concerne l’endroit où vous trouverez l’aide relative à toutes les fonctionnalités de stockage Blob existantes. L’aide est accessible ici : [Présentation de Stockage Blob Azure](storage-blobs-introduction.md). 

En passant d’un ensemble de contenu à l’autre, vous remarquerez quelques légères différences terminologiques. Par exemple, le contenu proposé dans Data Lake Storage Gen2 peut utiliser les termes *fichier* et *système de fichiers* au lieu des termes *blob* et *conteneur*. Les termes *fichier* et *système de fichiers* sont profondément enracinés dans le monde de l’analyse du Big Data, où Data Lake Storage a une longue histoire. Ces termes sont utilisés dans le contenu pour qu’il soit accessible à ces publics. Ces termes ne décrivent pas des *choses* distinctes.

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous êtes prêt à mettre à niveau votre compte de stockage pour inclure les capacités de Data Lake Storage Gen2, consultez ce guide étape par étape. 
> [!div class="nextstepaction"]
> [Mettre à niveau Stockage Blob Azure avec les capacités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2-how-to.md)