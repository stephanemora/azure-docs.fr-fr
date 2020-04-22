---
title: 'Démarrage rapide : Créer un pool Apache Spark (préversion)'
description: Effectuez les étapes décrites dans ce guide afin de créer un pool Apache Spark pour un espace de travail Azure Synapse Analytics.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420623"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Démarrage rapide : Créer un pool Apache Spark (préversion)

Synapse Analytics propose différents moteurs d’analytique pour vous aider à ingérer, transformer, modéliser, analyser et servir vos données. Un pool Apache Spark offre des fonctionnalités de calcul Big Data open source. Après avoir créé un pool Apache Spark dans votre espace de travail Synapse, vous pouvez charger, modéliser, traiter et servir les données pour obtenir des insights.

Dans ce guide de démarrage rapide, vous allez apprendre à utiliser le portail Azure pour créer un pool Apache Spark dans un espace de travail Synapse.

> [!IMPORTANT]
> La facturation des instances Spark est calculée au prorata des minutes écoulées, que vous les utilisiez ou non. Veillez à arrêter votre instance Spark une fois que vous avez fini de l’utiliser, ou définissez un délai d’expiration court. Pour plus d’informations, consultez la section **Nettoyer les ressources** de cet article.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https:/azure.microsoft.com/free/)
- [Espace de travail Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Créer un pool Apache Spark

1. Dans l’espace de travail Synapse où vous souhaitez créer le pool Apache Spark, cliquez sur **Nouveau pool Apache Spark**.
![Vue d’ensemble de l’espace de travail Synapse avec, dans un encadré rouge, la commande permettant de créer un pool Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Sous l’onglet **Général**, entrez les informations suivantes :

    |Paramètre | Valeur suggérée | Description |
    | :------ | :-------------- | :---------- |
    | **Nom du pool Apache Spark** | Nom de pool valide | Il s’agit du nom du pool Apache Spark. |
    | **Taille du nœud** | Petite (4 processeurs virtuels/32 Go) | Définissez ce paramètre sur la plus petite taille pour réduire les coûts de ce guide de démarrage rapide. |
    | **Autoscale** | activé | Conservez ce paramétrage par défaut. |
    | **Nombre de nœuds** | 3 à 40 | Conservez ce paramétrage par défaut. |
    ||||

    ![Flux de création d’un pool Apache Spark - Onglet Général](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Notez que les noms que vous pouvez attribuer aux pools Apache Spark sont soumis à des limitations spécifiques. Les noms doivent contenir uniquement des lettres ou des chiffres, ne doivent pas comporter plus de 15 caractères, doivent commencer par une lettre, ne pas contenir de mots réservés et être uniques dans l’espace de travail.

3. Cliquez sur **Suivant : Paramètres supplémentaires** et passez en revue les paramètres par défaut. Ne modifiez pas les paramètres par défaut.
![Flux de création d’un pool Apache Spark - Onglet Paramètres supplémentaires](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Cliquez sur **Suivant : Étiquettes**. N’ajoutez pas d’étiquettes.
![Flux de création d’un pool Apache Spark - Onglet Paramètres supplémentaires](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Cliquez sur **Vérifier + créer**.

6. Vérifiez que les informations reflètent bien ce que vous avez entré, puis cliquez sur **Créer**.
![Flux de création d’un pool Apache Spark - Onglet Passer en revue les paramètres](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. À ce stade, le flux de provisionnement des ressources démarre. Une fois terminé, il indique ![Flux de création d’un pool Apache Spark - Provisionnement des ressources](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png).

8. Une fois le provisionnement terminé, une nouvelle entrée apparaît pour le pool Apache Spark créé quand vous revenez à l’espace de travail.
 ![Flux de création d’un pool Apache Spark - Provisionnement des ressources](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. À ce stade, aucune ressource n’est en cours d’exécution, rien n’est facturé pour Spark ; vous avez créé les métadonnées sur les instances Spark que vous souhaitez créer.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Effectuez les étapes ci-dessous pour supprimer le pool Apache Spark de l’espace de travail.
> [!WARNING]
> La suppression d’un pool Apache Spark supprime le moteur d’analytique de l’espace de travail. Il n’est plus possible de se connecter au pool, et l’ensemble des requêtes, pipelines et notebooks qui utilisent ce pool Apache Spark ne fonctionnent plus.

Si vous souhaitez supprimer le pool Apache Spark, procédez comme suit :

1. Accédez au panneau des pools Apache Spark dans l’espace de travail.
2. Sélectionnez le pool Apache à supprimer (dans ce cas, **contosospark**).
3. Appuyez sur la touche **Supprimer**.
 ![Liste des pools Apache Spark, avec sélection du pool récemment créé.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Confirmez la suppression, puis appuyez sur le bouton **Supprimer**.
 ![Boîte de dialogue permettant de confirmer la suppression du pool Apache Spark sélectionné.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Une fois le processus terminé, le pool Apache Spark ne figure plus dans la liste des ressources de l’espace de travail.

Une fois le pool Apache Spark créé, vous pouvez l’utiliser dans l’espace de travail pour charger des données, traiter des flux, lire dans le lac, etc.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Démarrage rapide : Créer un pool Apache Spark dans Synapse Studio à l’aide des outils web](spark/apache-spark-notebook-create-spark-use-sql.md).
- Consultez [Démarrage rapide : Créer un pool SQL Synapse avec le portail Azure](quickstart-create-sql-pool.md).
