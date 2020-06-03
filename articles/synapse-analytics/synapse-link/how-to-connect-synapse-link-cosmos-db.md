---
title: Se connecter à Azure Synapse Link (préversion) pour Cosmos DB
description: Comment connecter une base de données Azure Cosmos DB à un espace de travail Synapse avec Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 6bdc2eca3bdf02814ee851ff266e04bb57f7978c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658829"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Se connecter à Azure Synapse Link (préversion) pour Cosmos DB

Cet article explique comment accéder à une base de données Azure Cosmos DB à partir d’Azure Synapse Analytics Studio avec Azure Synapse Link.

## <a name="prerequisites"></a>Prérequis

Avant de connecter une base de données Azure Cosmos DB à votre espace de travail, les points suivants doivent être respectés :

* Vous avez une base de données Azure Cosmos DB ou vous avez suivi ce [guide de démarrage rapide](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account) pour créer un compte
* Vous avez un espace de travail Synapse ou vous avez suivi ce [guide de démarrage rapide](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) pour créer un espace de travail 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Vous avez activé le magasin analytique Azure Cosmos DB

Pour mener des analyses à grande échelle dans Azure Cosmos DB sans affecter vos performances opérationnelles, nous vous recommandons d’activer Synapse Link pour Azure Cosmos DB. Synapse Link apporte une capacité HTAP pour un conteneur et un support intégré dans Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Accéder à Synapse Studio

Dans votre espace de travail Synapse, sélectionnez **Lancer Synapse Studio**. Sur la page d’accueil de Synapse Studio, sélectionnez **Data (Données), ce qui vous permet de vous connecter à **Data Object Explorer** (Explorateur d'objets de données).

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Connecter une base de données Azure Cosmos DB à un espace de travail Synapse

La connexion d’une base de données Azure Cosmos DB s’effectue en tant que service lié. Un service lié Cosmos DB permet aux utilisateurs de parcourir et d’explorer les données, de les lire et de les écrire à partir d’Apache Spark pour Azure Synapse Analytics ou SQL dans Azure Cosmos DB.

À partir de l’Explorateur d’objets de données, vous pouvez vous connecter directement à une base de données Azure Cosmos DB en procédant comme suit :

1. Sélectionnez l’icône ***+*** à proximité de Données
2. Sélectionnez **Connect to external data** (Se connecter à des sources de données externes)
3. Sélectionnez l’API à laquelle vous souhaitez vous connecter : API SQL ou API pour MongoDB
4. Sélectionnez ***Continue*** (Continuer)
5. Nommez le service lié. Le nom sera affiché dans l’Explorateur d’objets et utilisé par les exécutables Synapse pour se connecter à la base de données et aux conteneurs. Nous vous recommandons d’utiliser un nom convivial.
6. Sélectionnez le **nom du compte Azure Cosmos DB** et le **nom de la base de données**
7. (Facultatif) Si aucune région n’est spécifiée, les opérations d’exécution Synapse sont acheminées vers la région la plus proche dans laquelle le magasin analytique est activé. Toutefois, vous pouvez définir manuellement la région dans laquelle vous souhaitez que vos utilisateurs accèdent au magasin analytique Azure Cosmos DB. Sélectionnez **Additional connection properties** (Propriétés de connexion supplémentaires) puis **New** (Nouveau). Sous **Property Name** (Nom de la propriété), tapez ***PreferredRegions*** et définissez **Value** (Valeur) sur la région de votre choix (exemple : WestUS2, sans espace entre les mots et les nombres)
8. Sélectionnez ***Créer***

Les bases de données Azure Cosmos DB sont visibles sous l’onglet **Linked** (Lié) dans la section Azure Cosmos DB. Avec Azure Cosmos DB, vous pouvez différencier un conteneur activé par HTAP d’un conteneur limité à l’OLTP en vous reportant aux icônes suivantes :

**Conteneur limité à l’OLTP** :

![Conteneur OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Conteneur activé par HTAP**:

![Conteneur HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagir rapidement avec des actions générées par le code

Lorsque vous cliquez avec le bouton droit sur un conteneur, vous obtenez une liste des mouvements qui déclencheront une exécution Spark ou SQL. L’écriture de données dans un conteneur aura lieu via le magasin transactionnel d’Azure Cosmos DB et consommera des unités de requête.  

## <a name="next-steps"></a>Étapes suivantes

* [Découvrir ce qui est pris en charge entre Synapse et Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Découvrir comment interroger un magasin analytique avec Spark](./how-to-query-analytical-store-spark.md)