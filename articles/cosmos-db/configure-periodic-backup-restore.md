---
title: Configurer un compte Azure Cosmos DB avec une sauvegarde périodique
description: Cet article explique comment configurer des comptes Azure Cosmos DB avec une sauvegarde périodique, un intervalle de sauvegarde et la conservation des données. Il explique également comment contacter le support pour restaurer vos données.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69677ed419fa9bac2cbcb06c394c92f68d0b7777
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930926"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Configurer un compte Azure Cosmos DB avec une sauvegarde périodique
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB sauvegarde automatiquement vos données à intervalles réguliers. Les sauvegardes automatiques sont effectuées sans affecter les performances ou la disponibilité des opérations de base de données. Toutes les sauvegardes sont stockées séparément dans un service de stockage, et ces sauvegardes sont répliquées globalement pour garantir la résilience contre les sinistres régionaux. Avec Azure Cosmos DB, vos données et leurs sauvegardes sont rendues hautement redondantes et résilientes aux sinistres régionaux. Les étapes suivantes montrent comment Azure Cosmos DB sauvegarde les données :

* Azure Cosmos DB effectue automatiquement une sauvegarde complète de votre base de données toutes les 4 heures, quel que soit le moment. Par défaut, seules les deux dernières sauvegardes sont stockées. Si les intervalles par défaut sont insuffisants pour vos charges de travail, vous pouvez modifier l’intervalle de sauvegarde et la période de rétention à partir du portail Azure. Vous pouvez modifier la configuration de la sauvegarde pendant ou après la création du compte Azure Cosmos. En cas de suppression du conteneur ou de la base de données, Azure Cosmos DB conserve leurs captures instantanées pendant 30 jours.

* Azure Cosmos DB stocke ces sauvegardes dans Stockage Blob Azure, tandis que les données réelles résident localement dans Azure Cosmos DB.

* Pour garantir une latence faible, la capture instantanée de votre sauvegarde est stockée dans le Stockage Blob Azure, dans la région d’écriture actuelle (ou dans l’**une** des régions d’écriture si vous avez une configuration d’écriture multirégion). Pour assurer la résilience contre les sinistres régionaux, chaque capture instantanée de vos données de sauvegarde dans le stockage blob Azure est à nouveau répliqué vers une autre région par le biais du stockage géoredondant (GRS). La région vers laquelle la sauvegarde est répliquée dépend de votre région source et de la paire régionale associée à la région source. Pour plus d’informations, consultez la [liste des paires de régions Azure géoredondantes](../best-practices-availability-paired-regions.md). Vous ne pouvez pas accéder directement à cette sauvegarde. L’équipe Azure Cosmos DB restaurera votre sauvegarde lorsque vous le demanderez par le biais d’une demande de support.

  L’image suivante montre comment un conteneur Azure Cosmos avec les trois partitions physiques principales dans la région USA Ouest est sauvegardé dans un compte Stockage Blob Azure distant dans la région USA Ouest, puis répliqué dans la région USA Est :

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Sauvegardes complètes périodiques de toutes les entités Cosmos DB dans Stockage Azure GRS." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* Les sauvegardes sont effectuées sans affecter les performances ou la disponibilité de votre application. Azure Cosmos DB effectue la sauvegarde des données en arrière-plan sans consommer de débit (RU) approvisionné supplémentaire et sans affecter les performances ou la disponibilité de votre base de données.

> [!Note]
> Les comptes avec Synapse Link activé ne sont pas pris en charge.

## <a name="backup-storage-redundancy"></a><a id="backup-storage-redundancy"></a>Redondance du stockage de sauvegarde

Par défaut, Azure Cosmos DB stocke les données de sauvegarde en mode périodique dans un [stockage blob](../storage/common/storage-redundancy.md) géo-redondant qui est répliqué dans une [région jumelée](../best-practices-availability-paired-regions.md).  

Pour vous assurer que vos données de sauvegarde restent dans la même région que celle où votre compte Azure Cosmos DB est configuré, vous pouvez modifier le stockage de sauvegarde géo-redondant par défaut et configurer un stockage localement redondant ou redondant interzone. La redondance de stockage stocke toujours plusieurs copies de vos sauvegardes afin qu’elles soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant et des catastrophes naturelles majeures.

Les données de sauvegarde dans Azure Cosmos DB sont répliquées trois fois dans la région primaire. Vous pouvez configurer la redondance de stockage pour le mode de sauvegarde périodique au moment de la création du compte ou la mettre à jour pour un compte existant. Vous pouvez utiliser les trois options de redondance des données suivantes en mode de sauvegarde périodique :

* **Stockage de sauvegarde géo-redondant :** Cette option permet de copier vos données de façon asynchrone dans la région jumelée.

* **Stockage de sauvegarde redondant interzone** : Cette copie vos données de façon synchrone dans trois zones de disponibilité Azure au sein de la région primaire.

* **Stockage de sauvegarde redondant localement :** Cette option copie vos données de façon synchrone trois fois au sein d’un même emplacement physique dans la région primaire.

> [!NOTE]
> Pour le moment, le stockage redondant interzone est uniquement disponible dans [certaines régions](high-availability.md#availability-zone-support). En fonction de la région que vous sélectionnez, cette option ne sera pas disponible pour les comptes nouveaux ou existants.
>
> La mise à jour de la redondance du stockage de sauvegarde n’aura aucun impact sur la tarification du stockage de sauvegarde.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Modifier l’intervalle de sauvegarde et la période de rétention

Azure Cosmos DB effectue automatiquement une sauvegarde complète de vos données toutes les 4 heures, quel que soit le moment. Les deux dernières sauvegardes sont stockées. Il s’agit de la configuration par défaut offerte sans coût supplémentaire. Vous pouvez modifier l’intervalle de sauvegarde et la période de rétention par défaut pendant ou après la création du compte Azure Cosmos. La configuration de la sauvegarde est définie au niveau du compte Azure Cosmos, et vous devez la configurer sur chaque compte. Une fois que vous avez configuré les options de sauvegarde pour un compte, la configuration est appliquée à tous les conteneurs de ce compte. Actuellement, vous pouvez modifier les options de sauvegarde uniquement à partir du portail Azure.

Si vous avez accidentellement supprimé ou endommagé vos données, **avant de créer une demande de support pour restaurer les données, veillez à allonger la période de rétention des sauvegardes de votre compte à au moins sept jours. Il est préférable d’allonger la période de rétention dans les 8 heures qui suivent l’événement.** Ainsi, l’équipe Azure Cosmos DB dispose de suffisamment de temps pour restaurer votre compte.

### <a name="modify-backup-options-for-an-existing-account"></a>Modifier les options de sauvegarde pour un compte existant

Procédez comme suit pour modifier les options de sauvegarde par défaut pour un compte Azure Cosmos existant :

1. Connectez-vous au [portail Azure.](https://portal.azure.com/)
1. Accédez à votre compte Azure Cosmos et ouvrez le volet **Sauvegarder et restaurer**. Mettez à jour l’intervalle de sauvegarde et la période de rétention de sauvegarde selon les besoins.

   * **Intervalle de sauvegarde** : intervalle auquel Azure Cosmos DB tente d’effectuer une sauvegarde de vos données. La sauvegarde prend un certain temps et, dans certains cas, elle peut échouer en raison de dépendances en aval. Azure Cosmos DB tente d’effectuer une sauvegarde à l’intervalle configuré. Il n’est cependant nullement garanti que la sauvegarde s’achève dans cet intervalle de temps. Vous pouvez configurer cette valeur en heures ou minutes. L’intervalle de sauvegarde ne peut pas être inférieur à 1 heure et supérieur à 24 heures. Lorsque vous modifiez cet intervalle, le nouvel intervalle prend effet à partir de l’heure à laquelle la dernière sauvegarde a été effectuée.

   * **Rétention des sauvegardes** : période pendant laquelle chaque sauvegarde est conservée. Vous pouvez configurer cette valeur en heures ou en jours. La période de rétention minimale ne peut pas être inférieure à deux fois l’intervalle de sauvegarde (en heures), et ne peut pas être supérieure à 720 heures.

   * **Copies des données conservées** : par défaut, deux copies de sauvegarde de vos données sont offertes gratuitement. Des frais supplémentaires sont facturés si vous avez besoin de plus de deux copies. Pour connaître le prix exact des copies supplémentaires, consultez la section Stockage consommé dans la [page relative à la tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

   * **Redondance du stockage de sauvegarde** : choisissez l’option de redondance de stockage requise, consultez la section [Redondance du stockage de sauvegarde](#backup-storage-redundancy) pour les options disponibles. Par défaut, vos comptes de mode de sauvegarde périodique existants disposent d’un stockage géoredondant. Vous pouvez choisir un autre stockage, par exemple localement redondant, pour vous assurer que la sauvegarde n’est pas répliquée vers une autre région. Les modifications apportées à un compte existant sont appliquées uniquement aux sauvegardes ultérieures. Une fois la redondance de stockage de sauvegarde d’un compte existant mise à jour, il peut s’écouler jusqu’à deux fois l’intervalle de sauvegarde pour que les modifications prennent effet et **vous perdrez l’accès pour la restauration immédiate des anciennes sauvegardes.**

   > [!NOTE]
   > Vous devez avoir le rôle [Lecteur du compte Azure Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role) attribué au niveau de l’abonnement pour configurer la redondance du stockage de sauvegarde.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-existing-accounts.png" alt-text="Configurer l’intervalle de sauvegarde, la rétention et la redondance du stockage pour un compte Azure Cosmos DB existant." border="true":::

### <a name="modify-backup-options-for-a-new-account"></a>Modifier les options de sauvegarde pour un nouveau compte

Lors de la configuration d’un nouveau compte, sous l’onglet **Stratégie de sauvegarde**, sélectionnez la stratégie de sauvegarde **Périodique** _. La stratégie périodique vous permet de configurer l’intervalle de sauvegarde, la rétention de sauvegarde et la redondance de stockage de sauvegarde. Par exemple, vous pouvez choisir les options _ *stockage de sauvegarde localement redondant* * ou **Stockage de sauvegarde redondant interzone** pour empêcher la réplication des données de sauvegarde en dehors de votre région.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-options-new-accounts.png" alt-text="Configurer une stratégie de sauvegarde périodique ou continue pour de nouveaux comptes Azure Cosmos DB." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Demander une restauration des données à partir d’une sauvegarde

En cas de suppression accidentelle de votre base de données ou conteneur, vous pouvez [émettre un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [appeler le support Azure](https://azure.microsoft.com/support/options/) pour restaurer les données à partir de sauvegardes en ligne automatiques. Le support Azure est disponible uniquement pour certains plans, tels que **Standard**, **Développeur** et des plans plus élevés. Le support technique Azure n’est pas disponible avec un plan **De base**. Pour plus d’informations sur les différents plans de support technique, consultez la page [Plans de support Azure](https://azure.microsoft.com/support/plans/).

Pour restaurer une capture instantanée spécifique de la sauvegarde, Azure Cosmos DB exige que les données soient accessibles pendant le cycle de sauvegarde de cette capture instantanée.
Vous devez avoir les informations suivantes avant de demander une restauration :

* Votre ID d’abonnement.

* En fonction de la manière dont vos données ont été accidentellement supprimées ou modifiées, vous devez vous préparer à disposer d’informations supplémentaires. Nous vous recommandons d’avoir ces informations à portée de main à l’avance, afin de minimiser les aller-retours qui peuvent être préjudiciables dans certains cas d’urgence.

* Si l’intégralité du compte Azure Cosmos DB est supprimé, vous devez fournir le nom du compte supprimé. Si vous créez un autre compte avec le même nom que le compte supprimé, partagez-le avec l’équipe de support, car cela aide à identifier le compte correct. Nous vous recommandons d’émettre différents tickets de support pour chaque compte supprimé, car cela permet de réduire la confusion de l’état de la restauration.

* Si une ou plusieurs bases de données sont supprimées, vous devez fournir le compte Azure Cosmos ainsi que les noms des bases de données Azure Cosmos, et spécifier s’il existe une nouvelle base de données portant le même nom.

* Si un ou plusieurs conteneurs sont supprimés, vous devez fournir le nom du compte Azure Cosmos, les noms des bases de données et les noms des conteneurs. Spécifiez aussi s’il existe un conteneur portant le même nom.

* Si vous avez accidentellement supprimé ou endommagé vos données, vous devez contacter le [support Azure](https://azure.microsoft.com/support/options/) dans les 8 heures afin que l’équipe Azure Cosmos DB puisse vous aider à les restaurer à partir des sauvegardes. **Avant de créer une demande de support pour restaurer les données, veillez à [allonger la période de rétention des sauvegardes](#configure-backup-interval-retention) de votre compte à au moins sept jours. Il est préférable d’allonger la période de rétention dans les 8 heures qui suivent l’événement.** Ainsi, l’équipe de support Azure Cosmos DB disposera de suffisamment de temps pour restaurer votre compte.

En plus du nom du compte Azure Cosmos, des noms de bases de données et des noms de conteneurs, vous devez spécifier le moment dans le temps auquel les données peuvent être restaurées. Il est important d’être aussi précis que possible, afin de nous aider à déterminer les sauvegardes disponibles à ce moment-là. **Il est également important de spécifier l’heure au format UTC.**

La capture d’écran suivante illustre comment créer une demande de support pour un conteneur (collection/graphe/table) afin de restaurer des données à l’aide du portail Azure. Fournissez d’autres détails tels que le type de données, l’objectif de la restauration et le moment auquel les données ont été supprimées afin de nous aider à affecter une priorité à la demande.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Créer une demande de support de sauvegarde à l’aide du portail Azure." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Considérations relatives à la restauration des données à partir d’une sauvegarde

Vous risquez d’effacer ou de modifier accidentellement vos données dans l’un des cas suivants :  

* Vous supprimez le compte Azure Cosmos entier.

* Vous supprimez une ou plusieurs bases de données Azure Cosmos.

* Vous supprimez un ou plusieurs conteneurs Azure Cosmos.

* Vous supprimez ou modifiez des éléments Azure Cosmos (par exemple, des documents) dans un conteneur. Dans ce cas, on parle généralement d’« altération des données ».

* Une base de données d’offre partagée ou des conteneurs au sein d’une base de données d’offre partagée sont supprimés ou endommagés.

Azure Cosmos DB peut restaurer les données dans tous les scénarios ci-dessus. Lors de la restauration, un nouveau compte Azure Cosmos est créé pour contenir les données restaurées. Le nom du nouveau compte, s’il n’est pas spécifié, a le format `<Azure_Cosmos_account_original_name>-restored1`. Le dernier chiffre est incrémenté quand plusieurs tentatives de restauration sont effectuées. Vous ne pouvez pas restaurer les données sur un compte Azure Cosmos créé au préalable.

Quand vous supprimez accidentellement un compte Azure Cosmos, nous pouvons restaurer les données dans un nouveau compte portant le même nom, si le nom du compte n’est pas utilisé. Nous vous recommandons donc de ne pas recréer le compte après l’avoir supprimé. En effet, cela empêche les données restaurées d’utiliser le même nom, et complique la découverte du compte approprié à restaurer.

Quand vous supprimez accidentellement une base de données Azure Cosmos, nous pouvons restaurer la base de données entière ou un sous-ensemble des conteneurs à l’intérieur de celle-ci. Vous pouvez sélectionner des conteneurs spécifiques dans des bases de données et les restaurer sur un nouveau compte Azure Cosmos.

Quand vous supprimez ou modifiez accidentellement un ou plusieurs éléments d’un conteneur (altération des données), vous devez spécifier l’heure à laquelle effectuer la restauration. L’heure est importante en cas d’altération des données. Le conteneur étant en ligne, la sauvegarde est toujours en cours d’exécution. Par conséquent, si vous attendez au-delà de la période de rétention (de huit heures par défaut), les sauvegardes seront remplacées. Pour éviter le remplacement de la sauvegarde, allongez la période de rétention de la sauvegarde de votre compte à au moins sept jours. Il est préférable d’allonger la période de rétention dans les 8 heures qui suivent l’altération des données.

Si vous avez accidentellement supprimé ou endommagé vos données, vous devez contacter le [support Azure](https://azure.microsoft.com/support/options/) dans les 8 heures afin que l’équipe Azure Cosmos DB puisse vous aider à les restaurer à partir des sauvegardes. Ainsi, l’équipe de support Azure Cosmos DB disposera de suffisamment de temps pour restaurer votre compte.

> [!NOTE]
> Une fois les données restaurées, les fonctionnalités ou les paramètres source ne sont pas tous reportés sur le compte restauré. Les paramètres suivants ne sont pas reportés sur le nouveau compte :
> * Listes de contrôle d'accès VNET
> * Procédures stockées, déclencheurs et fonctions définies par l’utilisateur
> * Paramètres multirégion  

Si vous approvisionnez le débit au niveau de la base de données, le processus de sauvegarde et de restauration se produit au niveau de la base de données entière, non au niveau de conteneurs individuels. Dans ce cas, vous ne pouvez pas sélectionner un sous-ensemble de conteneurs à restaurer.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Autorisations requises pour modifier la rétention ou la restauration à partir du portail
Les principaux qui font partie du rôle [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), propriétaire ou contributeur sont autorisés à demander une restauration ou à modifier la période de rétention.

## <a name="understanding-costs-of-extra-backups"></a>Comprendre les coûts des sauvegardes supplémentaires
Deux sauvegardes sont effectuées gratuitement, et les sauvegardes supplémentaires sont facturées selon la tarification régionale pour le stockage de sauvegarde décrite dans [Tarification du stockage de sauvegarde](https://azure.microsoft.com/pricing/details/cosmos-db/). Par exemple, si la rétention de la sauvegarde est configurée sur 240 heures, c’est-à-dire 10 jours, avec un intervalle de sauvegarde de 24 heures. Cela implique 10 copies des données de sauvegarde. En supposant 1 To de données pour la région USA Ouest 2, le coût s’élève à 0,12 x 1 000 x 8 pour le stockage de sauvegarde d’un mois donné.

## <a name="options-to-manage-your-own-backups"></a>Options pour gérer vos propres sauvegardes

Avec les comptes d’API SQL Azure Cosmos DB, vous pouvez également tenir à jour vos propres sauvegardes en adoptant l’une des approches suivantes :

* Utiliser [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pour déplacer régulièrement les données vers un stockage de votre choix

* Utilisez le [flux de modification](change-feed.md) d’Azure Cosmos DB pour lire périodiquement les données pour des sauvegardes complètes ou des modifications incrémentielles, et les stocker dans votre propre stockage.

## <a name="post-restore-actions"></a>Actions de post-restauration

L’objectif principal de la restauration des données est de récupérer les données que vous avez supprimées ou modifiées accidentellement. Nous recommandons donc d’inspecter d’abord le contenu des données restaurées, afin de vous assurer qu’il correspond à vos attentes. Si tout semble correct, vous pouvez de nouveau migrer les données vers le compte principal. Bien qu’il soit possible d’utiliser le compte restauré comme nouveau compte actif, cette option n’est pas recommandée si vous avez des charges de travail de production. 

Après avoir restauré les données, vous recevez une notification concernant le nom du nouveau compte (il est généralement au format `<original-name>-restored1`) et le moment auquel le compte a été restauré. Le compte restauré aura le même débit provisionné et les mêmes stratégies d’indexation, et il se trouvera dans la même région que le compte d’origine. Un utilisateur administrateur ou coadministrateur de l’abonnement peut voir le compte restauré.

### <a name="migrate-data-to-the-original-account"></a>Migration des données vers le compte d’origine

Voici différentes méthodes pour migrer les données vers le compte d’origine :

* Vous utilisez l’[outil de migration de données Azure Cosmos DB](import-data.md).
* Vous utilisez Le service [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Vous utilisez le [flux de modification](change-feed.md) dans Azure Cosmos DB.
* Vous pouvez écrire votre propre code personnalisé.

Nous vous conseillons de supprimer le conteneur ou la base de données immédiatement après la migration des données. Si vous ne supprimez pas les bases de données ou les conteneurs restaurés, ils génèreront des coûts de stockage, de sortie et d’unités de requête.

## <a name="next-steps"></a>Étapes suivantes

* Pour effectuer une demande de restauration, [émettez un ticket à partir du portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Configurez et gérez la sauvegarde continue en utilisant [Portail Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
