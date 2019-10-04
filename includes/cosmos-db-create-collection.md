---
title: Fichier Include
description: Fichier Include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 45fa6a332697cf298b2446212701025007682357
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754276"
---
Vous pouvez désormais utiliser l’outil Explorateur de données dans le portail Azure pour créer une base de données et un conteneur. 

1. Sélectionnez **Explorateur de données** > **Nouveau conteneur**. 
    
    La zone **Ajouter un conteneur** s’affiche à l’extrême droite : il peut donc être nécessaire de faire défiler à droite pour l’afficher.

    ![Explorateur de données du portail Azure, volet Ajouter un conteneur](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Dans la page **Ajouter un conteneur**, entrez les paramètres du nouveau conteneur.

    |Paramètre|Valeur suggérée|Description
    |---|---|---|
    |**ID de base de données**|Tâches|Entrez *ToDoList* pour le nom de la nouvelle base de données. Les noms de base de données doivent comporter entre 1 et 255 caractères, et ne peuvent pas contenir les caractères `/, \\, #, ?`, ni un espace de fin. Cochez l’option **Provisionner le débit de base de données** qui vous permet de partager le débit provisionné pour la base de données entre tous les conteneurs au sein de la base de données. Cette option permet également de réduire les coûts. |
    |**Débit**|400|Laissez le débit sur 400 unités de requête par seconde (RU/s). Si vous souhaitez réduire la latence, vous pourrez augmenter le débit par la suite.| 
    |**ID de conteneur**|Éléments|Entrez *Éléments* comme nom de votre nouveau conteneur. Les ID de conteneur sont soumis aux mêmes exigences en termes de caractères que les noms de base de données.|
    |**Clé de partition**| /category| L’exemple décrit dans cet article utilise */category* comme clé de partition.|
    
    Outre les paramètres précédents, vous pouvez ajouter des **clés uniques** pour le conteneur. Laissez le champ vide dans cet exemple. Les clés uniques permettent aux développeurs d’ajouter une couche d’intégrité des données à la base de données. En créant une stratégie de clé unique durant la création d’un conteneur, vous garantissez l’unicité d’une ou de plusieurs valeurs par clé de partition. Pour en savoir plus, référez-vous à l’article [Clés uniques dans Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Sélectionnez **OK**. L’Explorateur de données affiche la nouvelle base de données et le nouveau conteneur.