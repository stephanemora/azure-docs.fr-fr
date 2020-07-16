---
title: Guide pratique pour restaurer des données Azure Cosmos DB à partir d’une sauvegarde
description: Cet article explique comment restaurer des données Azure Cosmos DB à partir d’une sauvegarde et comment contacter le support Azure pour restaurer les données. Il décrit aussi les étapes à effectuer une fois les données restaurées.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a0075f9b4fc3ff919d4db4bd440a5435d711c83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261730"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Restaurer des données à partir d'une sauvegarde dans Azure Cosmos DB 

En cas de suppression accidentelle de votre base de données ou conteneur, vous pouvez [émettre un ticket de support]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [appeler le support technique Azure]( https://azure.microsoft.com/support/options/) pour restaurer les données à partir de sauvegardes en ligne automatiques. Le support technique Azure est disponible seulement pour certains plans, comme **Standard**, **Développeur** et les plans plus élevés. Le support technique Azure n’est pas disponible avec un plan **De base**. Pour plus d’informations sur les différents plans de support technique, consultez la page [Plans de support Azure](https://azure.microsoft.com/support/plans/). 

Pour restaurer une capture instantanée spécifique de la sauvegarde, Azure Cosmos DB exige que les données soient accessibles pendant la durée du cycle de sauvegarde de cette capture instantanée.

## <a name="request-a-restore"></a>Demander une restauration

Vous devez avoir les informations suivantes avant de demander une restauration :

* Votre ID d’abonnement.

* En fonction de la manière dont vos données ont été accidentellement supprimées ou modifiées, vous devez vous préparer à disposer d’informations supplémentaires. Nous vous recommandons d’avoir ces informations à portée de main à l’avance, afin de minimiser les aller-retours qui peuvent être préjudiciables dans certains cas d’urgence.

* Si l’intégralité du compte Azure Cosmos DB est supprimé, vous devez fournir le nom du compte supprimé. Si vous créez un autre compte avec le même nom que le compte supprimé, partagez-le avec l’équipe de support, car cela aide à identifier le compte correct. Nous vous recommandons d’émettre différents tickets de support pour chaque compte supprimé, car cela permet de réduire la confusion de l’état de la restauration.

* Si une ou plusieurs bases de données sont supprimées, vous devez fournir le compte Azure Cosmos ainsi que les noms des bases de données Azure Cosmos, et spécifier s’il existe une base de données portant le même nom.

* Si un ou plusieurs conteneurs sont supprimés, vous devez fournir le nom du compte Azure Cosmos, les noms des bases de données et les noms des conteneurs. Spécifiez aussi s’il existe un conteneur portant le même nom.

* Si vous avez accidentellement supprimé ou endommagé vos données, vous devez contacter le [support Azure](https://azure.microsoft.com/support/options/) dans les 8 heures afin que l’équipe Azure Cosmos DB puisse vous aider à les restaurer à partir des sauvegardes.
  
  * Si vous avez accidentellement supprimé votre base de données ou votre conteneur, ouvrez un cas de support Sev B ou Sev C Azure. 
  * Si vous avez accidentellement supprimé ou endommagé certains documents au sein du conteneur, ouvrez un cas de support Sev A. 

En cas d’endommagement des données, et si les documents au sein d’un conteneur sont modifiés ou supprimés, **supprimez le conteneur le plus vite possible**. La suppression du conteneur peut éviter qu’Azure Cosmos DB remplace les sauvegardes. Si pour une raison quelconque la suppression n’est pas possible, vous devez émettre un ticket dès que possible. En plus du nom du compte Azure Cosmos, des noms de bases de données et des noms de conteneurs, vous devez spécifier le moment dans le temps auquel les données peuvent être restaurées. Il est important d’être aussi précis que possible, afin de nous aider à déterminer les sauvegardes disponibles à ce moment-là. Il est également important de spécifier l’heure au format UTC. 

La capture d’écran suivante illustre comment créer une demande de support pour un conteneur (collection/graphe/table) afin de restaurer des données à l’aide du portail Azure. Fournissez des détails supplémentaires tels que le type de données, l’objectif de la restauration et le moment auquel les données ont été supprimées, afin de nous aider à affecter une priorité à la demande.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Créer une demande de support de sauvegarde à l’aide du portail Azure":::

## <a name="post-restore-actions"></a>Actions de post-restauration

Après avoir restauré les données, vous recevez une notification concernant le nom du nouveau compte (il est généralement au format `<original-name>-restored1`) et le moment auquel le compte a été restauré. Le compte restauré aura le même débit provisionné et les mêmes stratégies d’indexation, et il se trouvera dans la même région que le compte d’origine. Un utilisateur administrateur ou coadministrateur de l’abonnement peut voir le compte restauré.

Une fois les données restaurées, vous devez inspecter et valider les données dans le compte restauré et vérifier qu’il contient la version que vous attendez. Si tout semble correct, vous devez migrer les données vers votre compte d’origine à l’aide du [flux de modification Azure Cosmos DB](change-feed.md) ou d’[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Nous vous conseillons de supprimer le conteneur ou la base de données immédiatement après la migration des données. Si vous ne supprimez pas les bases de données ou les conteneurs restaurés, ils génèreront des coûts de stockage, de sortie et d’unités de requête.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant découvrir comment migrer les données vers votre compte d’origine à l’aide des articles suivants :

* Pour effectuer une demande de restauration, [émettez un ticket à partir du portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* [Utiliser le flux de modification Cosmos DB](change-feed.md) pour déplacer des données vers Azure Cosmos DB.

* [Utiliser Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) pour déplacer des données vers Azure Cosmos DB.
