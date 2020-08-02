---
title: 'Démarrage rapide : créer un pool Synapse SQL à l’aide de Synapse Studio'
description: Créez un pool SQL Synapse à l’aide de Synapse Studio en suivant les étapes décrites dans ce guide.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f85731a7f3ffef0adf35812ee8da4e0bbd89124f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036571"
---
# <a name="quickstart-create-a-synapse-sql-pool-using-synapse-studio"></a>Démarrage rapide : créer un pool Synapse SQL à l’aide de Synapse Studio

Azure Synapse Analytics propose différents moteurs d’analytique pour vous aider à ingérer, transformer, modéliser et analyser vos données. Un pool SQL offre des fonctionnalités de calcul et de stockage basées sur T-SQL. Après avoir créé un pool SQL dans votre espace de travail Synapse, vous pouvez charger, modéliser, traiter et distribuer les données pour obtenir plus rapidement des insights.

Ce guide de démarrage rapide décrit les étapes à suivre pour créer un pool SQL dans un espace de travail Synapse à l’aide de Synapse Studio.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Espace de travail Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accéder à l’espace de travail Synapse

1. Accédez à l’espace de travail Synapse dans lequel vous allez créer le pool SQL en saisissant le nom du service (ou directement le nom de la ressource) dans la barre de recherche.
![Barre de recherche du portail Azure contenant le texte « espaces de travail Synapse ».](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Dans la liste des espaces de travail, saisissez le nom (ou une partie du nom) de l’espace de travail à ouvrir. Pour cet exemple, nous allons utiliser un espace de travail nommé **contosoanalytics**.
![Liste des espaces de travail Synapse filtrés pour afficher ceux contenant le nom contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Lancer Synapse Studio

1. Dans la vue d’ensemble de l’espace de travail, sélectionnez **Lancer Synapse Studio** pour ouvrir l’emplacement où le pool SQL sera créé. Saisissez le nom du service ou de la ressource directement dans la barre de recherche.
![Vue d’ensemble de l’espace de travail Synapse du portail Azure avec l’option Lancer Synapse Studio mise en surbrillance.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Créer un pool SQL dans Synapse Studio

1. Sur la page d’accueil de Synapse Studio, accédez au **hub de gestion** dans le volet de navigation gauche en sélectionnant l’icône **Gérer**.
![Page d’accueil de Synapse Studio avec la section Hub de gestion mise en surbrillance.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Une fois dans le hub de gestion, accédez à la section **Pools SQL** pour afficher la liste actuelle des pools SQL disponibles dans l’espace de travail.
![Hub de gestion de Synapse Studio avec navigation dans les pools SQL sélectionnée](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Sélectionnez la commande **+ Nouveau** ; l’assistant de création d’un pool SQL s’affiche. 
![Liste des pools SQL du hub de gestion de Synapse Studio.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Sous l’onglet **Informations de base**, entrez ce qui suit :

    | Paramètre | Valeur suggérée | Description |
    | :------ | :-------------- | :---------- |
    | **Nom du pool SQL** | contosoedw | Il s’agit du nom du pool SQL. |
    | **Niveau de performances** | DW100c | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |

    ![Flux de création de pools SQL - Onglet Informations de base.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Notez que les noms que vous pouvez attribuer aux pools SQL sont soumis à des limitations spécifiques. Les noms ne peuvent pas contenir de caractères spéciaux, ne doivent pas dépasser 15 caractères, ne doivent pas contenir de mots réservés et doivent être uniques dans l’espace de travail.

4. Dans l’onglet suivant, **Paramètres supplémentaires**, sélectionnez **aucun** pour approvisionner le pool SQL sans données. Laissez le classement par défaut tel quel.
![Flux de création d’un pool SQL - Onglet Paramètres supplémentaires.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Nous n’ajoutons aucune étiquette pour le moment. Par conséquent, sélectionnez **Suivant : Vérifier + créer**.

1. Dans l’onglet **Vérifier + créer**, vérifiez que les informations reflètent bien ce que vous avez entré, puis appuyez **Créer**. 
![Flux de création d’un pool SQL - Onglet Passer en revue les paramètres.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. À ce stade, le flux de provisionnement des ressources démarre.

1. Quand vous revenez à l’espace de travail au terme du provisionnement, une entrée apparaît pour le pool SQL nouvellement créé.
 ![Flux de création d’un pool SQL - Provisionnement des ressources.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Une fois le pool SQL créé, vous pouvez l’utiliser dans l’espace de travail pour charger des données, traiter des flux, lire dans le lac, etc.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Nettoyer un pool SQL à l’aide de Synapse Studio    

Effectuez les étapes ci-dessous pour supprimer le pool SQL de l’espace de travail à l’aide de Synapse Studio.
> [!WARNING]
> La suppression d’un pool SQL supprime le moteur d’analytique de l’espace de travail. Il n’est plus possible de se connecter au pool, et l’ensemble des requêtes, pipelines et scripts qui utilisent ce pool SQL ne fonctionnent plus.

Si vous souhaitez supprimer le pool SQL, procédez comme suit :

1. Accédez aux pools SQL dans le hub de gestion de Synapse Studio.
1. Sélectionnez les points de suspension dans le pool SQL à supprimer (dans ce cas, **contosoedw**) pour afficher les commandes du pool SQL : ![Liste des pools SQL, avec sélection du pool récemment créé.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Appuyez sur la touche **Supprimer**.
1. Confirmez la suppression, puis appuyez sur le bouton **Supprimer**.
 ![Boîte de dialogue permettant de confirmer la suppression du pool SQL sélectionné.](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. À la fin du processus, le pool SQL ne figure plus dans la liste des ressources de l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes 
- Consultez [Démarrage rapide : Créer un notebook Apache Spark](quickstart-apache-spark-notebook.md).
- Consultez [Démarrage rapide : Créer un pool SQL Synapse avec le portail Azure](quickstart-create-sql-pool-portal.md).
