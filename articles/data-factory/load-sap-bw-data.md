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
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200155"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Charger des données à partir de SAP Business Warehouse (BW) à l’aide d’Azure Data Factory

Cet article vous montre une procédure pas à pas sur l’utilisation de la fabrique de données _charger des données à partir de SAP Business Warehouse (BW) via Open Hub dans Azure Data Lake Storage Gen2_. Vous pouvez suivre des étapes similaires pour copier des données à d’autres [prise en charge des banques de données réceptrices](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Reportez-vous à [article traitant du connecteur SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md) sur la copie des données à partir de SAP BW en général, notamment la présentation sur SAP BW Open Hub intégration et delta d’extraction le flux.

## <a name="prerequisites"></a>Conditions préalables

- **Azure Data Factory (ADF) :** Si vous n’avez pas une fabrique de données, suivez la «[créer une fabrique de données](quickstart-create-data-factory-portal.md#create-a-data-factory)« section pour en créer un. 

- **SAP BW Open Hub Destination (OHD) avec le type de destination en tant que « Table de base de données ».** Suivez [configurations de Destination SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) section pour en créer un ou pour vérifier si votre OHD existant est configuré correctement pour être intégré à ADF.

- **Utilisateur de SAP BW utilisé doit comporter les autorisations suivantes :**

  - Autorisation relative à RFC et SAP BW.
  - Autorisations pour le «**exécuter**« Activité de l’objet d’autorisation »**S_SDSAUTH**».

- **[Self-host Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) avec le connecteur SAP .NET 3.0 sont requis**. Voici les tâches de préparation détaillées qui doivent être effectuées :

  1. Installer et inscrire le runtime d’intégration auto-hébergé avec version > = 3.13 (traitée dans la procédure suivante). 

  2. Téléchargez le [64 bits SAP .NET 3.0 connecteur](https://support.sap.com/en/product/connectors/msnet.html) du site Web de SAP et l’installer sur l’ordinateur runtime d’intégration auto-hébergé.  Lorsque l’installation, dans la fenêtre « étapes de configuration facultatives », veillez à sélectionner la «**installer des assemblys GAC**« option comme illustré dans l’image suivante.

     ![Configurer le connecteur SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Copie complète à partir de SAP BW Open Hub

Sur le portail Azure, accédez à votre fabrique de données -> sélectionnez **créer et surveiller** pour lancer l’UI ADF dans un onglet séparé. 

1. Sur la page **Prise en main**, sélectionnez **Copier des données** pour lancer l’outil Copier des données. 

2. Sur le **propriétés** , spécifiez un nom pour le **nom_tâche** champ, puis sélectionnez **suivant**.

3. Sur le **magasin de données Source** , cliquez sur **+ créer une nouvelle connexion** -> sélectionnez **SAP BW Open Hub** à partir de la galerie de connecteur -> sélectionnez **continuer**. Vous pouvez taper « SAP » dans la zone de recherche pour filtrer les connecteurs.

4. Sur le **connexion spécifier SAP BW Open Hub** page, 

   ![Créer le service de SAP BW Open Hub lié](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Choisissez le **se connecter via le Runtime d’intégration**: cliquez sur la liste déroulante pour sélectionner un runtime d’intégration auto-hébergé existant ou créez-en un, si vous n’avez pas encore configuré le runtime d’intégration auto-hébergé. 

      Pour créer de nouvelles, cliquez sur **+ nouveau** dans la liste déroulante-> sélectionnez le type de **auto-hébergé** -> spécifiez un **nom** et cliquez sur **suivant** -> choisissez **Installation rapide** à installer sur l’ordinateur actuel, ou suivez le **le programme d’installation manuelle** étapes il.

      Comme mentionné dans [prérequis](#prerequisites), vérifiez que vous avez également la **connecteur SAP .NET 3.0** installé sur le même ordinateur où le runtime d’intégration auto-hébergé est en cours d’exécution.

   2. Spécifiez la SAP BW **nom du serveur**, **numéro du système**, **ID de Client,** **langage** (if autres qu’anglais), **nomd’utilisateur**, et **mot de passe**.

   3. Cliquez sur **Tester la connexion** pour vérifier les paramètres, puis sélectionnez **Terminer**.

   4. Vous voyez qu’une nouvelle connexion est créée. Sélectionnez **Suivant**.

5. Sur le **les destinations Sélectionnez Open Hub** page, recherchez les Destinations de Hub Open disponible sur votre SAP BW et sélectionnez celui que vous souhaitez copier des données à partir de, puis cliquez sur **suivant**.

   ![Sélectionnez la table de SAP BW Open Hub](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Spécifiez le filtre si nécessaire. Si votre Destination Open Hub contient seulement les données à partir de l’exécution du processus de transfert de données (DTP) unique avec l’ID de demande unique, ou vous vraiment votre DTP terminée et que vous souhaitez copier toutes les données, désactivez le **exclure la dernière demande**. Vous pouvez en savoir plus sur la façon dont ces paramètres sont liés à votre configuration de SAP BW dans [configurations de Destination SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) section. Cliquez sur **Validate** vérifier soigneusement les données retournées, puis sélectionnez **suivant**.

   ![Configurer le filtre de SAP BW Open Hub](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Dans le **magasin de données de Destination** , cliquez sur **+ créer une nouvelle connexion**, puis sélectionnez **Azure Data Lake Storage Gen2**, puis sélectionnez **continuer**.

8. Dans le **connexion de spécifier Azure Data Lake Storage** page, 

   ![Créer le service de génération 2 ADLS lié](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Sélectionnez votre compte capable de Data Lake Storage Gen2 à partir de la liste déroulante « Nom de compte de stockage ».
   2. Sélectionnez **Terminer** pour créer la connexion. Sélectionnez ensuite **Suivant**.

9. Dans le **choisir le fichier de sortie ou le dossier** page, entrez « copyfromopenhub » comme nom de dossier de sortie, puis sélectionnez **suivant**.

   ![Choisissez le dossier de sortie](media/load-sap-bw-data/choose-output-folder.png)

10. Dans le **paramètre de format de fichier** page, sélectionnez **suivant** à utiliser les paramètres par défaut.

    ![Spécifiez le format de récepteur](media/load-sap-bw-data/specify-sink-format.png)

11. Dans le **paramètres** page, développez le **les paramètres de performances**et définissez **degré de parallélisme de copie** par exemple 5 afin de charger à partir de SAP BW en parallèle. Cliquez sur **Suivant**.

    ![Configurer les paramètres de copie](media/load-sap-bw-data/configure-copy-settings.png)

12. Dans le **Résumé** page, passez en revue les paramètres, puis sélectionnez **suivant**.

13. Dans le **déploiement** page, sélectionnez **moniteur** pour surveiller le pipeline.

    ![Page Déploiement](media/load-sap-bw-data/deployment.png)

14. Notez que l’onglet **Surveiller** sur la gauche est sélectionné automatiquement. La colonne **Actions** comprend les liens permettant d’afficher les détails de l’exécution de l’activité et de réexécuter le pipeline :

    ![Surveillance de pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne **Actions**. Il n’y a qu’une seule activité (activité de copie) dans le pipeline ; vous ne voyez donc qu’une seule entrée. Pour revenir à l’affichage des exécutions du pipeline, sélectionnez le lien **Pipelines** affiché en haut de la fenêtre. Sélectionnez **Actualiser** pour actualiser la liste.

    ![Surveillance de l’activité](media/load-sap-bw-data/activity-monitoring.png)

16. Pour surveiller les détails d’exécution de chaque activité de copie, sélectionnez le lien **Détails** (image de lunettes) sous **Actions** dans la vue de surveillance des activités. Vous pouvez suivre les informations détaillées comme le volume de données copiées à partir de la source dans le récepteur, le débit des données, les étapes d’exécution avec une durée correspondante et les configurations utilisées :

    ![Surveillance des détails de l’activité](media/load-sap-bw-data/activity-monitoring-details.png)

17. Examinez le **ID de demande maximale** qui est copié. Revenez à la surveillance des activités, cliquez sur le **sortie** sous **Actions**.

    ![Sortie d'activité](media/load-sap-bw-data/activity-output.png)

    ![Détails de sortie d’activité](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Copie incrémentielle à partir de SAP BW Open Hub

> [!TIP]
>
> Reportez-vous à [flux de l’extraction de delta de connecteur SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) pour en savoir plus sur le fonctionne du connecteur d’ADF SAP BW Open Hub pour copier des données incrémentielles à partir de SAP BW, lisez cet article à partir du début de comprendre les principes fondamentaux de connecteur liés configurations.

Maintenant, nous allons continuer à configurer la copie incrémentielle à partir de SAP BW Open Hub. 

La copie incrémentielle à l’aide de mécanisme de limite supérieure selon **ID de demande** généré automatiquement dans la Destination SAP BW Open Hub par DTP. Le flux de travail de cette approche est illustré dans le diagramme suivant :

![Flux de travail de copie incrémentielle](media/load-sap-bw-data/incremental-copy-workflow.png)

Sur l’UI ADF **prise en main** page, sélectionnez **créer un pipeline à partir du modèle** d’exploiter le modèle intégré. 

1. Rechercher « SAP BW » pour rechercher et sélectionner le modèle nommé **Incremental copier à partir de SAP BW dans Azure Data Lake Storage Gen2**. Ce modèle copie des données dans ADLS Gen2, vous pouvez suivre plus tard le flux similaire à copier à d’autres types de récepteur.

2. Dans la page principale du modèle, sélectionnez ou créez trois connexions suivantes, puis sélectionnez **utiliser ce modèle** en bas à droite.

   - **Objets Blob Azure**: dans cette procédure pas à pas, nous utilisons des objets Blob Azure pour stocker la limite supérieure, qui est l’ID de demande copié max.
   - **SAP BW Open Hub**: votre source de laquelle copier les données. Reportez-vous à la procédure de copie complète précédente sur les configurations détaillées.
   - **ADLS Gen2**: votre récepteur pour copier des données. Reportez-vous à la procédure de copie complète précédente sur les configurations détaillées.

   ![Copie incrémentielle à partir du modèle de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Ce modèle génère un pipeline avec trois activités - **recherche, copier des données et Web** - et les rend chaînées en cas de réussite. Accédez au pipeline **paramètres** onglet, vous consultez toutes les configurations que vous devez fournir.

   ![Copie incrémentielle à partir de la configuration de SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: spécifiez le nom de table Open Hub pour copier des données à partir de.

   - **ADLSGen2SinkPath**: spécifiez le chemin d’accès de destination ADLS Gen2 pour copier des données. Si le chemin d’accès n’existe pas, activité de copie de l’ADF en créera un pendant l’exécution.

   - **HighWatermarkBlobPath**: spécifiez le chemin d’accès pour stocker la valeur de limite supérieure, par exemple `container/path`. 

   - **HighWatermarkBlobName**: spécifiez le nom de l’objet blob pour stocker la valeur de limite supérieure, par exemple `requestIdCache.txt`. Dans votre stockage d’objets blob, dans le chemin correspondant de HighWatermarkBlobPath + HighWatermarkBlobName par exemple, «*container/path/requestIdCache.txt*», créez un objet blob avec contenu 0. 

      ![Contenu de l’objet blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: dans ce modèle, nous utilisons l’activité Web pour appeler des applications logiques pour définir la valeur de limite supérieure dans le stockage Blob. Ou bien, vous pouvez également utiliser SQL database pour stocker et utiliser l’activité de procédure stockée pour mettre à jour la valeur. 

      Ici, vous devez tout d’abord créer une application logique en tant que la commande suivante, puis copiez le **URL HTTP POST** à ce champ. 

      ![Configuration de l’application logique](media/load-sap-bw-data/logic-app-config.png)

      1. Accédez au portail Azure -> nouveau un **Logic Apps** service -> cliquez sur **+ application logique vide** pour accéder à **Concepteur d’applications logiques**.

      2. Créer un déclencheur de **quand une demande HTTP est reçue**. Spécifiez le corps de la demande HTTP comme suit :

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. Ajouter une action de **créer un objet blob**. Pour « Chemin d’accès du dossier » et « Nom d’objet Blob », utilisez la même valeur que celui configurée dans HighWatermarkBlobPath et HighWatermarkBlobName ci-dessus.

      4. Cliquez sur **enregistrer**, puis copiez la valeur de **URL HTTP POST** à utiliser dans le pipeline ADF.

4. Après avoir fourni toutes les valeurs pour les paramètres de pipeline ADF, vous pouvez cliquer sur **déboguer** -> **Terminer** pour appeler une exécution pour valider la configuration. Vous pouvez également sélectionner **publier tout** pour publier toutes les modifications, puis cliquez sur **déclencheur** exécuter une série.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurations de Destination SAP BW Open Hub

Cette section présente la configuration nécessaire côté SAP BW pour pouvoir utiliser le connecteur de SAP BW Open Hub dans ADF pour copier des données.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurer l’extraction de delta dans SAP BW

Si vous avez besoin de copie historique et le copie incrémentielle ou uniquement la copie incrémentielle, configurer l’extraction de delta dans SAP BW.

1. Création de la Destination Open Hub (OHD)

   Vous pouvez créer le OHD dans SAP Transaction RSA1, qui crée automatiquement la transformation requise et le processus de transfert de données (DTP). Utilisez les paramètres suivants :

   - Type d’objet peut être tout. Ici, nous utilisons InfoCube comme exemple.
   - **Type de destination :** *Table de base de données*
   - **Clé de la Table :** *Clé technique*
   - **Extraction :** *Conserver les données et insérer des enregistrements dans la Table*

   ![Créer d’extraction de SAP BW OHD delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Vous pouvez augmenter le nombre de processus de travail SAP en cours d’exécution pour le processus DTP parallèle de :

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Planifier le processus DTP dans les chaînes de processus

   Un processus DTP Delta pour un cube ne fonctionne que si le nécessitent lignes ont pas été compressés encore. Par conséquent, il se peut que vous devez vous assurer que la Compression du Cube BW n’est pas exécuté avant le processus DTP à la table Open Hub. Le moyen le plus simple pour cela consiste à intégrer cette DTP dans vos chaînes de processus existant. Dans l’exemple ci-dessous, le processus DTP (pour le OHD) est inséré dans la chaîne de processus entre l’étape Adjust (cumul d’agrégats) et réduire (Cube Compression).

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurer l’extraction complète dans SAP BW

Outre l’extraction delta, vous pouvez souhaiter avoir une extraction complète de la même fournisseur d’infos. Elle s’applique généralement si vous souhaitez complète copie sans nécessité incrémentielle ou vous souhaitez [resynchronisation d’extraction delta](#re-sync-delta-extraction).

Vous ne devez pas avoir plusieurs DTP pour la même OHD. Par conséquent, vous devez créer une extraction OHD puis delta supplémentaire.

![créer-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Pour un chargement complet OHD, choisir des options différentes à l’extraction de delta :

- Dans OHD : définir l’option « Extraction » en tant que «*supprimer les données et insérer des enregistrements*». Sinon les données seraient extraites souvent répété lorsque le processus DTP dans une chaîne de processus BW.

- Dans DTP : définir le « Mode d’Extraction » comme «*complète*». Vous devez modifier le processus DTP créé automatiquement à partir de Delta Full juste après que le OHD a été créé :

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Dans le connecteur d’ADF SAP BW Open Hub : désactiver l’option «*exclure la dernière demande*». Sinon, rien ne serait extraits. 

Vous exécutez généralement le processus DTP complète manuellement. Ou vous pouvez également créer une chaîne de processus pour le processus DTP complète : il serait généralement une chaîne de processus distinct indépendante à partir de vos chaînes de processus existant. Dans les deux cas, vous devez **Assurez-vous que le processus DTP terminée avant de commencer l’extraction à l’aide d’ADF copy**, sinon, les données partielles seront copiées.

### <a name="run-delta-extraction-the-first-time"></a>Exécuter d’extraction de delta la première fois

La première Extraction Delta est techniquement un **Extraction complète**. Remarque par le connecteur d’ADF SAP BW Open Hub par défaut exclut la dernière demande lors de la copie des données. Dans le cas d’extraction delta pour la première fois, dans l’activité de copie ADF, aucune donnée ne sera extrait jusqu'à ce qu’il soit suivantes DTP génère des données delta dans la table avec l’ID de demande distinct. Bien qu’il existe deux façons possibles d’éviter ce scénario :

1. Désactiver l’option « Exclure les dernière demande » pour la première Delta d’Extraction dans ce cas, que vous devez vous assurer que le premier DTP Delta est terminée avant de commencer l’Extraction de Delta de la première fois
2. Utilisez la procédure de resynchroniser l’Extraction de Delta, comme décrit ci-dessous.

### <a name="re-sync-delta-extraction"></a>Synchronisez à nouveau d’extraction delta

Il existe quelques scénarios qui modifient les données de SAP BW Cubes, mais ne sont pas envisagés par le processus DTP Delta :

- SAP BW de suppression sélective (de lignes à l’aide de n’importe quelle condition de filtre)
- Suppression des demandes SAP BW (de demande défectueux)

Une SAP une Destination Open Hub n’est pas une cible de données contrôlées par le dépôt de données (dans tous les SAP BW prise en charge des Packages depuis l’année 2015). Par conséquent, il est possible de supprimer des données à partir d’un cube sans modifier les données dans le OHD. Dans ce cas, vous devez resynchroniser les données du cube avec les données dans ADF en effectuant les étapes suivantes :

1. Exécuter une Extraction complète dans ADF (en utilisant un DTP complète dans SAP)
2. Supprimer toutes les lignes dans la table Open Hub pour le processus DTP Delta
3. La valeur de l’état de la PAO Delta extraites

Après cela, toutes les PDT Delta et Extractions de Delta ADF fonctionnent comme prévu.

Vous pouvez définir l’état de la PAO Delta à extraites en exécutant la DTP Delta manuellement à l’aide de l’option suivante : «*Aucun transfert de données ; État de delta dans la Source : Extraites*».

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour en savoir plus sur la prise en charge du connecteur SAP BW Open Hub : 

> [!div class="nextstepaction"]
>[Connecteur SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
