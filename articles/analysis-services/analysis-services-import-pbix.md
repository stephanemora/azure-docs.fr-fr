---
title: Importer un fichier Power BI Desktop dans Azure Analysis Services | Microsoft Docs
description: Décrit comment importer un fichier Power BI Desktop (pbix) à l’aide du portail Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601069"
---
# <a name="import-a-power-bi-desktop-file"></a>Importer un fichier Power BI Desktop

Vous pouvez importer un modèle de données dans un fichier Power BI Desktop (pbix) dans Azure Analysis Services Les connexions de sources de données, les données en cache et les métadonnées de modèle sont importées. Les rapports et les visualisations ne sont pas importés. Les modèles de donnée importés depuis Power BI Desktop ne sont qu’au niveau de compatibilité 1400.

**Restrictions**   
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

## <a name="see-also"></a>Voir aussi

[Créer un modèle dans le portail Azure](analysis-services-create-model-portal.md)   
[Se connecter à un serveur Azure Analysis Services](analysis-services-connect.md)  
