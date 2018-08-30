---
title: Utiliser le serveur d’historique Spark étendu pour déboguer et diagnostiquer des applications Spark - Azure HDInsight
description: Utilisez le serveur d’historique Spark étendu pour déboguer et diagnostiquer des applications Spark – Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2018
ms.openlocfilehash: b514f23f2e8a43f99fd5bf5c3afb5ed625ad4472
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046573"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Utiliser le serveur d’historique Spark étendu pour déboguer et diagnostiquer des applications Spark

Cet article aide à utiliser le serveur d’historique Spark étendu pour déboguer et diagnostiquer des applications Spark terminées et en cours d’exécution. L’extension comporte actuellement un onglet Données et un onglet Graphique. Dans l’onglet Données, les utilisateurs peuvent vérifier les données d’entrée et de sortie du travail Spark. L’onglet Graphique permet de consulter le flux de données et de réexécuter le graphique du travail.

## <a name="open-the-spark-history-server"></a>Ouvrir le serveur d’historique Spark

Le serveur d’historique Spark est l’interface utilisateur web pour les applications Spark terminées et en cours d’exécution. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Pour ouvrir l’interface utilisateur web du serveur d’historique Spark sur le Portail Azure

1. À partir du [portail Azure](https://portal.azure.com/), ouvrez le cluster Spark. Pour plus d’informations, voir [Énumération et affichage des clusters](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Dans **Liens rapides**, cliquez sur **Tableau de bord du cluster**, puis sur **Serveur d’historique Spark**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark. 

    ![Serveur d’historique Spark](./media/apache-azure-spark-history-server/launch-history-server.png "Serveur d’historique Spark")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>Pour ouvrir l’interface utilisateur web du serveur d’historique Spark par URL
Accédez à l’URL suivante en remplaçant <ClusterName> par le nom de cluster Spark du client pour ouvrir le serveur d’historique Spark.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

L’interface utilisateur web du serveur d’historique Spark ressemble à ce qui suit :

![Serveur d’historique HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Ouvrir l’onglet Données du serveur d’historique Spark
Sélectionnez l’ID tâche, puis cliquez sur **Données** dans le menu Outil pour ouvrir la Vue de données.

+ Consultez les **Entrées**, **Sorties** et **Opérations de table** en sélectionnant les onglets séparément.

    ![Onglets Données](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Copiez toutes les lignes en cliquant sur le bouton **Copier**.

    ![Copie de données](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Enregistrez toutes les données dans un fichier CSV en cliquant sur le bouton **CSV**.

    ![Sauvegarde de données](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Entrez des mots clés dans le champ **Rechercher** : le résultat de la recherche s’affiche immédiatement.

    ![Recherche de données](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Cliquez sur l’en-tête de colonne pour trier la table, sur le signe plus pour développer une ligne et afficher plus de détails, ou sur le signe moins pour réduire une ligne.

    ![Table de données](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Téléchargez un seul fichier en cliquant sur le bouton **Téléchargement partiel** qui se trouve à droite : le fichier sélectionné sera téléchargé vers l’ordinateur local ; s’il n’existe plus, un nouvel onglet s’ouvrira pour afficher les messages d’erreur.

    ![Ligne de téléchargement de données](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiez le chemin d’accès complet ou relatif en développant le menu de téléchargement pour sélectionner **Copier le chemin d’accès complet** ou **Copier le chemin d’accès relatif**. Pour les fichiers Azure Data Lake Storage, **Ouvrir dans l’Explorateur Stockage Azure** lance l’Explorateur Stockage Azure et l’ouvre sur le dossier lors de la connexion.

    ![Chemin de copie de données](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Cliquez sur le chiffre sous le tableau pour parcourir les pages quand il y a trop de lignes pour les afficher sur une seule page. 

    ![Page de données](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Placez le curseur sur le point d’interrogation sous Données pour afficher l’info-bulle, ou cliquez sur le point d’interrogation pour obtenir plus d’informations.

    ![Plus d’informations sur les données](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Pour signaler des problèmes, envoyez des commentaires en cliquant sur **Envoyez-nous vos commentaires**.

    ![Commentaires sur le graphique](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Ouvrir l’onglet Graphique du serveur d’historique Spark
Sélectionnez l’ID tâche, puis cliquez sur **Graphique** dans le menu Outil pour ouvrir la Vue de graphique du travail.

+ Regardez la vue d’ensemble de votre travail offerte par le graphique ainsi généré. 

+ Par défaut, il affiche tous les travaux ; vous pouvez le filtrer par **ID tâche**.

    ![ID tâche du graphique](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Par défaut, **En cours** est sélectionné ; l’utilisateur peut consulter le flux de données en sélectionnant **Lu/Écrit** dans la liste déroulante **Affichage**.

    ![Affichage du graphique](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Le nœud du graphique est représenté en couleurs indiquant la carte thermique.

    ![Carte thermique du graphique](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Lisez le travail en cliquant sur le bouton **Lecture**, et arrêtez quand vous le souhaitez en cliquant sur le bouton Arrêter. La tâche s’affiche dans une couleur indiquant l’état lors de la lecture :

    + Le vert indique une réussite : le travail s’est terminé avec succès.
    + L’orange indique une nouvelle tentative : le travail a échoué sans que cela affecte son résultat final. Ces tâches comportaient des doublons ou des instances de nouvelle tentative susceptibles de réussir par la suite.
    + Le rouge indique un échec : la tâche a échoué.
    + Le bleu indique une exécution en cours : la tâche est en cours d’exécution.
    + Le blanc indique une tâche ignorée ou en attente : la tâche est en attente d’exécution ou la phase a été ignorée.

    ![Échantillon de couleur En cours d’exécution du graphique](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![Échantillon de couleur Échec du graphique](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > La lecture de chaque travail est autorisée. Lorsqu’un travail ne comporte pas de phase ou n’est pas terminé, la lecture n’est pas prise en charge.


+ Utilisez la roulette de défilement pour effectuer un zoom avant/arrière sur le graphique du travail, ou cliquez sur **Zoomer pour ajuster** afin de l’ajuster à l’écran.
 
    ![Zoomer pour ajuster le graphique](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Placez le curseur sur le nœud du graphique pour afficher l’info-bulle au niveau des tâches ayant échoué, puis cliquez sur la phase pour ouvrir la page correspondante.

    ![Info-bulle du graphique](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Le nœud de graphique du travail affiche les informations suivantes sur chaque phase :
    + ID ;
    + nom ou description ;
    + nombre total de tâches ;
    + lecture de données : somme de la taille d’entrée et de la taille de lecture aléatoire ;
    + écriture de données : somme de la taille de sortie et de la taille d’écriture aléatoire ;
    + durée d’exécution : délai entre l’heure de début de la première tentative et l’heure de fin de la dernière tentative ;
    + nombre de lignes : somme des enregistrements d’entrée, des enregistrements de sortie, des enregistrements de lecture aléatoire et des enregistrements d’écriture aléatoire ;
    + progression.

    > [!NOTE]
    > Par défaut, le nœud de graphique du travail affiche les informations de la dernière tentative de chaque phase (à l’exception du délai d’exécution de la phase) ; pendant la lecture cependant, il présente les informations de chaque tentative.

    > [!NOTE]
    > Pour la taille des données de lecture et d’écriture, nous appliquons 1 Mo = 1 000 Ko = 1 000 * 1 000 octets.

+ Pour signaler des problèmes, envoyez des commentaires en cliquant sur **Envoyez-nous vos commentaires**.

    ![Commentaires sur le graphique](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>Forum Aux Questions

### <a name="1-revert-to-community-version"></a>1. Revenir à la version Communauté

Pour revenir à la version Communauté, suivez les étapes ci-dessous :

1. Ouvrez le cluster dans Ambari. Cliquez sur **Spark2** dans le volet gauche.
2. Cliquez sur l’onglet **Configurations**.
3. Développez le groupe **Custom spark2-defaults**.
4. Cliquez sur **Ajouter une propriété**, ajoutez **spark.ui.enhancement.enabled=false** et enregistrez.
5. La propriété est maintenant définie sur **false**.
6. Pour enregistrer la configuration, cliquez sur **Enregistrer**.

    ![Désactivation de la fonctionnalité](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Cliquez sur **Spark2** dans le volet gauche ; dans l’onglet **Résumé**, cliquez sur **Serveur d’historique Spark2**.

    ![Redémarrer le serveur 1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Redémarrez le serveur d’historique en cliquant sur **Redémarrer** sur le **Serveur d’historique Spark2**.

    ![Redémarrer le serveur 2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Actualisez l’interface utilisateur web du serveur d’historique Spark : il reviendra à la version Communauté.

### <a name="2-upload-history-server-event"></a>2. Charger un événement du serveur d’historique

Si vous rencontrez une erreur de serveur d’historique, suivez ces étapes pour récupérer l’événement :
1. Téléchargez l’événement en cliquant sur **Télécharger** dans l’interface utilisateur web du serveur d’historique.

    ![Télécharger l’événement](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Cliquez sur **Envoyez-nous vos commentaires** dans l’onglet Données/Graphique.

    ![Commentaires sur le graphique](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Indiquez le titre et la description de l’erreur, faites glisser le fichier zip dans le champ d’édition, puis cliquez sur **Soumettre un nouveau problème**.

    ![Entrer un problème](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Mettre à niveau un fichier jar dans un scénario de correctif logiciel

Si vous souhaitez effectuer une mise à niveau avec un correctif logiciel, utilisez le script ci-dessous, qui met à jour spark-enhancement.jar*.

**upgrade_spark_enhancement.sh** :

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Utilisation**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Exemple**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**Pour utiliser le fichier Bash sur le Portail Azure**

1. Lancez le [Portail Azure](https://ms.portal.azure.com), puis sélectionnez votre cluster.
2. Cliquez sur **Actions de script**, puis **Soumettre une nouvelle action de script**. Remplissez le formulaire **Soumettre une action de script**, puis cliquez sur le bouton **Créer**.
    
    + **Type de script** : sélectionnez **Personnalisé**.
    + **Nom** : spécifiez un nom de script.
    + **URI de script Bash** : chargez le fichier Bash dans le cluster privé, puis copiez-y l’URL. Vous pouvez également utiliser l’URI fourni.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Regardez sur **Principal** et **Travail**.
    + **Paramètres** : définissez les paramètres suivant l’utilisation de l’interpréteur de commandes.

    ![Charger des journaux ou mettre à niveau le correctif logiciel](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>Problème connu

1.  Pour le moment, cela ne fonctionne que pour le cluster Spark 2.3.

2.  Les données d’entrée/de sortie avec RDD ne s’affichent pas dans l’onglet Données.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Configurer les paramètres de Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Nous contacter

Si vous avez des commentaires, ou que vous rencontrez d’autres problèmes dans l’utilisation de cet outil, envoyez un e-mail à l’adresse [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com).
