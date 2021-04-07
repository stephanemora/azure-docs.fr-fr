---
title: Sauvegarde continue avec la fonctionnalité de limite de restauration dans le temps dans Azure Cosmos DB
description: La fonctionnalité de limite de restauration dans le temps d’Azure Cosmos DB vous aide à récupérer des données suite à une opération accidentelle d’écriture ou de suppression ou bien à restaurer des données dans n’importe quelle région. En savoir plus sur la tarification et les limitations actuelles.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393137"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Sauvegarde continue avec la fonctionnalité de limite de restauration dans le temps (préversion) dans Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de limite de restauration dans le temps (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de limite de restauration dans le temps d’Azure Cosmos DB (préversion) vous aide dans plusieurs scénarios, par exemple :

* Pour effectuer une récupération suite à une opération d’écriture ou de suppression accidentelle dans un conteneur.
* Pour restaurer un compte, une base de données ou un conteneur supprimé.
* Pour effectuer une restauration dans n’importe quelle région (où les sauvegardes existent) à un instant dans le passé.

Azure Cosmos DB effectue la sauvegarde des données en arrière-plan sans consommer de débit (RU) provisionné supplémentaire et sans affecter les performances ou la disponibilité de votre base de données. Les sauvegardes continues sont effectuées dans chaque région où le compte existe. L’image suivante montre comment un conteneur avec la région d’écriture USA Ouest, les régions de lecture USA Est et USA Est 2 est sauvegardé dans un compte Stockage Blob Azure distant dans les régions respectives. Par défaut, chaque région stocke la sauvegarde dans des comptes de stockage localement redondants. Si l’option [Zones de disponibilité](high-availability.md#availability-zone-support) est activée pour la région, la sauvegarde est stockée dans des comptes de stockage redondants interzone.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Sauvegarde des données Azure Cosmos DB dans Stockage Blob Azure." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

La fenêtre de temps disponible pour la restauration (également appelée période de conservation) est la valeur inférieure parmi les deux éléments suivants :  *30 jours auparavant à partir de maintenant* ou *Jusqu’à l’heure de création de la ressource*. La limite de restauration dans le temps peut correspondre à n’importe quel horodatage durant la période de conservation.

En préversion publique, vous pouvez restaurer le contenu d’un compte Azure Cosmos DB pour API SQL ou MongoDB à un instant dans le passé vers un autre compte à l’aide du [portail Azure](continuous-backup-restore-portal.md), de l’[interface de ligne de commande Azure](continuous-backup-restore-command-line.md) (Azure CLI), d’[Azure PowerShell](continuous-backup-restore-powershell.md)ou d’[Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>Qu’est-ce qui est restauré ?

Dans un état stable, toutes les mutations effectuées sur le compte source (notamment les bases de données, conteneurs et éléments) sont sauvegardées de façon asynchrone dans les 100 secondes. Si le support de sauvegarde (c’est-à-dire Stockage Azure) est hors connexion ou indisponible, les mutations sont conservées localement jusqu’à ce que le support redevienne disponible, puis elles sont vidées pour éviter toute perte de fidélité des opérations qui peuvent être restaurées.

Vous pouvez choisir de restaurer le compte entier ou toute combinaison de conteneurs de débit provisionnés ou de bases de données de débit partagées. L’action de restauration restaure toutes les données et leurs propriétés d’index dans un nouveau compte. Le processus de restauration garantit que toutes les données restaurées dans un compte, une base de données ou un conteneur sont cohérentes jusqu’à l’heure de restauration spécifiée. La durée de la restauration dépend de la quantité de données qui doit être restaurée.

> [!NOTE]
> Avec le mode de sauvegarde continue, les sauvegardes sont effectuées dans chaque région où votre compte Azure Cosmos DB est disponible. Les sauvegardes effectuées pour chaque compte de région sont localement redondantes par défaut et redondantes interzone si la fonctionnalité [zone de disponibilité](high-availability.md#availability-zone-support) est activée pour cette région dans votre compte. L’action de restauration restaure toujours les données dans un nouveau compte.

## <a name="what-is-not-restored"></a>Qu’est-ce qui n’est pas restauré ?

Les configurations suivantes ne sont pas restaurées après la restauration à un instant dans le passé :

* Paramètres de pare-feu, de réseau virtuel, de point de terminaison privé.
* Paramètres de cohérence. Par défaut, le compte est restauré avec la cohérence de session.  
* Régions.
* Procédures stockées, déclencheurs, fonctions définies par l’utilisateur.

Vous pouvez ajouter ces configurations au compte restauré une fois la restauration terminée.

## <a name="restore-scenarios"></a>Scénarios de restauration

Voici quelques-uns des principaux scénarios qui sont traités par la fonctionnalité de limite de restauration dans le temps. Les scénarios [a] à [c] montrent comment déclencher une restauration si l’horodatage de la restauration est connu au préalable.
Toutefois, il existe des scénarios dans lesquels vous ne connaissez pas l’heure exacte de la suppression ou de l’altération accidentelle des données. Les scénarios [d] et [e] montrent comment _découvrir_ l’horodatage de la restauration à l’aide des nouvelles API de flux d’événements sur la base de données ou les conteneurs pouvant être restaurés.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Événements de cycle de vie avec horodatages pour un compte pouvant être restauré." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Restaurer le compte supprimé** : tous les comptes supprimés que vous pouvez restaurer sont visibles dans le volet **Restaurer**. Par exemple, si le *Compte A* est supprimé à l’horodatage T3. Dans ce cas, l’horodatage juste avant T3, l’emplacement, le nom du compte cible, le groupe de ressources et le nom du compte cible sont suffisants pour effectuer une restauration à partir du [portail Azure](continuous-backup-restore-portal.md#restore-deleted-account), de [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) ou de l’interface [CLI](continuous-backup-restore-command-line.md#trigger-restore).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Événements de cycle de vie avec horodatages pour une base de données et un conteneur pouvant être restaurés." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Restaurez les données d’un compte dans une région particulière** : par exemple, si le *Compte A »* existe dans deux régions *USA Est* et *USA Ouest* à l’horodatage T3. Si vous avez besoin d’une copie du compte A dans *USA Ouest*, vous pouvez effectuer une restauration à un instant dans le passé à partir du [portail Azure](continuous-backup-restore-portal.md), de [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) ou de l’[interface CLI](continuous-backup-restore-command-line.md#trigger-restore) avec USA Ouest comme localisation cible.

c. **Récupérer à partir d’une opération d’écriture ou de suppression accidentelle dans un conteneur avec un horodatage de restauration connu** : par exemple, si vous **savez** que le contenu du *Conteneur 1* dans la *Base de données 1* a été modifié accidentellement à l’horodatage T3. Vous pouvez effectuer une restauration à un instant dans le passé à partir du [portail Azure](continuous-backup-restore-portal.md#restore-live-account), de [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) ou de l’interface [CLI](continuous-backup-restore-command-line.md#trigger-restore) dans un autre compte à l’horodatage T3 pour récupérer l’état souhaité du conteneur.

d. **Restaurer un compte à un instant dans le passé avant la suppression accidentelle de la base de données** : dans le [portail Azure](continuous-backup-restore-portal.md#restore-live-account), vous pouvez utiliser le volet de flux d’événements pour déterminer quand une base de données a été supprimée et rechercher l’heure de la restauration. Avec l’interface [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) et [PowerShell](continuous-backup-restore-powershell.md#trigger-restore), vous pouvez également découvrir l’événement de suppression de la base de données en énumérant le flux des événements de la base de données, puis déclencher la commande de restauration avec les paramètres requis.

e. **Restaurez un compte à un instant antérieur dans le passé avant la suppression ou la modification accidentelle des propriétés du conteneur.** – Dans le [portail Azure](continuous-backup-restore-portal.md#restore-live-account), vous pouvez utiliser le volet de flux d’événements pour déterminer à quel moment un conteneur a été créé, modifié ou supprimé pour trouver l’heure de la restauration. Avec l’interface [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) et [PowerShell](continuous-backup-restore-powershell.md#trigger-restore) également, vous pouvez découvrir tous les événements de conteneur en énumérant le flux des événements de conteneur, puis déclencher la commande de restauration avec les paramètres requis.

## <a name="permissions"></a>Autorisations

Azure Cosmos DB vous permet d’isoler et de restreindre les autorisations de restauration pour le compte de sauvegarde continue à un rôle ou à un principal spécifique. Le propriétaire du compte peut déclencher une restauration et attribuer un rôle à d’autres principaux pour effectuer l’opération de restauration. Pour plus d’informations, consultez l’article [Autorisations](continuous-backup-restore-permissions.md).

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Tarifs

Les comptes Azure Cosmos DB pour lesquels la sauvegarde en continu est activée entraînent des frais mensuels supplémentaires pour *stocker la sauvegarde* et *restaurer vos données*. Le coût de restauration est ajouté chaque fois que l’opération de restauration est lancée. Si vous configurez un compte avec la sauvegarde continue, mais que vous ne restaurez pas les données, seul le coût de stockage de la sauvegarde est facturé.

L’exemple suivant se base sur le prix d’un compte Azure Cosmos déployé dans une région non gouvernementale aux États-Unis. Le tarif et le calcul varient en fonction de la région. Pour connaître les dernières informations tarifaires, consultez la [page des tarifs Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

* Tous les comptes activés avec la stratégie de sauvegarde continue impliquent un coût mensuel supplémentaire pour le stockage de sauvegarde calculé comme suit :

  0,20 USD/Go x taille des données en Go dans le compte x nombre de régions

* Chaque appel de restauration de l’API entraîne un coût unique. Les coûts dépendent de la taille de la restauration des données et sont calculés comme suit :

  0,15 USD/Go x taille des données en Go.

Par exemple, si vous avez 1 To de données dans deux régions :

* Le coût de stockage de sauvegarde est calculé comme suit : (1 000 x 0,20 x 2) = 400 USD par mois

* Le coût de restauration est calculé comme suit : (1 000 x 0,15) = 150 USD par restauration

## <a name="current-limitations-public-preview"></a>Limitations actuelles (préversion publique)

La fonctionnalité de limite de restauration dans le temps est actuellement disponible en préversion publique et présente les limitations suivantes :

* Seules les API Azure Cosmos DB pour SQL et MongoDB sont prises en charge pour la sauvegarde continue. Les API Cassandra, Table et Gremlin ne sont pas encore prises en charge.

* Un compte existant avec une stratégie de sauvegarde périodique par défaut ne peut pas être converti pour utiliser le mode de sauvegarde continue.

* Les régions constituées de clouds souverains Azure et du cloud Azure Government ne sont pas encore prises en charge.

* Les comptes avec des clés gérées par le client ne sont pas pris en charge pour l’utilisation de la sauvegarde continue.

* Les comptes d’écriture dans plusieurs régions ne sont pas pris en charge.

* Les comptes avec Synapse Link activé ne sont pas pris en charge.

* Le compte restauré est créé dans la même région que celle où se trouve votre compte source. Vous ne pouvez pas restaurer un compte dans une région où le compte source n’existait pas auparavant.

* La fenêtre de restauration n’est que de 30 jours et ne peut pas être modifiée.

* Les sauvegardes n’offrent pas automatiquement de géo-reprise d’activité après sinistre. Vous devez ajouter explicitement une autre région pour bénéficier d’une résilience pour le compte et la sauvegarde.

* Quand une restauration est en cours, vous ne devez ni modifier, ni supprimer les stratégies de gestion des identités et des accès (IAM) qui accordent les autorisations pour le compte. Ne modifiez pas non plus la configuration d’un réseau virtuel ou d’un pare-feu.

* Les comptes Azure Cosmos DB pour API SQL ou MongoDB qui créent un index unique après la création du conteneur ne sont pas pris en charge pour la sauvegarde continue. Seuls les conteneurs qui créent un index unique dans le cadre de la création du conteneur initial sont pris en charge. Pour les comptes MongoDB, vous créez un index unique à l’aide de [commandes d’extension](mongodb-custom-commands.md).

* La fonctionnalité de limite de restauration dans le temps utilise toujours un nouveau compte Azure Cosmos. La restauration vers un compte existant n’est pas prise en charge pour l’instant. Si vous souhaitez fournir des commentaires sur la restauration sur place, contactez l’équipe d’Azure Cosmos DB par le biais de [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou de votre responsable de compte.

* Toutes les nouvelles API exposées pour la liste `RestorableDatabaseAccount`, `RestorableSqlDatabases`, `RestorableSqlContainer`, `RestorableMongodbDatabase` et `RestorableMongodbCollection` peuvent faire l’objet de modifications quand la fonctionnalité est en préversion.

* Après la restauration, il est possible que l’index cohérent soit régénéré pour certaines collections. Vous pouvez vérifier l’état de l’opération de régénération à l’aide de la propriété [IndexTransformationProgress](how-to-manage-indexing-policy.md).

* Le processus de restauration restaure toutes les propriétés d’un conteneur, y compris sa configuration TTL. Il est donc possible que les données restaurées soient supprimées immédiatement si vous les avez configurées de cette manière. Pour éviter cette situation, l’horodatage de restauration doit être antérieur à l’ajout des propriétés TTL dans le conteneur.

## <a name="next-steps"></a>Étapes suivantes

* Configurez et gérez la sauvegarde continue en utilisant [Portail Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.
* [Modèle de ressource de mode de sauvegarde continue](continuous-backup-restore-resource-model.md)
