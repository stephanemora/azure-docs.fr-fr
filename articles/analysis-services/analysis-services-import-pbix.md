---
title: Importer un fichier Power BI Desktop dans Azure Analysis Services | Microsoft Docs
description: Décrit comment importer un fichier Power BI Desktop (pbix) à l’aide du portail Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a2855ca5dbb76d3fcc30c4b1007c20bb48c91c9b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141502"
---
# <a name="import-a-power-bi-desktop-file"></a>Importer un fichier Power BI Desktop

Vous pouvez importer un modèle de données dans un fichier Power BI Desktop (pbix) dans Azure Analysis Services Les connexions de sources de données, les données en cache et les métadonnées de modèle sont importées. Les rapports et les visualisations ne sont pas importés. Les modèles de donnée importés depuis Power BI Desktop ne sont qu’au niveau de compatibilité 1400.

**Restrictions**   

- L’importation à partir d’un fichier pbix utilise la fonctionnalité de concepteur web dans le portail, disponible en **préversion**. Les fonctionnalités disponibles sont limitées. Pour un développement et un test de modèles plus avancés, utilisez plutôt Visual Studio (SSDT) et SQL Server Management Studio (SSMS).
- Vous devez disposer des autorisations d’administrateur de serveur pour importer à partir d’un fichier pbix.
- Le modèle pbix peut se connecter aux sources de données **Azure SQL Database** et **Azure SQL Data Warehouse** uniquement.
- Le modèle pbix ne peut pas avoir de connexions DirectQuery ou en temps réel. 
- L’importation risque d’échouer si votre modèle de données pbix contient des métadonnées non prises en charge dans Analysis Services.


## <a name="to-import-from-pbix"></a>Importation à partir de pbix

1. Sur votre serveur, dans **Vue d’ensemble** > **Concepteur web**, cliquez sur **Ouvrir**.

    ![Créer un modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Dans **Concepteur web** > **Modèles**, cliquez sur **Ajouter**.

    ![Créer un modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. Dans **Nouveau modèle**, tapez un nom de modèle, puis sélectionnez un fichier Power BI Desktop.

    ![Boîte de dialogue Nouveau modèle dans le portail Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. Dans **Importer**, recherchez et sélectionnez votre fichier.

     ![Boîte de dialogue Connexion dans le portail Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Modifier les informations d’identification

Quand vous importez un modèle de données à partir d’un fichier pbix, par défaut, les informations d’identification utilisées pour se connecter à une source de données sont définies sur ServiceAccount. Après l’importation d’un modèle à partir d’un fichier pbix, vous pouvez changer les informations d’identification à l’aide des méthodes suivantes :

- Utilisez SSMS version 17.8.1 (juillet 2018) ou ultérieure pour modifier les informations d’identification. Il s’agit du moyen le plus simple.
- Utilisez la [commande alter](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) TMSL sur [l’objet DataSources](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) pour modifier la propriété de chaîne de connexion. 
- Ouvrez le modèle dans Visual Studio, modifiez les informations d’identification pour la connexion à la source de données, puis redéployez le modèle.

Pour changer les informations d’identification à l’aide de SSMS 

1. Dans SSMS, développez la base de données > **Connexions**. 
2. Cliquez avec le bouton droit sur la connexion de base de données, puis cliquez sur **Actualiser les informations d’identification**. 

    ![Actualiser les informations d’identification](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. Dans la boîte de dialogue des informations d’identification, sélectionnez un type d’informations d’identification et entrez les informations d’identification. Pour l’authentification SQL, sélectionnez Base de données. Pour le compte d’organisation (OAuth), sélectionnez Compte Microsoft.
    ![Modifier les informations d’identification](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

La version de juillet 2018 de Power BI Desktop inclut une nouvelle fonctionnalité pour la modification des autorisations de source de données. Sous l’onglet **Accueil**, cliquez sur **Modifier les requêtes**  > **Paramètres de la source de données**. Sélectionnez la connexion à la source de données, puis cliquez sur **Modifier les autorisations**.


## <a name="see-also"></a>Voir aussi

[Créer un modèle dans le portail Azure](analysis-services-create-model-portal.md)   
[Se connecter à un serveur Azure Analysis Services](analysis-services-connect.md)  
