---
title: Découvrir, connecter et explorer des données dans Synapse en utilisant Azure Purview
description: Guide sur la façon de découvrir des données, de les connecter et de les explorer dans Synapse
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567891"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Découvrir, connecter et explorer des données dans Synapse en utilisant Azure Purview 

Dans ce document, vous apprendrez le type d’interactions que vous pouvez effectuer lors de l’inscription d’un compte Azure Purview auprès de Synapse. 

## <a name="prerequisites"></a>Prérequis 

- [Compte Azure Purview](../../purview/create-catalog-portal.md) 
- [Espace de travail Synapse](../quickstart-create-workspace.md) 
- [Connecter un compte Azure Purview à Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Utilisation d’Azure Purview dans Synapse 

Pour utiliser Azure Purview dans Synapse, vous devez avoir accès à ce compte Purview. Synapse passe par votre autorisation Purview. Par exemple, si vous avez un rôle d’autorisation conservateur, vous pourrez modifier les métadonnées analysées par Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Découverte de données : rechercher dans les jeux de données 

Pour découvrir les données enregistrées et analysées par Azure Purview, vous pouvez utiliser la barre de recherche en haut au centre de l’espace de travail de Synapse. Veillez à sélectionner Azure Purview pour rechercher toutes les données de votre organisation. 

[![Rechercher des ressources Azure Purview](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Actions Azure Purview 

Voici une liste des fonctionnalités Azure Purview disponibles dans Synapse : 
- **Vue d’ensemble** des métadonnées 
- Afficher et modifier le **schéma** des métadonnées avec les classifications, les termes du glossaire, les types de données et les descriptions 
- Afficher la **traçabilité des données** pour comprendre les dépendances et effectuer des analyses d’impact. Pour plus d’informations, voir [Traçabilité des données](../../purview/catalog-lineage-user-guide.md).
- Afficher et modifier **Contacts** pour savoir qui est propriétaire ou expert d’un jeu de données 
- **Connexe** pour comprendre les dépendances hiérarchiques d’un jeu de données spécifique. Cette expérience est utile pour parcourir la hiérarchie des données.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Actions que vous pouvez effectuer sur les jeux de données avec des ressources Synapse 

### <a name="connect-data-to-synapse"></a>Connecter des données à Synapse 

- Vous pouvez créer un **nouveau service lié** à Synapse. Cette action est nécessaire pour copier des données dans Synapse ou les faire figurer dans votre hub de données (pour les sources de données prises en charge telles qu’ADLSg2). 
- Pour les objets tels que les fichiers, les dossiers ou les tables, vous pouvez créer directement un **nouveau jeu de données d’intégration** et exploiter un service lié existant s’il est déjà créé. 

Nous ne sommes pas encore en mesure de déduire l’existence d’un service lié ou d’un jeu de données d’intégration. 

###  <a name="develop-in-synapse"></a>Développer dans Synapse 

Vous pouvez effectuer trois actions : **Nouveau script SQL**, **Nouveau notebook** et **Nouveau flux de données**. 

Avec **Nouveau script SQL**, selon le type de prise en charge, vous pouvez : 
- Afficher les 100 premières lignes afin de comprendre la forme des données 
- Créer une table externe à partir d’une base de données Synapse SQL 
- Charger les données dans une base de données Synapse SQL 
 
Avec **Nouveau notebook**, vous pouvez : 
- Charger des données dans un DataFrame Spark 
- Créer une table Spark (si vous effectuez cette opération au format Parquet, elle crée également une table de pools SQL serverless) 
 
Avec **Nouveau flux de données**, vous pouvez créer un jeu de données d’intégration qui peut être utilisé comme source dans un pipeline de flux de données. Le flux de données est une capacité de développeur sans code permettant d’effectuer la transformation des données. Pour d’informations sur l’[utilisation du flux de données dans Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Étapes suivantes 

- [Inscrire et analyser des ressources Azure Synapse dans Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Comment rechercher des données dans Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)
