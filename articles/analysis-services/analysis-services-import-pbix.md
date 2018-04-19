---
title: Importer un fichier Power BI Desktop dans Azure Analysis Services | Microsoft Docs
description: Décrit comment importer un fichier Power BI Desktop (pbix) à l’aide du portail Azure.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/03/2018
ms.author: owend
ms.openlocfilehash: 2ba9bc0e4b9a55312875fe120ee179800aeefb23
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importer un fichier Power BI Desktop

Vous pouvez créer un modèle dans Azure AS en important un fichier Power BI Desktop (pbix). Les connexions de sources de données, les données en cache et les métadonnées de modèle sont importées. Les rapports et les visualisations ne sont pas importés.

**Restrictions**   
- Le modèle pbix peut se connecter aux sources de données Azure SQL Database et Azure SQL Data Warehouse uniquement. 
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
