---
title: Analyser des données dans Azure Data Lake Storage Gen2 à l’aide de Power BI | Microsoft Docs
description: Utilisez Power BI pour analyser des données stockées dans Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: 156999c6b6d8451516ac1c0f095e1a864420d0b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354829"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Analyser des données dans Azure Data Lake Storage Gen2 à l’aide de Power BI

Dans cet article, vous allez apprendre à utiliser Power BI Desktop pour analyser et visualiser les données stockées dans un compte de stockage doté d’un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.
> Cet article suppose que vous avez déjà créé un compte nommé `myadlsg2`.
> * Vous bénéficiez de l’un des rôles suivants pour le compte de stockage : **Lecteur de données blob**, **Contributeur aux données blob** ou **Propriétaire des données Blob**.
> * Un exemple de fichier de données nommé `Drivers.txt`, situé dans votre compte de stockage.
> Vous pouvez télécharger cet exemple à partir du [Référentiel Azure Data Lake Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt), puis charger ce fichier dans votre compte de stockage.
> * **Power BI Desktop**. Vous pouvez le télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Création d’un rapport dans Power BI Desktop

1. Lancez Power BI Desktop sur votre ordinateur.
2. À partir de l’onglet **Accueil** du ruban, cliquez sur **Obtenir les données**, puis sur **Plus**.
3. Dans la boîte de dialogue **Obtenir les données**, cliquez sur **Azure**, cliquez sur **Azure Data Lake Store Gen2 (bêta)** , puis sur **Connexion**.

    ![Obtenir la page de données](media/data-lake-storage-use-power-bi/get-data-page.png)

4. Dans la boîte de dialogue **Azure Data Lake Storage Gen2**, vous pouvez indiquer l’URL de votre compte, conteneur ou sous-répertoire Azure Data Lake Storage Gen2 à l’aide du format de point de terminaison du conteneur. Les URL de Data Lake Storage Gen2 ont le modèle `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` suivant, puis cliquez sur **OK**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. Dans la boîte de dialogue suivante, cliquez sur **Se connecter** pour vous connecter à votre compte de stockage. Vous êtes redirigé vers la page de connexion de votre organisation. Suivez les instructions de l’invite pour vous connecter au compte.

    ![Page de connexion](media/data-lake-storage-use-power-bi/sign-in.png)

6. Une fois que vous vous êtes connecté, cliquez sur **Connexion**.

    ![Page de connexion](media/data-lake-storage-use-power-bi/signed-in.png)

7. La boîte de dialogue suivante affiche tous les fichiers sous l’URL que vous avez fournie à l’étape 4 ci-dessus, y compris le fichier que vous avez chargé dans votre compte de stockage. Vérifiez les informations, puis cliquez sur **Charger**.

    ![Systèmes de fichiers](media/data-lake-storage-use-power-bi/file-systems.png)

8. Une fois que les données ont été chargées correctement dans Power BI, les champs suivants s’affichent dans l’onglet **Champs** .

    ![Onglet Champs](media/data-lake-storage-use-power-bi/fields.png)

    Toutefois, pour visualiser et analyser les données, nous préférons que les données soient disponibles dans les champs suivants.

    ![Champs](media/data-lake-storage-use-power-bi/preferred-fields.png)

    Dans les étapes suivantes, nous mettrons à jour la requête pour convertir les données importées dans le format souhaité.

9. À partir de l’onglet **Accueil** du ruban, cliquez sur **Modifier les requêtes**.

    ![Requêtes](media/data-lake-storage-use-power-bi/queries.png)

10. Dans l’**éditeur de requête**, sous la colonne **Contenu**, cliquez sur **Binaire**. Le fichier sera automatiquement détecté en tant que CSV et vous devriez voir une sortie comme celle indiquée ci-dessous. Vos données sont désormais disponibles dans un format que vous pouvez utiliser pour créer des visualisations.

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. À partir de l’onglet **Accueil** du ruban, cliquez sur **Fermer** et sur **Appliquer**, puis cliquez sur **Fermer** et sur **Appliquer**.

    ![Fermer et appliquer](media/data-lake-storage-use-power-bi/close-apply.png)

12. Une fois que la requête est mise à jour, l’onglet **Champs** affiche les nouveaux champs disponibles pour la visualisation.

    ![Nouveaux champs](media/data-lake-storage-use-power-bi/new-fields.png)

13. Créons un graphique à secteurs pour représenter les pilotes dans chaque ville d’un pays donné. Pour ce faire, effectuez les sélections suivantes.

    Dans l’onglet **Visualisations**, cliquez sur le symbole du graphique à secteurs.

    ![Visualisations](media/data-lake-storage-use-power-bi/visualizations.png)

    Les colonnes que nous allons utiliser sont la colonne 4 (nom de la ville) et la colonne 7 (nom du pays). Faites glisser ces colonnes de l’onglet **Champs** vers l’onglet **Visualisations** comme indiqué ci-dessous.

    ![Faire glisser les champs](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Le graphique à secteurs doit maintenant ressembler à celui illustré ci-dessous.

    ![Graphique à secteurs](media/data-lake-storage-use-power-bi/pie-chart.png)

14. En sélectionnant un pays spécifique dans les filtres au niveau de la page, vous pouvez maintenant voir le nombre de pilotes dans chaque ville du pays sélectionné. Par exemple, sous l’onglet **Visualisations**, sous **Filtres au niveau de la page**, sélectionnez **Brésil**.

    ![Filtres de page](media/data-lake-storage-use-power-bi/page-filters.png)

15. Le graphique à secteurs est automatiquement mis à jour pour afficher les pilotes dans les villes du Brésil.

    ![Brésil](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. Dans le menu **Fichier**, cliquez sur **Enregistrer** pour enregistrer la visualisation sous forme de fichier Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publication d’un rapport dans le service Power BI

Après avoir créé les visualisations dans Power BI Desktop, vous pouvez les partager avec d’autres utilisateurs en les publiant sur le service Power BI. Pour obtenir des instructions, consultez la page [Publier à partir de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).
