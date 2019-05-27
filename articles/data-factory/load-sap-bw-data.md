---
title: Charger des données depuis SAP Business Warehouse à l’aide d’Azure Data Factory | Microsoft Docs
description: Utiliser Azure Data Factory pour copier des données à partir de SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 4cd61db3ec0e8d88c9b1c6d6ba427b120b3f1af1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66152413"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copier des données depuis SAP Business Warehouse à l’aide d’Azure Data Factory

Cet article explique comment utiliser Azure Data Factory pour copier des données à partir de SAP Business Warehouse (BW) via Open Hub dans Azure Data Lake Storage Gen2. Vous pouvez utiliser un processus similaire pour copier des données à d’autres [prise en charge des banques de données réceptrices](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Pour des informations générales sur la copie des données à partir de SAP BW, y compris l’intégration de SAP BW Open Hub et flux de delta d’extraction, consultez [copier des données depuis SAP Business Warehouse via Open Hub à l’aide d’Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Conditions préalables

- **Azure Data Factory**: Si vous n’en avez pas, suivez les étapes pour [créer une fabrique de données](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Hub Destination (OHD) avec le type de destination « Table de base de données »**: Pour créer un OHD ou pour vérifier que votre OHD est correctement configuré pour l’intégration de Data Factory, consultez le [configurations de Destination SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) section de cet article.

- **L’utilisateur SAP BW nécessite les autorisations suivantes**:

  - Autorisation pour les appels de fonction distants (RFC) et SAP BW.
  - Autorisations pour l’activité « Execute » de la **S_SDSAUTH** objet d’autorisation.

- **Un [runtime d’intégration (IR) auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime) avec le connecteur SAP .NET 3.0**. Suivez ces étapes de configuration :

  1. Installer et inscrire le runtime d’intégration auto-hébergé, 3,13 ou version ultérieure. (Ce processus est décrit plus loin dans cet article).

  2. Téléchargez le [64 bits SAP Connector pour Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) du site Web de SAP et l’installer sur le même ordinateur que l’IR auto-hébergé. Pendant l’installation, assurez-vous que vous sélectionnez **installer des assemblys GAC** dans le **les étapes de configuration facultatives** boîte de dialogue, comme le montre l’image suivante :

     ![Configurer la boîte de dialogue de connecteur SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Effectuer une copie complète à partir de SAP BW Open Hub

Dans le portail Azure, accédez à votre fabrique de données. Sélectionnez **créer et surveiller** pour ouvrir l’interface utilisateur de Data Factory dans un onglet séparé.

1. Sur le **prise en main** page, sélectionnez **copier des données** pour ouvrir l’outil Copier des données.

2. Sur le **propriétés** , spécifiez un **nom_tâche**, puis sélectionnez **suivant**.

3. Sur le **magasin de données Source** page, sélectionnez **+ créer une nouvelle connexion**. Sélectionnez **SAP BW Open Hub** à partir de la galerie de connecteur, puis **continuer**. Pour filtrer les connecteurs, vous pouvez taper **SAP** dans la zone de recherche.

4. Sur le **connexion spécifier SAP BW Open Hub** page, procédez comme suit pour créer une nouvelle connexion.

   ![Créer la page du service lié SAP BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. À partir de la **se connecter via le runtime d’intégration** , sélectionnez un runtime auto-hébergé existant d’intégration. Ou bien, si vous choisissez de créer une si vous n’en avez pas encore.

      Pour créer un nouveau runtime d’intégration auto-hébergé, sélectionnez **+ nouveau**, puis sélectionnez **auto-hébergé**. Entrez un **nom**, puis sélectionnez **suivant**. Sélectionnez **installation rapide** à installer sur l’ordinateur actuel, ou suivez le **le programme d’installation manuelle** étapes sont fournies.

      Comme mentionné dans [conditions préalables](#prerequisites), assurez-vous que vous avez un connecteur SAP pour Microsoft .NET 3.0 est installé sur le même ordinateur où le runtime d’intégration auto-hébergé est en cours d’exécution.

   2. Renseignez le SAP BW **nom du serveur**, **numéro du système**, **ID de Client,** **langage** (if autre que **EN**) , **Nom d’utilisateur**, et **mot de passe**.

   3. Sélectionnez **tester la connexion** pour valider les paramètres, puis sélectionnez **Terminer**.

   4. Une nouvelle connexion est créée. Sélectionnez **Suivant**.

5. Sur le **sélectionner les Destinations de Hub Open** page, parcourir les Destinations Open Hub qui sont disponibles dans votre SAP BW. Sélectionnez le OHD pour copier des données à partir de, puis sélectionnez **suivant**.

   ![Sélectionnez la table de Destination SAP BW Open Hub](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Spécifiez un filtre, si vous en avez besoin. Si votre OHD contient seulement les données à partir d’une transfert de données unique (DTP) l’exécution des processus avec un ID de demande unique, ou si vous êtes sûr que votre DTP est terminé et que vous souhaitez copier les données, désactivez le **exclure la dernière demande** case à cocher.

   En savoir plus sur ces paramètres dans le [configurations de Destination SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) section de cet article. Sélectionnez **Validate** pour vérifier quelles données seront renvoyées. Sélectionnez ensuite **Suivant**.

   ![Configurer le filtre de SAP BW Open Hub](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Sur le **magasin de données de Destination** page, sélectionnez **+ créer une nouvelle connexion** > **Azure Data Lake Storage Gen2**  >   **Continuer**.

8. Sur le **connexion de spécifier Azure Data Lake Storage** page, procédez comme suit pour créer une connexion.

   ![Créer une page de service lié ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Sélectionnez votre compte Data Lake Gen2 compatible avec le stockage à partir de la **nom** liste déroulante.
   2. Sélectionnez **Terminer** pour créer la connexion. Sélectionnez ensuite **Suivant**.

9. Sur le **choisir le fichier de sortie ou le dossier** , entrez **copyfromopenhub** comme nom de dossier de sortie. Sélectionnez ensuite **Suivant**.

   ![Choisissez la page de dossier de sortie](media/load-sap-bw-data/choose-output-folder.png)

10. Sur le **paramètre de format de fichier** page, sélectionnez **suivant** à utiliser les paramètres par défaut.

    ![Spécifiez la page format de récepteur](media/load-sap-bw-data/specify-sink-format.png)

11. Sur le **paramètres** page, développez **les paramètres de performances**. Entrez une valeur pour **degré de parallélisme de copie** tels que 5 à charger à partir de SAP BW en parallèle. Sélectionnez ensuite **Suivant**.

    ![Configurer les paramètres de copie](media/load-sap-bw-data/configure-copy-settings.png)

12. Sur le **Résumé** page, vérifiez les paramètres. Sélectionnez ensuite **Suivant**.

13. Sur le **déploiement** page, sélectionnez **moniteur** pour surveiller le pipeline.

    ![Page Déploiement](media/load-sap-bw-data/deployment.png)

14. Notez que le **moniteur** onglet sur le côté gauche de la page est automatiquement sélectionné. Le **Actions** colonne inclut des liens pour afficher les détails de l’exécution d’activité et de réexécuter le pipeline.

    ![Affichage d’analyse de pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Pour afficher les exécutions d’activités qui sont associés à l’exécution du pipeline, sélectionnez **afficher les exécutions d’activité** dans le **Actions** colonne. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour revenir à la vue des exécutions de pipeline, sélectionnez le **Pipelines** lien en haut. Sélectionnez **Actualiser** pour actualiser la liste.

    ![Écran analyse des activités](media/load-sap-bw-data/activity-monitoring.png)

16. Pour surveiller les détails d’exécution pour chaque activité de copie, sélectionnez le **détails** lien, qui est une icône en forme de lunettes ci-dessous **Actions** dans la vue de l’analyse des activités. Détails disponibles incluent le volume de données copiées de la source vers le récepteur, le débit des données, les étapes de l’exécution et la durée et les configurations utilisées.

    ![Surveillance des détails de l’activité](media/load-sap-bw-data/activity-monitoring-details.png)

17. Pour afficher le **ID de demande maximale**, revenez à la vue et sélectionnez analyse des activités **sortie** sous **Actions**.

    ![Écran de sortie d’activité](media/load-sap-bw-data/activity-output.png)

    ![Vue Détails de sortie activité](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Effectuez une copie incrémentielle à partir de SAP BW Open Hub

> [!TIP]
> Consultez [flux de l’extraction de delta de connecteur SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) pour savoir comment le connecteur SAP BW Open Hub dans Data Factory copie les données incrémentielles à partir de SAP BW. Cet article peut également vous aider à comprendre la configuration du connecteur de base.

Maintenant, nous allons continuer à configurer la copie incrémentielle à partir de SAP BW Open Hub.

Copie incrémentielle utilise un mécanisme de « limite supérieure » qui est basé sur le **ID de demande**. Cet ID est généré automatiquement dans la Destination SAP BW Open Hub par le processus DTP. Le diagramme suivant illustre ce flux de travail :

![Organigramme de flux de travail de copie incrémentielle](media/load-sap-bw-data/incremental-copy-workflow.png)

Sur la fabrique de données **prise en main** page, sélectionnez **créer un pipeline à partir du modèle** à utiliser le modèle intégré.

1. Recherchez **SAP BW** pour rechercher et sélectionner le **Incremental copier à partir de SAP BW dans Azure Data Lake Storage Gen2** modèle. Ce modèle copie les données dans le stockage Azure Data Lake Gen2. Vous pouvez utiliser un flux de travail similaire pour copier à d’autres types de récepteur.

2. Sur la page principale du modèle, sélectionnez ou créez trois connexions suivantes, puis **utiliser ce modèle** dans le coin inférieur droit de la fenêtre.

   - **Stockage d’objets Blob Azure**: Dans cette procédure pas à pas, nous utilisons le stockage Blob Azure pour stocker la limite supérieure, c'est-à-dire le *max copié ID de demande*.
   - **SAP BW Open Hub**: Il s’agit de la source pour copier des données à partir de. Reportez-vous à la procédure de copie complète précédente de configuration détaillées.
   - **Azure Data Lake Storage Gen2**: C’est le récepteur pour copier des données. Reportez-vous à la procédure de copie complète précédente de configuration détaillées.

   ![Copie incrémentielle à partir du modèle de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ce modèle génère un pipeline avec les trois activités suivantes et les rend chaînées en cas de réussite : *Recherche*, *copier des données*, et *Web*.

   Accédez au pipeline **paramètres** onglet. Affiche toutes les configurations que vous devez fournir.

   ![Copie incrémentielle à partir de la configuration de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Spécifiez le nom de table Open Hub pour copier des données à partir de.

   - **ADLSGen2SinkPath**: Spécifiez le chemin d’Azure Data Lake Storage Gen2 de destination pour copier des données. Si le chemin d’accès n’existe pas, l’activité de copie Data Factory crée un chemin d’accès lors de l’exécution.

   - **HighWatermarkBlobPath**: Spécifiez le chemin d’accès pour stocker la valeur de limite supérieure, tels que `container/path`.

   - **HighWatermarkBlobName**: Spécifiez le nom de l’objet blob pour stocker la valeur de limite supérieure, tels que `requestIdCache.txt`. Dans le stockage Blob, atteindre le chemin d’accès correspondante de HighWatermarkBlobPath + HighWatermarkBlobName, tel que *container/path/requestIdCache.txt*. Créez un objet blob avec contenu 0.

      ![Contenu de l’objet blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: Dans ce modèle, nous utilisons WebActivity pour appeler Azure Logic Apps pour définir la valeur de limite supérieure dans le stockage Blob. Ou bien, vous pouvez utiliser la base de données SQL Azure pour les stocker. Utiliser une activité de procédure stockée pour mettre à jour la valeur.

      Vous devez d’abord créer une application logique, comme le montre l’image suivante. Ensuite, collez la **URL HTTP POST**.

      ![Configuration de l’application logique](media/load-sap-bw-data/logic-app-config.png)

      1. Accédez au portail Azure. Sélectionner un nouveau **Logic Apps** service. Sélectionnez **+ application logique vide** pour accéder à **Concepteur d’applications logiques**.

      2. Créer un déclencheur de **quand une demande HTTP est reçue**. Spécifiez le corps de la demande HTTP comme suit :

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Ajouter un **créer un objet blob** action. Pour **chemin d’accès du dossier** et **nom d’objet Blob**, utilisent les mêmes valeurs que vous avez configuré précédemment dans **HighWatermarkBlobPath** et **HighWatermarkBlobName**.

      4. Sélectionnez **Enregistrer**. Ensuite, copiez la valeur de **URL HTTP POST** à utiliser dans le pipeline Data Factory.

4. Une fois que vous fournissez les paramètres de pipeline Data Factory, sélectionnez **déboguer** > **Terminer** pour appeler une exécution pour valider la configuration. Ou sélectionnez **publier tout** pour publier les modifications, puis sélectionnez **déclencheur** exécuter une série.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurations de Destination SAP BW Open Hub

Cette section présente la configuration du côté SAP BW pour utiliser le connecteur SAP BW Open Hub dans Data Factory pour copier des données.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurer l’extraction de delta dans SAP BW

Si vous avez besoin uniquement de copie incrémentielle ou de copie incrémentielle et les copie historique, configurer l’extraction de delta dans SAP BW.

1. Créez la Destination Open Hub. Vous pouvez créer le OHD dans SAP Transaction RSA1, qui crée automatiquement le processus de transfert de données et de transformation nécessaire. Utilisez les paramètres suivants :

   - **ObjectType**: Vous pouvez utiliser n’importe quel type d’objet. Ici, nous utilisons **InfoCube** comme exemple.
   - **Type de destination**: Sélectionnez **Table de base de données**.
   - **Clé de la Table**: Sélectionnez **clé technique**.
   - **Extraction** : Sélectionnez **conserver des données et insérer les enregistrements dans la Table**.

   ![Créer la boîte de dialogue d’extraction de SAP BW OHD delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Créer la boîte de dialogue d’extraction de SAP BW OHD delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Vous pouvez augmenter le nombre de processus de travail SAP en cours d’exécution pour le processus DTP parallèle de :

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Planifier le processus DTP dans les chaînes de processus.

   Un delta DTP pour un cube fonctionne uniquement si les lignes nécessaires n’ont pas été compressées. Assurez-vous que la compression du cube BW n’est pas en cours d’exécution avant le processus DTP à la table Open Hub. Pour ce faire, le plus simple consiste à intégrer le processus DTP dans vos chaînes de processus existant. Dans l’exemple suivant, le processus DTP (pour le OHD) est inséré dans la chaîne de processus entre les *Adjust* (cumul d’agrégats) et *réduire* les étapes (compression de cube).

   ![Créer le diagramme de flux de chaîne de processus SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurer l’extraction complète dans SAP BW

En plus de l’extraction de delta, vous souhaiterez peut-être une extraction complète de la même fournisseur d’infos BW SAP. Cela s’applique généralement si vous souhaitez de copie complète mais non incrémentielle, ou vous souhaitez [resync d’extraction delta](#resync-delta-extraction).

Vous ne pouvez avoir plusieurs DTP pour la même OHD. Par conséquent, vous devez créer un OHD supplémentaire avant l’extraction de delta.

![Créer des SAP BW OHD complète](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Pour un chargement complet OHD, choisir différentes options que pour l’extraction de delta :

- Dans OHD : Définir le **Extraction** option **supprimer les données et insérer des enregistrements**. Sinon, les données seront extraites autant de fois lorsque vous répétez le processus DTP dans une chaîne de processus BW.

- Dans le processus DTP : Définissez **Mode Extraction** à **complète**. Vous devez modifier le processus DTP créé automatiquement à partir de **Delta** à **complète** immédiatement après le OHD est créé, comme le montre cette image :

   ![Créer des SAP BW OHD boîte de dialogue configurée pour l’extraction de « Complète »](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Dans le connecteur BW Open Hub de fabrique de données : Désactiver **exclure la dernière demande**. Sinon, rien ne sera extrait.

Vous exécutez généralement le processus DTP complète manuellement. Ou bien, vous pouvez créer une chaîne de processus pour le processus DTP complète. Il est généralement une chaîne distincte qui est indépendante de vos chaînes de processus existant. Dans les deux cas, *vous assurer que le processus DTP est terminé avant de commencer l’extraction à l’aide de copie de Data Factory*. Sinon, seules des données partielles seront copiées.

### <a name="run-delta-extraction-the-first-time"></a>Exécuter d’extraction de delta la première fois

La première extraction delta est techniquement un *complète d’extraction*. Par défaut, le connecteur SAP BW Open Hub exclut la dernière demande lors de la copie des données. Pour la première extraction delta, aucune donnée n’est extrait par l’activité de copie Data Factory jusqu'à ce qu’un DTP suivante génère des données delta dans le tableau avec un ID de demande séparée. Il existe deux façons d’éviter ce scénario :

- Désactiver la **exclure la dernière demande** option pour la première extraction delta. Assurez-vous que le premier delta DTP est terminé avant de commencer l’extraction de delta la première fois.
-  Utilisez la procédure de resynchronisation de l’extraction delta, comme décrit dans la section suivante.

### <a name="resync-delta-extraction"></a>Resynchronisation d’extraction delta

Les scénarios suivants, modifient les données dans les cubes de SAP BW mais ne sont pas considérés comme par le delta DTP :

- Suppression de sélectif de SAP BW (des lignes à l’aide de n’importe quelle condition de filtre)
- Suppression des demandes de SAP BW (de demandes défectueux)

Une SAP une Destination Open Hub n’est pas une cible de données contrôlées par le dépôt de données (dans tous les packages de prise en charge SAP BW depuis 2015). Par conséquent, vous pouvez supprimer des données à partir d’un cube sans modifier les données dans le OHD. Vous devez ensuite resynchroniser les données du cube avec Data Factory :

1. Exécuter une extraction complète dans Data Factory (en utilisant un DTP complète dans SAP).
2. Supprimez toutes les lignes dans la table de Open Hub pour le delta DTP.
3. Définir l’état du delta DTP à **extraites**.

Après cela, delta suivante PDT des extractions de delta de fabrique de données fonctionnent comme prévu.

Pour définir l’état du delta DTP à **extraites**, vous pouvez utiliser l’option suivante pour exécuter le processus DTP delta manuellement :

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la prise en charge du connecteur SAP BW Open Hub :

> [!div class="nextstepaction"]
>[Connecteur SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
