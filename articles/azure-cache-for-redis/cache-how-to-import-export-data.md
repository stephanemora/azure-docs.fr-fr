---
title: Importer et exporter des données dans le Cache Azure pour Redis
description: Découvrez comment importer et exporter des données vers et depuis un stockage d’objets blob à l’aide de votre Cache Azure Premium pour les instances Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: a877ee39c2d5a3760df50ebb3575793d1500de96
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110795529"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importer et exporter des données dans le Cache Azure pour Redis

L’importation et l’exportation sont des opérations de gestion des données d’Azure Cache pour Redis. Vous pouvez importer ou exporter des données depuis ou vers Azure Cache pour Redis, en important ou en exportant une capture instantanée de base de données Azure Cache pour Redis (RDB) à partir d’un cache Premium vers un objet blob dans un compte de stockage Azure.

- **Exportation** : vous pouvez exporter vos captures instantanées du cache Azure pour Redis RDB vers un objet blob de pages.
- **Importation** : vous pouvez importer vos captures instantanées du cache Azure pour Redis RDB à partir d’un objet blob de pages ou d’un objet blob de blocs.

L’Importation/Exportation vous permet de migrer entre différentes instances de cache Azure pour Redis ou de remplir le cache de données avant utilisation.

Cet article fournit un guide pour l’importation et l’exportation de données avec le Cache Azure pour Redis et répond aux questions fréquemment posées.

> [!IMPORTANT]
> L’importation/exportation est uniquement disponible pour les caches de [niveau Premium](cache-overview.md#service-tiers).
>
>

## <a name="import"></a>Importer

Utilisez l’importation pour récupérer les fichiers RDB compatibles Redis depuis un serveur Redis en cours d’exécution dans un environnement ou un cloud, y compris si Redis est exécuté sur Linux, Windows ou n’importe quel fournisseur de cloud, tel qu’Amazon Web Services. Importer des données est un moyen simple de créer un cache pré-rempli de données. Pendant le processus d’importation, le Cache Azure pour Redis charge les fichiers RDB du stockage Azure dans la mémoire, puis insère les clés dans le cache.

> [!NOTE]
> Avant de commencer l’opération d’importation, vérifiez que le ou les fichier(s) de votre base de données Redis (RDB) sont téléchargés vers des objets blob de pages ou de blocs dans le stockage Azure, dans les mêmes région et abonnement que votre instance de Cache Azure pour Redis. Pour plus d’informations, consultez l’article [Prise en main du stockage d’objets blob Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md). Si vous avez exporté votre fichier RDB à l’aide de la fonctionnalité [Exportation du cache Azure pour Redis](#export), votre fichier RDB est déjà stocké dans un objet blob de pages et est prêt pour l’importation.
>
>

1. Pour importer un ou plusieurs objets blob de cache exportés, [accédez à votre cache](cache-configure.md#configure-azure-cache-for-redis-settings) dans le portail Azure et sélectionnez **Importer des données** dans le **menu Ressource**.

    ![Importer des données](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Sélectionnez **Choisir un ou plusieurs objets blob** et sélectionnez le compte de stockage contenant les données à importer.

    ![Sélection d'un compte de stockage](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Sélectionnez le conteneur des données à importer.

    ![Sélection d’un conteneur](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Sélectionnez un ou plusieurs objets blob à importer en sélectionnant la zone située à gauche de leurs noms, puis en sélectionnant **Sélectionner**.

    ![Sélection d’objets blob](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Sélectionnez **Importer** pour commencer le processus d’importation.

   > [!IMPORTANT]
   > Pendant le processus d’importation, le cache n’est pas accessible par les clients de cache, et toutes les données existantes dans le cache sont supprimées.
   >
   >

    ![Importer](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Vous pouvez surveiller la progression de l’importation grâce aux notifications du portail Azure ou aux événements dans le [journal d’audit](../azure-resource-manager/management/view-activity-logs.md).

    ![Progression de l’importation](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exporter

L’exportation vous permet d’exporter les données stockées dans le Cache Azure pour Redis vers un ou plusieurs fichiers RDB compatibles. Vous pouvez utiliser cette fonctionnalité pour déplacer des données d’une instance de Cache Azure pour Redis à une autre, ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur la machine virtuelle qui héberge l’instance de serveur Azure Cache pour Redis. Ensuite, le fichier est chargé sur le compte de stockage choisi. Lorsque l’opération d’exportation se termine avec un état de réussite ou d’échec, le fichier temporaire est supprimé.

1. Pour exporter le contenu actuel du cache vers un espace de stockage, [accédez à votre cache](cache-configure.md#configure-azure-cache-for-redis-settings) dans le portail Azure, puis sélectionnez **Exporter des données** à partir du **Menu Ressource**.

    ![Dans le volet de navigation de contoso5premium, l’option Exporter des données de la liste Administration est mise en surbrillance.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Sélectionnez **Choisir un conteneur de stockage**, puis sélectionnez le compte de stockage souhaité. Le compte de stockage doit être situé dans les mêmes région et abonnement que votre cache.

   > [!IMPORTANT]
   > La fonctionnalité d’exportation s’utilise sur les objets blob de pages qui sont pris en charge par les comptes de stockage Resource Manager et classiques. L’exportation n’est pas prise en charge par les comptes de stockage d’objets blob pour l’instant. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../storage/common/storage-account-overview.md).
   >

    ![Compte de stockage](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Choisissez le conteneur d’objets blob souhaité, puis **Sélectionnez**. Pour utiliser un nouveau conteneur, sélectionnez d’abord **Ajouter un conteneur** pour l’ajouter, puis sélectionnez-le dans la liste.

    ![Dans Conteneurs pour contoso55, l’option Conteneur est mise en surbrillance. Il y a un conteneur dans la liste, cachesaves, qui est sélectionné et mis en surbrillance. L’option Sélection est sélectionnée et mise en surbrillance.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Tapez un **préfixe de nom d’objet blob** et sélectionnez **Exporter** pour lancer le processus d’exportation. Le préfixe de nom d’objet blob est utilisé pour préfixer les noms des fichiers générés par cette opération d’exportation.

    ![Exporter](./media/cache-how-to-import-export-data/cache-export-data.png)

    Vous pouvez surveiller la progression de l’exportation grâce aux notifications du portail Azure ou aux événements du [journal d’audit](../azure-resource-manager/management/view-activity-logs.md).

    ![Exportation des données terminées](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Les caches restent disponibles pendant le processus d’exportation.

## <a name="importexport-faq"></a>FAQ sur l’Importation/Exportation

Cette section contient les questions fréquemment posées sur la fonctionnalité d’Importation/Exportation.

- [Quels niveaux de tarification permettent d’utiliser l’Importation/Exportation ?](#what-pricing-tiers-can-use-importexport)
- [Puis-je importer des données à partir de n’importe quel serveur Redis ?](#can-i-import-data-from-any-redis-server)
- [Quelles versions RDB puis-je importer ?](#what-rdb-versions-can-i-import)
- [Mon cache est-il disponible pendant une opération d’Importation/Exportation ?](#is-my-cache-available-during-an-importexport-operation)
- [Puis-je utiliser l’Importation/Exportation avec le cluster Redis ?](#can-i-use-importexport-with-redis-cluster)
- [Comment l’importation/exportation fonctionne avec un paramètre de base de données personnalisé ?](#how-does-importexport-work-with-a-custom-databases-setting)
- [En quoi l’Importation/Exportation est-elle différente de la persistance Redis ?](#how-is-importexport-different-from-redis-persistence)
- [Puis-je automatiser l’Importation/Exportation à l’aide de PowerShell, de CLI ou d’autres clients de gestion ?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
- [J’ai reçu une erreur de délai d’attente pendant mon opération d’importation/exportation. Qu’est-ce que cela signifie ?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
- [J’ai obtenu une erreur en exportant mes données vers un stockage d’objets blob Azure. Que s’est-il passé ?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Quels niveaux de tarification permettent d’utiliser l’Importation/Exportation ?

L’Importation/Exportation n’est disponible que dans le niveau de tarification Premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Puis-je importer des données à partir de n’importe quel serveur Redis ?

Oui, vous pouvez importer des données exportées depuis des instances Azure Cache pour Redis, et importer également des fichiers RDB depuis n’importe quel serveur Redis s’exécutant sur un environnement ou un cloud. Les environnements comprennent Linux, Windows ou des fournisseurs cloud, tels que Amazon Web Services. Pour importer ces données, chargez le fichier RDB à partir du serveur Redis de votre choix, dans un objet blob de pages ou de blocs d’un compte de stockage Azure. Ensuite, importez-le dans votre instance Premium Azure Cache pour Redis. Vous pouvez par exemple exporter les données de votre cache de production, et les importer dans un cache qui est utilisé dans le cadre d’un environnement de préproduction, à des fins de test ou de migration.

> [!IMPORTANT]
> Pour importer correctement des données exportées à partir de serveurs Redis autres que le Cache Azure pour Redis lors de l’utilisation d’un objet blob de pages, la taille de l’objet blob de pages doit être alignée sur une limite de 512 octets. Pour obtenir un exemple de code permettant d’exécuter un remplissage d’octets, consultez [Sample page blob upload](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Quelles versions RDB puis-je importer ?

Cache Azure pour Redis prend en charge l’importation RDB via la version 7 de RDB.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Mon cache est-il disponible pendant une opération d’Importation/Exportation ?

- **Exporter** - Les caches restent disponibles et vous pouvez continuer à utiliser votre cache pendant une opération d’exportation.
- **Importer** - Les caches deviennent indisponibles au démarrage d’une opération d’importation, puis redeviennent disponibles lorsqu’elle est terminée.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Puis-je utiliser l’Importation/Exportation avec le cluster Redis ?

Oui, et vous pouvez importer/exporter des données entre un cache en cluster et un cache hors cluster. Depuis que le cluster Redis [ne prend en charge que la base de données 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), toutes les données de bases de données différentes de 0 ne sont pas importées. Lorsque vous importez des données d’un cache en cluster, les clés sont redistribuées entre les partitions du cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Comment l’importation/exportation fonctionne avec un paramètre de base de données personnalisé ?

Certains niveaux tarifaires sont associés à des [limites de bases de données](cache-configure.md#databases) différentes. Certains aspects doivent donc être pris en compte lors d’une importation si vous avez configuré une valeur personnalisée pour le paramètre `databases` lors de la création du cache.

- Lorsque vous procédez à une importation vers un niveau tarifaire avec une priorité inférieure à `databases` par rapport au niveau à partir duquel vous effectuez l’exportation :
  - Si vous utilisez le nombre par défaut de `databases`, c’est-à-dire 16 pour tous les niveaux tarifaires, vous ne perdrez aucune donnée.
  - Si vous utilisez un nombre personnalisé de `databases` qui se trouve dans les limites du niveau tarifaire vers lequel vous effectuez l’importation, vous ne perdrez aucune donnée.
  - Si vos données exportées contenaient des données d’une base de données dépassant les limites du nouveau niveau, les données de telles bases de données plus élevées ne sont pas importées.

### <a name="how-is-importexport-different-from-redis-persistence"></a>En quoi l’Importation/Exportation est-elle différente de la persistance Redis ?

La persistance du Cache Azure pour Redis vous permet de conserver dans le stockage Azure les données stockées dans Redis. Quand la persistance est configurée, Azure Cache pour Redis conserve un instantané des données du cache dans un format binaire Redis, sur le disque, selon une fréquence de sauvegarde configurable. Si un événement catastrophique se produit et provoque la désactivation du cache principal et du réplica, les données du cache sont automatiquement rétablies à l’aide de l’instantané le plus récent. Pour plus d’informations, consultez [Comment configurer la persistance des données pour un Cache Azure pour Redis Premium](cache-how-to-premium-persistence.md).

L’Importation/Exportation vous permet d’importer ou d’exporter des données vers ou depuis le Cache Azure pour Redis. Ces opérations ne configurent pas la sauvegarde ni la restauration à l’aide de la persistance Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Puis-je automatiser l’Importation/Exportation à l’aide de PowerShell, de CLI ou d’autres clients de gestion ?

Oui, pour les instructions PowerShell, consultez les articles [pour importer un cache Azure pour Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) et [pour exporter un cache Azure pour Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>J’ai reçu une erreur de délai d’attente pendant mon opération d’Importation/Exportation. Qu’est-ce que cela signifie ?

Sur la gauche, si vous restez sur **Importer des données** ou **Exporter des données** pendant plus de 15 minutes avant de démarrer l’opération, vous recevez une erreur avec un message similaire à ce qui suit :

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Pour corriger cette erreur, démarrez l’importation ou l’exportation avant que les 15 minutes ne se soient écoulées.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>J’ai obtenu une erreur en exportant mes données vers un stockage d’objets blob Azure. Que s’est-il passé ?

L’exportation ne fonctionne qu’avec les fichiers RDB stockés sous la forme d’objets blob de pages. Les autres types d’objets blob ne sont pas pris en charge pour l’instant, entre autres les comptes de stockage d’objets blob de niveaux chaud et froid. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités d’Azure Cache pour Redis.

- [Niveaux de service Azure Cache pour Redis](cache-overview.md#service-tiers)
