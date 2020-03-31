---
title: Se connecter à Azure Analysis Services avec Power BI | Microsoft Docs
description: Découvrez comment vous connecter à un serveur Azure Analysis Services à l’aide de Power BI. Une fois connecté, les utilisateurs peuvent explorer les données du modèle.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6205c4189abfefc2ee9c4a273ebfd6773ea609b6
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411355"
---
# <a name="connect-with-power-bi"></a>Se connecter avec Power BI

Une fois que vous avez créé un serveur dans Azure avant d’y déployer un modèle tabulaire, les utilisateurs de votre entreprise sont prêts à s’y connecter et à explorer les données. 

> [!TIP]
> Assurez-vous d’utiliser la dernière version de [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Se connecter à Power BI Desktop

1. Dans Power BI Desktop, cliquez sur **Obtenir les données** > **Azure** > **Base de données Azure Analysis Services**.

2. Dans **Serveur**, entrez le nom du serveur. Veillez à inclure l’URL complète, par exemple, asazure://westcentralus.asazure.windows.net/advworks.

3. Dans **Base de données**, si vous connaissez le nom de la base de données de modèle tabulaire ou de la perspective à laquelle vous souhaitez vous connecter, collez-le ici. Sinon, vous pouvez laisser ce champ vide et sélectionner une base de données ou une perspective ultérieurement.

4. Sélectionnez une option de connexion, puis appuyez sur **Connexion**. 

    Les deux options **Connexion directe** et **Importation** sont prises en charge. Toutefois, nous recommandons d’utiliser des connexions directes, car le mode d’importation présente des limitations. Plus particulièrement, les performances du serveur pourraient être affectées lors de l’importation. De même, si le modèle doit être actualisé dans le service Power BI, le paramètre **Autoriser l’accès à partir de Power BI** s’applique uniquement lors de la sélection de **Connexion directe**.

5. Entrez vos informations d’identification si elles vous sont demandées. 

6. Dans le **Navigateur**, développez le serveur, puis sélectionnez le modèle ou la perspective auquel vous souhaitez vous connecter, puis cliquez sur **Se connecter**. Cliquez sur un modèle ou une perspective pour afficher tous les objets de cette vue.

    Le modèle s’ouvre dans Power BI Desktop avec un rapport vierge dans l’affichage Rapport. La liste Champs affiche tous les objets du modèle qui ne sont pas cachés. L’état de la connexion se trouve dans le coin inférieur droit.

## <a name="connect-in-power-bi-service"></a>Se connecter à Power BI Desktop (service)

1. Créez un fichier Power BI Desktop qui inclut une connexion active à votre modèle sur votre serveur.
2. Dans [Power BI](https://powerbi.microsoft.com), cliquez sur **Obtenir les données** > **Fichiers**, puis trouvez et sélectionnez votre fichier .pbix.

## <a name="see-also"></a>Voir aussi
[Connect to an Azure Analysis Services server](analysis-services-connect.md)  (Se connecter à un serveur Azure Analysis Services)  
[Fournisseurs de données pour la connexion à Azure Analysis Services](analysis-services-data-providers.md)

