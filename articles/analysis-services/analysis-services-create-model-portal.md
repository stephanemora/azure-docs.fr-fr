---
title: Créer un modèle tabulaire à l’aide du concepteur web Azure Analysis Services | Microsoft Docs
description: Découvrez comment créer un modèle tabulaire Azure Analysis Services à l’aide du concepteur web dans le portail Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0b613a978c15715519ecc0f130ff6ff3ee84e306
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186963"
---
# <a name="create-a-model-in-azure-portal"></a>Créer un modèle dans le portail Azure

La fonctionnalité de concepteur Web Azure Analysis Services (aperçu) dans le portail Azure fournit un moyen simple et rapide de créer et de modifier des modèles tabulaires et d’interroger les données des modèles directement dans votre navigateur. 

> [!IMPORTANT]
> Cette fonctionnalité est dépréciée. Elle peut être supprimée ou grandement remaniée dans une prochaine mise à jour. Il est recommandé d’interrompre l’utilisation de cette fonctionnalité dans les projets existants ou nouveaux, afin d’assurer la compatibilité avec les futures mises à jour. Pour un développement et un test de modèles plus avancés, utilisez plutôt Visual Studio (SSDT) et SQL Server Management Studio (SSMS).


## <a name="before-you-begin"></a>Avant de commencer

- Votre serveur Azure Analysis Services doit être au niveau Standard ou Développeur. Les modèles créés à l’aide du concepteur web sont en mode DirectQuery, uniquement pris en charge par ces niveaux.
- Un fichier Azure SQL Database, Azure SQL Data Warehouse ou Power BI Desktop (.pbix) comme source de données. Les modèles créés à partir de fichiers Power BI Desktop prennent en charge Azure SQL Database et Azure SQL Data Warehouse.
- Un compte et un mot de passe SQL Server pour se connecter aux sources de données Azure SQL Database ou Azure SQL Data Warehouse.
- Vous devez disposer des privilèges Administrateur de serveur pour créer un nouveau modèle. Les privilèges Administrateur de base de données sont requis pour modifier et interroger un modèle à l’aide du concepteur.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Pour créer un modèle tabulaire

1. Sur votre serveur, dans **Vue d’ensemble** > **Concepteur web**, cliquez sur **Ouvrir**.

    ![Créer un modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Dans **Concepteur web** > **Modèles**, cliquez sur **Ajouter**.

    ![Créer un modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. Dans **Nouveau modèle**, tapez un nom de modèle, puis sélectionnez une source de données.

    ![Boîte de dialogue Nouveau modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. Dans **Connexion**, entrez les propriétés de connexion. Le nom d’utilisateur et le mot de passe doivent correspondre à un compte SQL Server.

     ![Boîte de dialogue Connexion dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. Dans **Tables et vues**, sélectionnez les tables à inclure dans votre modèle, puis cliquez sur **Créer**. Les relations sont créées automatiquement entre les tables avec une paire de clés.

     ![Sélectionner les tables et les vues](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Votre nouveau modèle s’affiche dans votre navigateur. À ce stade, vous pouvez :   

- Interroger les données du modèle en faisant glisser des champs vers le concepteur de requêtes et en ajoutant des filtres.
- Créer des mesures dans les tables.
- Modifier les métadonnées du modèle à l’aide de l’éditeur json.
- Ouvrir le modèle dans Visual Studio (SSDT), Power BI Desktop ou Excel.

![Sélectionner les tables et les vues](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Quand vous modifiez les métadonnées du modèle ou créez des mesures dans votre navigateur, vous enregistrez les modifications apportées à votre modèle dans Azure. Si vous travaillez également sur votre modèle dans SSDT, Power BI Desktop ou Excel, il peut être désynchronisé.


## <a name="next-steps"></a>Étapes suivantes 
[Gérer les utilisateurs et rôles de bases de données](analysis-services-database-users.md)  
[Connexion avec Excel](analysis-services-connect-excel.md)  


