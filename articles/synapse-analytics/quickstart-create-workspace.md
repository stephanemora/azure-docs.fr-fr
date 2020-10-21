---
title: 'Démarrage rapide : créer un espace de travail Synapse'
description: Créez un espace de travail Synapse en suivant les étapes décrites dans ce guide.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9780a2adb60c690abda6880157252aaa0c562f18
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171786"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Démarrage rapide : Créer un espace de travail Synapse
Ce guide de démarrage rapide décrit les étapes à suivre pour créer un espace de travail Azure Synapse à l’aide du portail Azure.

## <a name="create-a-synapse-workspace"></a>Créer un espace de travail Synapse

1. Ouvrez le [Portail Azure](https://portal.azure.com) et, en haut, recherchez **Synapse**.
1. Dans les résultats de la recherche, sous **Services**, sélectionnez **Azure Synapse Analytics (préversion des espaces de travail)** .
1. Sélectionnez **Ajouter** pour créer un espace de travail avec ces paramètres :

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom de l’espace de travail**|Vous pouvez lui donner le nom que vous voulez.| Dans ce document, nous l’appellerons **myworkspace**.|
    |Concepts de base|**Région**|Choisissez la même région que le compte de stockage.|

1. Un compte ADLS Gen2 est nécessaire pour créer un espace de travail. Le choix le plus simple consiste à en créer un nouveau. Si vous souhaitez en réutiliser un, vous devrez effectuer une configuration supplémentaire. 
1. OPTION 1 Création d’un nouveau compte ADLS Gen2 : 
    1. Sous **Sélectionnez Data Lake Storage Gen2**, cliquez sur **Créer** et nommez-le **contosolake**.
    1. Sous **Sélectionnez Data Lake Storage Gen2**, cliquez sur **Système de fichiers** et nommez-le **users**.
1. OPTION 2 Consultez les instructions **Préparation d’un compte de stockage** qui se trouvent à la fin de ce document.
1. Votre espace de travail Azure Synapse utilisera ce compte de stockage comme compte de stockage « principal » et le conteneur pour stocker les données de l’espace de travail. L’espace de travail stocke les données dans des tables Apache Spark. Il stocke les journaux des applications Spark dans un dossier appelé **/synapse/workspacename**.
1. Sélectionnez **Vérifier + créer** > **Créer**. Votre espace de travail est prêt en quelques minutes.

## <a name="open-synapse-studio"></a>Ouvrir Synapse Studio

Après avoir créé votre espace de travail Azure Synapse, vous pouvez ouvrir Synapse Studio de deux manières :

* Ouvrez votre espace de travail Synapse dans le [Portail Azure](https://portal.azure.com). En haut de la section **Vue d’ensemble**, sélectionnez **Lancer Synapse Studio**.
* Accédez à `https://web.azuresynapse.net` et connectez-vous à votre espace de travail.

## <a name="prepare-an-existing-storage-account-for-use-with-synapse-analytics"></a>Préparer un compte de stockage existant à utiliser avec Synapse Analytics

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Accéder à un compte de stockage ADLSGEN2 existant
1. Sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche. Attribuez ensuite les rôles suivants ou vérifiez qu’ils sont déjà attribués :
    * Attribuez-vous le rôle **Propriétaire**.
    * Attribuez-vous le rôle **Propriétaire des données Blob du stockage**.
1. Dans le volet de gauche, sélectionnez **Conteneurs** et créez un conteneur.
1. Attribuez au conteneur un nom de votre choix. Dans ce document, nous appellerons le conteneur **users**.
1. Acceptez le paramètre par défaut **Niveau d’accès public**, puis sélectionnez **Créer**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Configuration de l’accès au compte de stockage à partir de votre espace de travail

Les identités managées pour votre espace de travail Azure Synapse ont peut-être déjà accès au compte de stockage. Vérifiez ce point en effectuant ces étapes :

1. Ouvrez le [Portail Azure](https://portal.azure.com) et le compte de stockage principal choisi pour votre espace de travail.
1. Dans le volet de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Attribuez les rôles suivants ou vérifiez qu’ils sont déjà attribués. Nous utilisons le même nom pour l’identité de l’espace de travail et le nom de l’espace de travail.
    * Pour le rôle **Contributeur aux données Blob du stockage** sur le compte de stockage, affectez **myworkspace** comme identité de l’espace de travail.
    * Attribuez le nom **myworkspace** à l’espace de travail.

1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un pool SQL](quickstart-create-sql-pool-studio.md) 
* [Créer un pool Apache Spark](quickstart-create-apache-spark-pool-portal.md)
* [Utiliser SQL à la demande](quickstart-sql-on-demand.md)
