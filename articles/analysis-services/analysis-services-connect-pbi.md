---
title: Se connecter à Azure Analysis Services avec Power BI | Microsoft Docs
description: Découvrez comment vous connecter à un serveur Azure Analysis Services à l’aide de Power BI. Une fois connecté, les utilisateurs peuvent explorer les données du modèle.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 5/25/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 579f97deaadf8005d7a7986ff3b032909c28972e
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110496558"
---
# <a name="connect-with-power-bi"></a>Se connecter avec Power BI

Une fois que vous avez créé un serveur dans Azure avant d’y déployer un modèle tabulaire, les utilisateurs de votre entreprise sont prêts à s’y connecter et à explorer les données. 

> [!NOTE]
> Si vous publiez un modèle Power BI Desktop sur le service Power BI, sur le serveur Azure Analysis Services, vérifiez que la propriété de serveur de classement Case-Sensitive (Respecte la casse) n’est pas sélectionnée (valeur par défaut). La propriété de serveur Case-Sensitive (Respecte la casse) peut être définie à l’aide de SQL Server Management Studio.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Se connecter à Power BI Desktop

1. Dans Power BI Desktop, cliquez sur **Obtenir les données** > **Azure** > **Base de données Azure Analysis Services**.

2. Dans **Serveur**, entrez le nom du serveur. Veillez à inclure l’URL complète, par exemple, asazure://westcentralus.asazure.windows.net/advworks.

3. Dans **Base de données**, si vous connaissez le nom de la base de données de modèle tabulaire ou de la perspective à laquelle vous souhaitez vous connecter, collez-le ici. Sinon, vous pouvez laisser ce champ vide et sélectionner une base de données ou une perspective ultérieurement.

4. Sélectionnez une option de connexion, puis appuyez sur **Connexion**. 

    Les deux options **Connexion directe** et **Importation** sont prises en charge. Toutefois, nous recommandons d’utiliser des connexions directes, car le mode d’importation présente des limitations. Plus particulièrement, les performances du serveur pourraient être affectées lors de l’importation.
    
    Si vous avez un modèle Power BI en [mode Stockage mixte](/power-bi/transform-model/desktop-composite-models), l'option **Connexion directe** est remplacée par l'option **[DirectQuery](/power-bi/connect-data/desktop-directquery-datasets-azure-analysis-services)** . Les connexions actives sont également automatiquement mises à niveau vers DirectQuery si le modèle passe du mode Importation au mode Stockage mixte.

5. Entrez vos informations d’identification si elles vous sont demandées. 

   > [!NOTE]
   > Les comptes à code secret à usage unique ne sont pas pris en charge. 

6. Dans le **Navigateur**, développez le serveur, puis sélectionnez le modèle ou la perspective auquel vous souhaitez vous connecter, puis cliquez sur **Se connecter**. Cliquez sur un modèle ou une perspective pour afficher tous les objets de cette vue.

    Le modèle s’ouvre dans Power BI Desktop avec un rapport vierge dans l’affichage Rapport. La liste Champs affiche tous les objets du modèle qui ne sont pas cachés. L’état de la connexion se trouve dans le coin inférieur droit.

## <a name="connect-in-power-bi-service"></a>Se connecter à Power BI Desktop (service)

1. Créez un fichier Power BI Desktop qui inclut une connexion active à votre modèle sur votre serveur.
2. Dans [Power BI](https://powerbi.microsoft.com), cliquez sur **Obtenir les données** > **Fichiers**, puis trouvez et sélectionnez votre fichier .pbix.

## <a name="request-memory-limit"></a>Limite de mémoire des requêtes

Pour préserver les performances du système, une limite de mémoire est appliquée à toutes les requêtes émises par les rapports Power BI sur Azure Analysis Services, quelle que soit la [Limite de mémoire des requêtes](/analysis-services/server-properties/memory-properties?view=azure-analysis-services-current&preserve-view=true) configurée sur le serveur Azure Analysis Services. Les utilisateurs doivent essayer de simplifier la requête ou ses calculs si la requête utilise trop de mémoire.

|                                                           | Limite de mémoire des requêtes |
|-----------------------------------------------------------|----------------------|
| Connexion active à partir de Power BI                            | 10 Go  |
| DirectQuery à partir d'un rapport Power BI dans un espace de travail partagé  | 1 Go   |
| DirectQuery à partir d'un rapport Power BI dans un espace de travail Premium | 10 Go  |
| Q&R Power BI | 100 Mo |

## <a name="see-also"></a>Voir aussi
[Connect to an Azure Analysis Services server](analysis-services-connect.md)  (Se connecter à un serveur Azure Analysis Services)  
[Fournisseurs de données pour la connexion à Azure Analysis Services](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
