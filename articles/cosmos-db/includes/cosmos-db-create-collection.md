---
title: Fichier Include
description: Fichier include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 22e4be00c67a854a53339ee5f6034b6d9d741022
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112234904"
---
Vous pouvez désormais utiliser l’outil Explorateur de données dans le portail Azure pour créer une base de données et un conteneur. 

1. Sélectionnez **Explorateur de données** > **Nouveau conteneur**. 
    
    La zone **Ajouter un conteneur** s’affiche à l’extrême droite : il peut donc être nécessaire de faire défiler à droite pour l’afficher.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Explorateur de données du portail Azure, volet Ajouter un conteneur":::

2. Dans la page **Ajouter un conteneur**, entrez les paramètres du nouveau conteneur.

    |Paramètre|Valeur suggérée|Description
    |---|---|---|
    |**ID de base de données**|ToDoList|Entrez *Tasks* comme nom pour la nouvelle base de données. Les noms de base de données doivent comporter entre 1 et 255 caractères, et ne peuvent pas contenir les caractères `/, \\, #, ?`, ni un espace de fin. Cochez l’option **Partager le débit entre les conteneurs** qui vous permet de partager le débit provisionné sur la base de données entre tous les conteneurs au sein de la base de données. Cette option permet également de réduire les coûts. |
    | **Débit de la base de données**| Vous pouvez provisionner un débit **Mise à l’échelle automatique** ou **Manuel**. Le débit Manuel vous permet de mettre à l’échelle les RU/s vous-même, tandis que le débit Mise à l’échelle automatique permet au système d’adapter les RU/s en fonction de l’utilisation. Sélectionnez **Manuel** pour cet exemple. <br><br> Laissez le débit sur 400 unités de requête par seconde (RU/s). Si vous voulez réduire la latence, vous pouvez effectuer un scale-up du débit plus tard en estimant les RU/s avec la [calculatrice de capacité](../estimate-ru-with-capacity-planner.md).<br><br>**Remarque** : Ce paramètre n’est pas disponible lors de la création d’un nouveau conteneur dans un compte serverless. |
    |**ID de conteneur**|Éléments|Entrez *Éléments* comme nom de votre nouveau conteneur. Les ID de conteneur sont soumis aux mêmes exigences en termes de caractères que les noms de base de données.|
    |**Clé de partition**| /category| L’exemple décrit dans cet article utilise */category* comme clé de partition.|

    Pour cet exemple, n’ajoutez pas de **clés uniques** et n’activez pas le **Magasin analytique**. Les clés uniques vous permettent d’ajouter une couche d’intégrité des données à la base de données en garantissant que chaque clé de partition contient des valeurs uniques. Pour plus d’informations, consultez [Clés uniques dans Azure Cosmos DB](../unique-keys.md). Le [Magasin analytique](../analytical-store-introduction.md) permet d’effectuer des analyses à grande échelle sur des données opérationnelles sans aucun impact sur vos charges de travail transactionnelles.
    
    Sélectionnez **OK**. L’Explorateur de données affiche la nouvelle base de données et le nouveau conteneur.