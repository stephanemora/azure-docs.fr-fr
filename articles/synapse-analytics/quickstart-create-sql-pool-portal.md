---
title: 'Démarrage rapide : Créer un pool SQL Synapse (préversion) à l’aide du portail Azure'
description: Créez un pool SQL Synapse à l’aide du portail Azure en suivant les étapes de ce guide.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 966ca099c44ba8b0f2d3241c391f4d7a101afb26
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955095"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>Démarrage rapide : Créer un pool SQL Synapse (préversion) à l’aide du portail Azure

Azure Synapse Analytics propose différents moteurs d’analytique pour vous aider à ingérer, transformer, modéliser et analyser vos données. Un pool SQL offre des fonctionnalités de calcul et de stockage basées sur T-SQL. Après avoir créé un pool SQL dans votre espace de travail Synapse, vous pouvez charger, modéliser, traiter et distribuer les données pour obtenir plus rapidement des insights.

Dans ce guide de démarrage rapide, vous allez apprendre à créer un pool SQL dans un espace de travail Synapse à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Espace de travail Synapse](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Accéder à l’espace de travail Synapse

1. Accédez à l’espace de travail Synapse dans lequel vous allez créer le pool SQL en saisissant le nom du service (ou directement le nom de la ressource) dans la barre de recherche.
![Barre de recherche du portail Azure contenant le texte « espaces de travail Synapse ».](media/quickstart-create-sql-pool/create-sql-pool-00a.png). 
1. Dans la liste des espaces de travail, saisissez le nom (ou une partie du nom) de l’espace de travail à ouvrir. Pour cet exemple, nous allons utiliser un espace de travail nommé **contosoanalytics**.
![Liste des espaces de travail Synapse filtrés pour afficher ceux contenant le nom contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>Créer un pool SQL

1. Dans l’espace de travail Synapse où vous souhaitez créer le pool SQL, sélectionnez **Nouveau pool SQL** dans la barre supérieure.
![Vue d’ensemble de l’espace de travail Synapse avec, dans un encadré rouge, la commande permettant de créer un pool SQL.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Sous l’onglet **Informations de base**, entrez ce qui suit :

    | Paramètre | Valeur suggérée | Description |
    | :------ | :-------------- | :---------- |
    | **Nom du pool SQL** | Nom valide | Nom du pool SQL. |
    | **Niveau de performances** | DW100c | Plus petite taille définie pour réduire les coûts de ce guide de démarrage rapide |

  
    ![Flux de création d’un pool SQL - Onglet Informations de base.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > Notez que les noms que vous pouvez attribuer aux pools SQL sont soumis à des limitations spécifiques. Les noms ne peuvent pas contenir de caractères spéciaux, ne doivent pas dépasser 15 caractères, ne doivent pas contenir de mots réservés et doivent être uniques dans l’espace de travail.

3. Sélectionnez **Suivant : Paramètres supplémentaires**.
4. Sélectionnez **Aucune** pour provisionner le pool SQL sans données. Laissez le classement par défaut tel quel.
![Flux de création d’un pool SQL - Onglet Paramètres supplémentaires.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Sélectionnez **Revoir + créer**.
6. Vérifiez que les informations reflètent bien ce que vous avez entré. Sélectionnez **Create** (Créer).
![Flux de création d’un pool SQL - Onglet Passer en revue les paramètres.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. À ce stade, le flux de provisionnement des ressources démarre.
 ![Flux de création d’un pool SQL - Provisionnement des ressources.](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. Quand vous revenez à l’espace de travail au terme du provisionnement, une entrée apparaît pour le pool SQL nouvellement créé.
 ![Flux de création d’un pool SQL - Provisionnement des ressources.](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


Une fois le pool SQL créé, vous pouvez l’utiliser dans l’espace de travail pour charger des données, traiter des flux, lire dans le lac, etc.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Effectuez les étapes ci-dessous pour supprimer le pool SQL de l’espace de travail.
> [!WARNING]
> Le fait de supprimer un pool SQL supprime à la fois le moteur d’analytique et les données stockées dans la base de données du pool SQL supprimé de l’espace de travail. Il n’est plus possible de se connecter au pool SQL, et l’ensemble des requêtes, pipelines et notebooks qui lisent ou écrivent dans ce pool SQL ne fonctionnent plus.

Si vous souhaitez supprimer le pool SQL, effectuez les étapes suivantes :

1. Accédez au panneau des pools SQL du panneau de l’espace de travail.
1. Sélectionnez le pool SQL à supprimer (dans ce cas, **contosowdw**)
1. Une fois le pool sélectionné, appuyez sur **Supprimer**
1. Confirmez la suppression, puis appuyez sur le bouton **Supprimer** ![Vue d’ensemble du pool SQL - mise e, suppression de la confirmation de suppression.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. À la fin du processus, le pool SQL ne figure plus dans la liste des ressources de l’espace de travail.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Démarrage rapide : Créer un pool Apache Spark dans Synapse Studio à l’aide des outils web](quickstart-apache-spark-notebook.md).
- Consultez [Démarrage rapide : Créer un pool Apache Spark à l’aide du portail Azure](quickstart-create-apache-spark-pool-portal.md).
