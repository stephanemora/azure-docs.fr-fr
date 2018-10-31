---
title: Importer un fichier Power BI Desktop dans Azure Analysis Services | Microsoft Docs
description: Décrit comment importer un fichier Power BI Desktop (pbix) à l’aide du portail Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8882a44b0b6db1b3c23c017a072ebddfe9aa20f5
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090862"
---
# <a name="import-a-power-bi-desktop-file"></a>Importer un fichier Power BI Desktop

Vous pouvez importer un modèle de données dans un fichier Power BI Desktop (pbix) dans Azure Analysis Services Les connexions de sources de données, les données en cache et les métadonnées de modèle sont importées. Les rapports et les visualisations ne sont pas importés. Les modèles de donnée importés depuis Power BI Desktop ne sont qu’au niveau de compatibilité 1400.

> [!IMPORTANT]
> Cette fonctionnalité est dépréciée. Elle peut être supprimée ou grandement remaniée dans une prochaine mise à jour. Il est recommandé d’interrompre l’utilisation de cette fonctionnalité dans les projets existants ou nouveaux, afin d’assurer la compatibilité avec les futures mises à jour. Pour un développement et un test de modèles plus avancés, utilisez plutôt Visual Studio (SSDT) et SQL Server Management Studio (SSMS).

**Restrictions**   


- Si votre modèle de données est créé dans la mise à jour de Power BI Desktop de juillet 2018 (2.60.5169.3201) ou ultérieure, vérifiez qu’aucune fonctionnalité en préversion n’est activée. Les fonctionnalités en préversion ne sont pas encore prises en charge dans Azure Analysis Services. Si vous recevez l’erreur suivante lors de l’importation, c’est que le fichier pbix a des fonctionnalités en préversion activées qui ne sont pas encore prises en charge dans Azure Analysis Services.

    ![Avertissement de niveau de compatibilité](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- Vous devez disposer des autorisations d’administrateur de serveur pour importer à partir d’un fichier pbix.
- Le modèle pbix peut se connecter aux sources de données **Azure SQL Database** et **Azure SQL Data Warehouse** uniquement.
- Le modèle pbix ne peut pas avoir de connexions DirectQuery ou en temps réel. 


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
