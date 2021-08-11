---
title: Fichier Include
description: Fichier include
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 8b512b0a73a0d95182c6614e498d20c3b3a23596
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112235390"
---
Il est maintenant possible d’utiliser l’outil Explorateur de données sur le Portail Azure afin de créer une API Azure Cosmos DB pour la base de données MongoDB et un conteneur. 

1. Sélectionnez **Explorateur de données** > **Nouveau conteneur**. 
    
    La zone **Ajouter un conteneur** s’affiche à l’extrême droite : il peut donc être nécessaire de faire défiler à droite pour l’afficher.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Explorateur de données du portail Azure, volet Ajouter un conteneur":::

2. Dans la page **Ajouter un conteneur**, entrez les paramètres du nouveau conteneur.

    |Paramètre|Valeur suggérée|Description
    |---|---|---|
    |**ID de base de données**|db|Entrez *db* comme nom de la nouvelle base de données. Les noms de base de données doivent comporter entre 1 et 255 caractères, et ne peuvent pas contenir les caractères `/, \\, #, ?`, ni un espace de fin. Cochez l’option **Provisionner le débit de base de données** qui vous permet de partager le débit provisionné pour la base de données entre tous les conteneurs au sein de la base de données. Cette option permet également de réduire les coûts. |
    |**Débit**|400|Laissez le débit sur 400 unités de requête par seconde (RU/s). Si vous souhaitez réduire la latence, vous pourrez augmenter le débit par la suite. Vous pouvez également choisir le [mode de mise à l’échelle automatique](../provision-throughput-autoscale.md) pour obtenir une plage de RU/s qui augmente et diminue de manière dynamique en fonction des besoins.| 
    |**ID de la collection**|coll|Entrez `coll` comme nom de votre nouveau conteneur. Les ID de conteneur sont soumis aux mêmes exigences en termes de caractères que les noms de base de données.|
    |**Capacité de stockage**|Fixe (10 Go)|Entrez *Fixe (10 Go)* pour cette application. Si vous sélectionnez *Illimité*, vous devez créer une `Shard Key`, dont tous les éléments insérés auront besoin.|
    |**Clé de partition**| /_id| L’exemple décrit dans cet article n’utilise pas de clé de partition. Par conséquent, si ce paramètre a la valeur */_id*, c’est le champ ID généré automatiquement qui est utilisé comme clé partition. Pour plus d’informations sur le sharding, également appelé partitionnement, consultez [Partitionnement dans Azure Cosmos DB](../partitioning-overview.md).|
        
    Sélectionnez **OK**. L’Explorateur de données affiche la nouvelle base de données et le nouveau conteneur.