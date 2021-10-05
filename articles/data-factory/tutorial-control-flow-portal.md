---
title: Activités de création de branches et de chaînage dans un pipeline à l’aide du portail Azure
description: Découvrez comment contrôler le flux de contrôle des données dans le pipeline Azure Data Factory à l’aide du Portail Azure.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: e3ed05264ab942383d2828384d36f2a5817ddbe6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806047"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Création de branches et de chaînage pour les activités dans un pipeline Azure Data Factory à l’aide du portail Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce didacticiel, vous créez un pipeline Data Factory qui présente certaines des fonctionnalités de flux de contrôle. Ce pipeline est une simple copie depuis un conteneur Stockage Blob Azure vers un autre conteneur dans le même compte de stockage. Si l’activité de copie réussit, le pipeline envoie les détails de l’opération de copie réussie (par exemple, la quantité de données écrites) dans un e-mail d’avis de réussite. Si l’activité de copie échoue, le pipeline envoie les détails de l’échec de la copie (par exemple, le message d’erreur) dans un e-mail d’avis d’échec. Tout au long de ce didacticiel, vous allez apprendre à passer des paramètres.

Vue d’ensemble du scénario : :::image type="content" source="media/tutorial-control-flow-portal/overview.png" alt-text="Le diagramme montre le Stockage Blob Azure qui est la cible d’une copie, laquelle, en cas de réussite, envoie un e-mail avec des détails ou, en cas d’échec, envoie un e-mail avec les détails de l’erreur.":::

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un service lié Stockage Azure
> * Créer un jeu de données d’objet blob Azure
> * Créer un pipeline contenant une activité de copie et une activité web
> * Envoyer les sorties des activités aux activités ultérieures
> * Utiliser des variables système et de passage de paramètres
> * Démarrer une exécution de pipeline
> * Surveiller les exécutions de pipeline et d’activité

Ce tutoriel utilise le portail Azure. Vous pouvez utiliser d’autres mécanismes pour interagir avec Azure Data Factory. Consultez « Guides de démarrage rapide » dans la table des matières.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Compte Stockage Azure**. Vous utilisez le stockage blob comme magasins de données **source**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-account-create.md) pour découvrir comment en créer un.
* **Azure SQL Database**. Vous utilisez la base de données en tant que magasin de données **récepteur**. Si vous n’avez pas de base de données dans Azure SQL Database, consultez l’article [Créer une base de données dans Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) pour savoir comme en créer une.

### <a name="create-blob-table"></a>Créer la table d’objets blob

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **input.txt** sur votre disque.

    ```
    John,Doe
    Jane,Doe
    ```
2. Utilisez des outils tels que l’ [Explorateur de stockage Azure](https://storageexplorer.com/) pour effectuer les étapes suivantes :
    1. Créer le conteneur **adfv2branch**.
    2. Créer le dossier **entrée** dans le conteneur **adfv2branch**.
    3. Télécharger le fichier **input.txt** au conteneur.

## <a name="create-email-workflow-endpoints"></a>Créer des points de terminaison de flux de travail d’e-mail
Pour déclencher l’envoi d’un e-mail depuis le pipeline, vous utilisez [Logic Apps](../logic-apps/logic-apps-overview.md) pour définir le flux de travail. Pour plus d’informations sur la création d’un flux de travail Logic Apps, consultez [Guide pratique pour créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Flux de travail d’un e-mail d’avis de réussite
Créez un flux de travail Logic Apps nommé `CopySuccessEmail`. Définissez le déclencheur du flux de travail comme `When an HTTP request is received` et ajoutez une action `Office 365 Outlook – Send an email`.

:::image type="content" source="media/tutorial-control-flow-portal/success-email-workflow.png" alt-text="Flux de travail d’un e-mail d’avis de réussite":::

Pour le déclencheur de votre requête, renseignez `Request Body JSON Schema` avec le texte JSON suivant :

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

La requête dans le concepteur d’application logique doit ressembler à l’image suivante :

:::image type="content" source="media/tutorial-control-flow-portal/logic-app-designer-request.png" alt-text="Concepteur d’application logique - requête":::

Pour l’action **Envoyer un e-mail**, personnalisez le mode de mise en forme de l’e-mail, en utilisant les propriétés passées dans le schéma JSON du corps de la requête. Voici un exemple :

:::image type="content" source="media/tutorial-control-flow-portal/send-email-action-2.png" alt-text="Concepteur d’application logique - action Envoyer un e-mail":::

Enregistrez le workflow. Prenez note de l’URL de votre requête HTTP Post pour votre flux de travail d’e-mail d’avis de réussite :

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Flux de travail d’un e-mail d’avis d’échec
Suivez les mêmes étapes pour créer un autre flux de travail Logic Apps **CopyFailEmail**. Dans le déclencheur de la requête, `Request Body JSON schema` est identique. Modifiez la mise en forme de votre e-mail, notamment `Subject`, pour l’adapter à un avis d’échec. Voici un exemple :

:::image type="content" source="media/tutorial-control-flow-portal/fail-email-workflow-2.png" alt-text="Concepteur d’application logique - flux de travail d’un e-mail d’avis d’échec":::

Enregistrez le workflow. Prenez note de l’URL de votre requête HTTP Post pour votre flux de travail d’e-mail d’avis d’échec :

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Vous devez maintenant avoir deux URL de flux de travail :

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Données + Analytique** > **Data Factory** :

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="Sélection de Data Factory dans le volet &quot;Nouveau&quot;":::

2. Dans la page **Nouvelle fabrique de données**, entrez **ADFTutorialDataFactory** comme **nom**.

     :::image type="content" source="./media/tutorial-control-flow-portal/new-azure-data-factory.png" alt-text="Page Nouvelle fabrique de données":::

   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomADFTutorialDataFactory), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory.

   *Le nom de fabrique de données « ADFTutorialDataFactory » n’est pas disponible*.

3. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données.
4. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes :

      - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante.
      - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.   
         
        Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).  
4. Sélectionnez **V2** pour la **version**.
5. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent être proposés dans d’autres régions.
6. Sélectionnez **Épingler au tableau de bord**.     
7. Cliquez sur **Créer**.      
8. Sur le tableau de bord, vous voyez la vignette suivante avec l’état : **Déploiement de Data Factory**.

    :::image type="content" source="media/tutorial-control-flow-portal/deploying-data-factory.png" alt-text="mosaïque déploiement de fabrique de données":::
9. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image.

   :::image type="content" source="./media/tutorial-control-flow-portal/data-factory-home-page.png" alt-text="Page d’accueil Data Factory":::
10. Cliquez sur la vignette **Créer et surveiller** pour lancer l’interface utilisateur d’Azure Data Factory dans un onglet séparé.


## <a name="create-a-pipeline"></a>Créer un pipeline
Dans cette étape, vous allez créer un pipeline avec une activité de copie et deux activités web. Vous utilisez les fonctionnalités suivantes pour créer le pipeline :

- Paramètres du pipeline auxquels les jeux de données ont accès.
- Activité Web pour appeler des flux de travail Logic Apps pour envoyer des messages de réussite/échec.
- Connexion d’une activité à une autre activité (cas de réussite et échec)
- Utilisation de la sortie d’une activité en tant qu’entrée de l’activité suivante

1. Dans la page d’accueil de l’interface utilisateur Data Factory, cliquez sur la vignette **Orchestrer**.  

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::
3. Dans la fenêtre Propriétés pour le pipeline, basculez vers l’onglet **Paramètres** et utilisez le bouton **Nouveau** pour ajouter les trois paramètres suivants de type chaîne : sourceBlobContainer, sinkBlobContainer et récepteur.

    - **sourceBlobContainer** - paramètre dans le pipeline consommé par le jeu de données d’objet blob source.
    - **sinkBlobContainer** - paramètre dans le pipeline consommé par le jeu de données d’objet blob récepteur
    - **receiver** - ce paramètre est utilisé par les deux activités web dans le pipeline qui envoient des e-mails de réussite ou d’échec au récepteur dont l’adresse e-mail est spécifiée par ce paramètre.

   :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-parameters.png" alt-text="Menu Nouveau pipeline":::
4. Dans la boîte à outils **Activités**, développez **Flux de données** et glissez-déposez l’activité de **copie** vers la surface du concepteur de pipeline.

   :::image type="content" source="./media/tutorial-control-flow-portal/drag-drop-copy-activity.png" alt-text="Glisser-déposer de l’activité de copie":::
5. Dans la fenêtre **Propriétés** pour l’activité de **copie** en bas, basculez vers l’onglet **Source**, puis cliquez sur **+ Nouveau**. Vous créez un jeu de données source pour l’activité de copie dans cette étape.

   :::image type="content" source="./media/tutorial-control-flow-portal/new-source-dataset-button.png" alt-text="Capture d’écran montrant comment créer un jeu de données source pour l’activité de copie.":::
6. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**.

   :::image type="content" source="./media/tutorial-control-flow-portal/select-azure-blob-storage.png" alt-text="Sélectionner le stockage Blob Azure":::
7. Vous voyez un nouvel **onglet** intitulé **AzureBlob1**. Modifiez le nom du jeu de données à **SourceBlobDataset**.

   :::image type="content" source="./media/tutorial-control-flow-portal/dataset-general-page.png" alt-text="Paramètres généraux du jeu de données":::
8. Basculez vers l’onglet **Connexion** dans la fenêtre **Propriétés**, puis cliquez sur Nouveau pour **Service lié**. Vous créez un service lié qui relie votre compte de stockage Azure à la fabrique de données à cette étape.

   :::image type="content" source="./media/tutorial-control-flow-portal/dataset-connection-new-button.png" alt-text="Connexion à un jeu de données - nouveau service lié":::
9. Dans la fenêtre **Nouveau service lié**, procédez comme suit :

    1. Entrez **AzureStorageLinkedService** pour **Nom**.
    2. Sélectionnez votre compte de stockage Azure pour le **Nom du compte de stockage**.
    3. Cliquez sur **Enregistrer**.

   :::image type="content" source="./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png" alt-text="Nouveau service lié Stockage Azure":::
12. Saisissez `@pipeline().parameters.sourceBlobContainer` pour le dossier et `emp.txt` pour le nom du fichier. Le paramètre de pipeline sourceBlobContainer vous permet de définir le chemin d’accès de dossier pour le jeu de données.

    :::image type="content" source="./media/tutorial-control-flow-portal/source-dataset-settings.png" alt-text="Paramètres du jeu de données source":::

13. Basculez vers l’onglet **pipeline** (ou) cliquez sur le pipeline dans l’arborescence. Vérifiez que **SourceBlobDataset** est sélectionné comme **jeu de données source**.
      
   :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png" alt-text="Jeu de données source":::

13. Dans la fenêtre Propriétés, basculez vers l’onglet **Récepteur**, puis cliquez sur **+ Nouveau** pour **Jeu de données récepteur**. Vous créez un jeu de données récepteur pour l’activité de copie dans cette étape d’une façon similaire à la création du jeu de données source.

    :::image type="content" source="./media/tutorial-control-flow-portal/new-sink-dataset-button.png" alt-text="Bouton Nouveau jeu de données récepteur":::
14. Dans la fenêtre **Nouveau jeu de données**, sélectionnez **Stockage Blob Azure**, puis cliquez sur **Terminer**.
15. Sur la page de paramètres **Général** pour le jeu de données, entrez **SinkBlobDataset** comme **nom**.
16. Basculez vers l’onglet **Connexions**, et procédez comme suit :

    1. Sélectionnez **AzureStorageLinkedService** comme **Service lié**.
    2. Saisissez `@pipeline().parameters.sinkBlobContainer` pour le dossier.
    1. Entrez `@CONCAT(pipeline().RunId, '.txt')` pour le nom de fichier. L’expression utilise l’ID de l’exécution de pipeline actuelle comme nom de fichier. Pour obtenir la liste des variables système et expressions prises en charge, consultez [Variables système](control-flow-system-variables.md) et [Langage d’expression](control-flow-expression-language-functions.md).

        :::image type="content" source="./media/tutorial-control-flow-portal/sink-dataset-settings.png" alt-text="Paramètres du jeu de données récepteur":::
17. Basculez vers l’onglet **Pipeline** en haut. Développez **Général** dans la boîte à outils **Activités**, et glissez-déposez une activité **Web** vers la surface du concepteur de pipeline. Définissez le nom de l’activité sur **SendSuccessEmailActivity**. L’activité web permet d’appeler n’importe quel point de terminaison REST. Pour plus d’informations sur l’activité, consultez [Activité web](control-flow-web-activity.md). Ce pipeline utilise une activité web pour appeler le flux de travail d’un e-mail Logic Apps.

    :::image type="content" source="./media/tutorial-control-flow-portal/success-web-activity-general.png" alt-text="Glisser-déposer la première activité Web":::
18. Basculez vers l’onglet **Paramètres** à partir de l’onglet **Général**, et procédez comme suit :
    1. Pour l’**URL**, spécifiez l’URL du flux de travail Logic Apps qui envoie l’e-mail de réussite.  
    2. Sélectionnez **POST** comme **méthode**.
    3. Cliquez sur le lien **+ Ajouter un en-tête** dans la section **En-têtes**.
    4. Ajoutez un en-tête **Content-Type** et affectez-lui la valeur **application/json**.
    5. Spécifiez le texte JSON suivant pour le **corps**.

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        Le corps du message contient les propriétés suivantes :

       - Message - Passage de la valeur `@{activity('Copy1').output.dataWritten`. Accède à une propriété de l’activité de copie précédente et passe la valeur de dataWritten. Pour un échec, passez la sortie de l’erreur au lieu de `@{activity('CopyBlobtoBlob').error.message`.
       - Nom de la fabrique de données - Passage de la valeur `@{pipeline().DataFactory}`. Il s’agit d’une variable système, qui vous permet d’accéder au nom de la fabrique de données correspondante. Consultez l’article [Variables système](control-flow-system-variables.md) pour obtenir la liste des variables système.
       - Nom du pipeline : Passage de la valeur `@{pipeline().Pipeline}`. Il s’agit également d’une variable système, qui vous permet d’accéder au nom du pipeline correspondant.
       - Récepteur - Passage de la valeur "\@pipeline().parameters.receiver"). Accès aux paramètres de pipeline.

         :::image type="content" source="./media/tutorial-control-flow-portal/web-activity1-settings.png" alt-text="Paramètres pour la première activité Web":::         
19. Connectez l’activité de **copie** à l’activité **Web** en faisant glisser le bouton vert proche l’activité de copie vers l’activité Web.

    :::image type="content" source="./media/tutorial-control-flow-portal/connect-copy-web-activity1.png" alt-text="Connecter l’activité de copie avec la première activité Web":::
20. Faites glisser une autre activité **Web** à partir de la boîte à outils Activités vers la zone du concepteur de pipeline et configurez le **nom** comme **SendFailureEmailActivity**.

    :::image type="content" source="./media/tutorial-control-flow-portal/web-activity2-name.png" alt-text="Nom de la deuxième activité Web":::
21. Basculez vers l’onglet **Paramètres**, et procédez comme suit :

    1. Pour l’**URL**, spécifiez l’URL du flux de travail Logic Apps qui envoie l’e-mail d’échec.  
    2. Sélectionnez **POST** comme **méthode**.
    3. Cliquez sur le lien **+ Ajouter un en-tête** dans la section **En-têtes**.
    4. Ajoutez un en-tête **Content-Type** et affectez-lui la valeur **application/json**.
    5. Spécifiez le texte JSON suivant pour le **corps**.

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        :::image type="content" source="./media/tutorial-control-flow-portal/web-activity2-settings.png" alt-text="Paramètres pour la deuxième activité Web":::         
22. Sélectionnez l’activité de **copie** dans le concepteur de pipeline, puis cliquez sur le bouton **+->** , puis sélectionnez **Erreur**.  

    :::image type="content" source="./media/tutorial-control-flow-portal/select-copy-failure-link.png" alt-text="Capture d’écran montrant comment sélectionner une erreur sur l’activité de copie dans le concepteur de pipeline.":::
23. Faites glisser le bouton **rouge** proche de l’activité de copie vers la deuxième activité Web **SendFailureEmailActivity**. Vous pouvez déplacer les activités afin que le pipeline ressemble à l’image suivante :

    :::image type="content" source="./media/tutorial-control-flow-portal/full-pipeline.png" alt-text="Pipeline complète avec toutes les activités":::
24. Pour valider le pipeline, cliquez sur le bouton **Valider** dans la barre d’outils. Fermez la fenêtre **Sortie de validation du pipeline** en cliquant sur le bouton **>>** .

    :::image type="content" source="./media/tutorial-control-flow-portal/validate-pipeline.png" alt-text="Valider le pipeline":::
24. Pour publier les entités (jeux de données, pipelines, etc.) sur Data Factory, cliquez sur **Publish All** (Publier tout). Patientez jusqu’à voir le message **Publication réussie**.

    :::image type="content" source="./media/tutorial-control-flow-portal/publish-button.png" alt-text="Publier":::

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Déclencher une exécution de pipeline qui réussit
1. Pour **déclencher** une exécution de pipeline, cliquez sur **Déclencher** dans la barre d’outils, puis sur **Déclencher maintenant**.

    :::image type="content" source="./media/tutorial-control-flow-portal/trigger-now-menu.png" alt-text="Déclencher une exécution du pipeline":::
2. Dans la fenêtre **Exécution de pipeline**, procédez comme suit :

    1. Entrez **adftutorial/adfv2branch/input** pour le paramètre **sourceBlobContainer**.
    2. Entrez **adftutorial/adfv2branch/output** pour le paramètre **sinkBlobContainer**.
    3. Entrez une **adresse e-mail** du **récepteur**.
    4. Cliquez sur **Terminer**

        :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-run-parameters.png" alt-text="Paramètres de l’exécution de pipeline":::

## <a name="monitor-the-successful-pipeline-run"></a>Surveiller l’exécution de pipeline réussie

1. Pour surveiller l’exécution de pipeline, basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez l’exécution de pipeline que vous avez déclenchée manuellement. Utilisez le bouton **Actualiser** pour actualiser la liste.

    :::image type="content" source="./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png" alt-text="Exécution de pipeline réussie":::
2. Pour **voir les exécutions d’activité** associées à l’exécution de ce pipeline, cliquez sur le premier lien dans la colonne **Actions**. Vous pouvez revenir à la vue précédente en cliquant sur **Pipelines** en haut. Utilisez le bouton **Actualiser** pour actualiser la liste.

    :::image type="content" source="./media/tutorial-control-flow-portal/activity-runs-success.png" alt-text="Capture d’écran montrant comment afficher la liste des exécutions d’activités.":::

## <a name="trigger-a-pipeline-run-that-fails"></a>Déclencher une exécution de pipeline qui échoue
1. Basculez vers l’onglet **Modifier** sur la gauche.
2. Pour **déclencher** une exécution de pipeline, cliquez sur **Déclencher** dans la barre d’outils, puis sur **Déclencher maintenant**.
3. Dans la fenêtre **Exécution de pipeline**, procédez comme suit :

    1. Entrez **adftutorial/dummy/input** pour le paramètre **sourceBlobContainer**. Assurez-vous que le dossier factice n’existe pas dans le conteneur adftutorial.
    2. Entrez **adftutorial/dummy/output** pour le paramètre **sinkBlobContainer**.
    3. Entrez une **adresse e-mail** du **récepteur**.
    4. Cliquez sur **Terminer**.

## <a name="monitor-the-failed-pipeline-run"></a>Surveiller l’exécution du pipeline qui a échoué

1. Pour surveiller l’exécution de pipeline, basculez vers l’onglet **Surveiller** sur la gauche. Vous voyez l’exécution de pipeline que vous avez déclenchée manuellement. Utilisez le bouton **Actualiser** pour actualiser la liste.

    :::image type="content" source="./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png" alt-text="Exécution de pipeline qui a échoué":::
2. Cliquez sur le lien **Erreur** pour l’exécution de pipeline afin d’afficher des détails sur l’erreur de pipeline.

    :::image type="content" source="./media/tutorial-control-flow-portal/pipeline-error-message.png" alt-text="Erreur de pipeline":::
2. Pour **voir les exécutions d’activité** associées à l’exécution de ce pipeline, cliquez sur le premier lien dans la colonne **Actions**. Utilisez le bouton **Actualiser** pour actualiser la liste. Notez que l’activité de copie dans le pipeline a échoué. L’activité Web a réussi à envoyer l’e-mail d’échec au destinataire spécifié.

    :::image type="content" source="./media/tutorial-control-flow-portal/activity-runs-failure.png" alt-text="Exécutions d’activités":::
4. Cliquez sur le lien **Erreur** dans la colonne **Actions** afin d’afficher des détails sur l’erreur.

    :::image type="content" source="./media/tutorial-control-flow-portal/activity-run-error.png" alt-text="Erreur d’exécution d’activité":::

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez effectué les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un service lié Stockage Azure
> * Créer un jeu de données d’objet blob Azure
> * Créer un pipeline qui contient une activité de copie et une activité web
> * Envoyer les sorties des activités aux activités ultérieures
> * Utiliser des variables système et de passage de paramètres
> * Démarrer une exécution de pipeline
> * Surveiller les exécutions de pipeline et d’activité

Vous pouvez maintenant passer à la section Concepts pour plus d’informations sur Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines et activités](concepts-pipelines-activities.md)
