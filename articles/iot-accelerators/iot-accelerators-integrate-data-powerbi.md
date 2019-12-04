---
title: Visualiser des données de supervision à distance à l’aide de Power BI - Azure | Microsoft Docs
description: Ce didacticiel utilise Power BI Desktop et Cosmos DB pour intégrer les données d’une solution de supervision à distance dans une visualisation personnalisée. Ainsi, les utilisateurs peuvent créer leurs propres tableaux de bord personnalisés et les partager avec des personnes qui ne se servent pas de cette solution.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184236"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualiser des données de surveillance à distance à l’aide de Power BI

Ce tutoriel vous montre comment connecter vos données de solution de supervision à distance CosmosDB à Power BI. Une fois la connexion établie, vous pouvez créer vos propres tableaux de bord personnalisés et les ajouter à votre tableau de bord de solution de supervision à distance. Ce flux de travail permet la création de graphes plus spécialisés, en plus de ceux prêts à l’emploi. Vous pouvez ensuite utiliser ce tutoriel pour intégrer des données à d’autres flux de données ou pour créer des tableaux de bord personnalisés dont les données doivent être utilisées en dehors de votre solution de supervision à distance. La création de tableaux de bord dans Power BI vous permet également de faire interagir les panneaux lorsque vous sélectionnez des éléments. Par exemple, vous pourriez avoir un filtre qui affiche seulement les informations concernant vos camions simulés. Tous les éléments de votre tableau de bord interagiraient donc de manière à afficher uniquement les informations concernant les camions simulés. Si vous souhaitez utiliser un autre outil que Power BI, vous pouvez également étendre ces étapes pour utiliser l’outil de visualisation de votre choix et vous connecter à Cosmos DB ou à une base de données personnalisée si vous en avez une. 

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’une solution de supervision à distance en cours d’exécution.
- Vous devez avoir accès au [Portail Azure](https://portal.azure.com) et à l’abonnement dans lequel IoT Hub et la Solution sont exécutés
- [Power BI Desktop](https://powerbi.microsoft.com) doit être installé (n’importe quelle version)


## <a name="information-needed-from-azure-portal"></a>Informations du Portail Azure nécessaires

1. Accédez au [Portail Azure](https://portal.azure.com) et connectez-vous, si nécessaire

2. Dans le panneau gauche, cliquez sur Groupes de ressources.

    ![Panneau gauche](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Accédez au groupe de ressources dans lequel votre solution IoT est exécutée, puis cliquez pour accéder à la page de présentation du groupe de ressources. 

4. Dans la page de présentation, cliquez sur l’élément « Compte Azure Cosmos DB » pour être redirigé vers la page de présentation du flux Cosmos DB de cette solution IoT.

    ![Groupe de ressources](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Dans le volet de gauche, cliquez sur la section « Clés », puis notez les valeurs suivantes pour les utiliser dans Power BI :

   - URI
   - Clé primaire

     ![clés](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Configuration du flux dans Power BI
  
1. Ouvrez l’application de bureau Power BI, puis cliquez sur « Obtenir des données » dans l’angle supérieur gauche. 

    ![Obtenir des données](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Lorsque vous êtes invité à entrer des données, choisissez de rechercher dans « Azure Cosmos DB », puis sélectionnez ce connecteur. Ce connecteur extrait des données directement de la base de données Cosmos de votre solution Azure IoT.
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Entrez les informations que vous avez enregistrées plus haut :

    * URI
    * Clé primaire

4. Sélectionnez toutes les tables à importer dans Power BI. Cette action va lancer le chargement des données. La durée de chargement des données (jusqu’à quelques heures) est proportionnelle à la durée d’exécution de la solution. 

    ![Importation des tables](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Une fois le chargement des données terminé, cliquez sur « Modifier les requêtes » sur la première ligne de Power BI, puis développez toutes les tables en cliquant sur les flèches situées dans la barre jaune de chaque table. En procédant ainsi, vous affichez toutes les colonnes. Notez que les données relatives à l’humidité, à la vitesse, etc. ne sont pas du bon type.

    ![Nouvelle colonne](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Par exemple, les données importées dans Power BI ont été converties en heure UNIX lorsqu’elles sont passées par le connecteur. Pour vous préparer à cette conversion, vous pouvez créer une colonne et utiliser cette équation pour la convertir au format date/heure : 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Table mise à jour](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received fait partie des colonnes au format UNIX, et elle peut être remplacée par d’autres colonnes nécessitant une conversion. 
  
    D’autres points de données ont été convertis au format String. Si nécessaire, ils peuvent être convertis au format Double ou Int à l’aide de la procédure indiquée ci-dessus.

## <a name="creating-a-dashboard"></a>Création d’un tableau de bord

Une fois le flux connecté, vous pouvez créer vos tableaux de bord personnalisés. Le tableau de bord ci-dessous montre des données de télémétrie obtenues par les appareils simulés, avec différents sélecteurs de vue tels que les suivants : 

* Emplacement des appareils sur une carte (à droite)
* Appareils, avec état et niveau de gravité (en haut à gauche)
* Appareils auxquels des règles sont appliquées, et pour lesquels des alertes sont générées (en bas à gauche)

![Visualisation Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publication du tableau de bord et actualisation des données

Une fois créés, il est recommandé de [publier vos tableaux de bord Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) en vue de les partager.

Vous devez également [actualiser les données](https://docs.microsoft.com/power-bi/refresh-data) du tableau de bord publié afin de vérifier que vous disposez bien du dernier jeu de données.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à visualiser les données de surveillance à distance à l’aide de Power BI.

Pour plus d’informations sur la personnalisation de la solution de supervision à distance, consultez :

* [Personnalisation de l’interface utilisateur de la solution de supervision à distance](iot-accelerators-remote-monitoring-customize.md)
* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

