---
title: Serveur d’historique Spark étendu pour déboguer des applications - Azure HDInsight
description: Utilisez le serveur d’historique Spark étendu pour déboguer et diagnostiquer des applications Spark – Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 1320764687f3eb2f033ca70703a9bcb16ab616ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494737"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Utiliser le serveur d’historique Apache Spark étendu pour déboguer et diagnostiquer des applications Apache Spark

Cet article aide à utiliser le serveur d’historique Apache Spark étendu pour déboguer et diagnostiquer des applications Spark terminées et en cours d’exécution. L’extension comporte des onglets dédiés aux données, aux graphiques et aux diagnostics. Dans l’onglet **Données**, les utilisateurs peuvent vérifier les données d’entrée et de sortie du travail Spark. L’onglet **Graph** permet de consulter le flux de données et de lire à nouveau le graphique du travail. Dans l’onglet **Diagnostic**, les utilisateurs peuvent connaître **l’asymétrie des données**, **l’asymétrie temporelle** et **l’analyse de l’utilisation des exécuteurs**.

## <a name="get-access-to-apache-spark-history-server"></a>Obtenir l’accès au serveur d’historique Apache Spark

Le serveur d’historique Apache Spark est l’interface utilisateur web pour les applications Spark terminées et en cours d’exécution.

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>Ouvrir l’interface utilisateur web du serveur d’historique Apache Spark sur le Portail Azure

1. À partir du [portail Azure](https://portal.azure.com/), ouvrez le cluster Spark. Pour plus d’informations, voir [Énumération et affichage des clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Dans **Liens rapides**, cliquez sur **Tableau de bord du cluster**, puis sur **Serveur d’historique Spark**. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster Spark.

    ![Serveur d’historique Spark du lancement du portail](./media/apache-azure-spark-history-server/launch-history-server.png "Serveur d’historique Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Ouvrir l’interface utilisateur web du serveur d’historique Spark par URL

Accédez à l’URL suivante en remplaçant `<ClusterName>` par le nom de cluster Spark du client pour ouvrir le serveur d’historique Spark.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

L’interface utilisateur web du serveur d’historique Spark ressemble à ce qui suit :

![Serveur d’historique HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Onglet des données du serveur d’historique Spark

Sélectionnez l’ID tâche, puis cliquez sur **Données** dans le menu Outil pour ouvrir la Vue de données.

+ Consultez les **Entrées**, **Sorties** et **Opérations de table** en sélectionnant les onglets séparément.

    ![Données pour l’application Spark - Onglets](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copiez toutes les lignes en cliquant sur le bouton **Copier**.

    ![Données pour l’application Spark - Copier](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Enregistrez toutes les données dans un fichier CSV en cliquant sur le bouton **CSV**.

    ![Données pour l’application Spark - Enregistrer](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Entrez des mots clés dans le champ **Rechercher** : le résultat de la recherche s’affiche immédiatement.

    ![Données pour l’application Spark - Rechercher](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Cliquez sur l’en-tête de colonne pour trier la table, sur le signe plus pour développer une ligne et afficher plus de détails, ou sur le signe moins pour réduire une ligne.

    ![Données pour l’application Spark - Table](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Téléchargez un seul fichier en cliquant sur le bouton **Téléchargement partiel** qui se trouve à droite : le fichier sélectionné sera téléchargé vers l’ordinateur local ; s’il n’existe plus, un nouvel onglet s’ouvrira pour afficher les messages d’erreur.

    ![Données pour l’application Spark - Télécharger](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copiez le chemin d’accès complet ou relatif en développant le menu de téléchargement pour sélectionner **Copier le chemin d’accès complet** ou **Copier le chemin d’accès relatif**. Pour les fichiers Azure Data Lake Storage, **Ouvrir dans l’Explorateur Stockage Azure** lance l’Explorateur Stockage Azure et l’ouvre sur le dossier lors de la connexion.

    ![Données pour l’application Spark - Copier le chemin](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Cliquez sur le chiffre sous le tableau pour parcourir les pages quand il y a trop de lignes pour les afficher sur une seule page.

    ![Données pour l’application Spark - Page](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Placez le curseur sur le point d’interrogation sous Données pour afficher l’info-bulle, ou cliquez sur le point d’interrogation pour obtenir plus d’informations.

    ![Données pour l’application Spark - Plus d’informations](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Pour signaler des problèmes, envoyez des commentaires en cliquant sur **Envoyez-nous vos commentaires**.

    ![Graphe Spark - Envoyez-nous vos commentaires](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Onglet de graphique du serveur d’historique Apache Spark

Sélectionnez l’ID tâche, puis cliquez sur **Graphique** dans le menu Outil pour ouvrir la Vue de graphique du travail.

+ Regardez la vue d’ensemble de votre travail offerte par le graphique ainsi généré.

+ Par défaut, il affiche tous les travaux ; vous pouvez le filtrer par **ID tâche**.

    ![Application Spark - ID du travail - Graphe du travail](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ Par défaut, **En cours** est sélectionné ; l’utilisateur peut consulter le flux de données en sélectionnant **Lu/Écrit** dans la liste déroulante **Affichage**.

    ![Application Spark - Graphe du travail - Affichage](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Le nœud du graphique est représenté en couleurs indiquant la carte thermique.

    ![Application Spark - Graphe du travail - Carte thermique](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Lisez le travail en cliquant sur le bouton **Lecture**, et arrêtez quand vous le souhaitez en cliquant sur le bouton Arrêter. La tâche s’affiche dans une couleur indiquant l’état lors de la lecture :

  + Vert pour une opération réussie : Le travail s'est terminé avec succès.
  + Orange pour une nouvelle tentative : Des tâches ont échoué sans que cela n'affecte le résultat final du travail. Ces tâches comportaient des doublons ou des instances de nouvelle tentative susceptibles de réussir par la suite.
  + Bleu pour une tâche en cours d'exécution : La tâche est en cours d'exécution.
  + Blanc pour une tâche en attente ou ignorée : La tâche est en attente d'exécution ou la phase a été ignorée.
  + Rouge pour un échec : La tâche a échoué.

    ![Application Spark - Exemple de couleur du graphe de travail - Exécution](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    La phase ignorée est affichée en blanc.
    ![Application Spark - Exemple de couleur du graphe de travail - Travail ignoré](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Application Spark - Exemple de couleur du graphe de travail - Échec](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > La lecture de chaque travail est autorisée. La lecture n’est pas prise en charge pour un travail incomplet.

+ Utilisez la roulette de défilement pour effectuer un zoom avant/arrière sur le graphique du travail, ou cliquez sur **Zoomer pour ajuster** afin de l’ajuster à l’écran.

    ![Application Spark - Graphe de travail - Zoomer pour ajuster](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Placez le curseur sur le nœud du graphique pour afficher l’info-bulle au niveau des tâches ayant échoué, puis cliquez sur la phase pour ouvrir la page correspondante.

    ![Application Spark - Graphe de travail - Info-bulle](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Dans l’onglet du graphique du travail, une info-bulle et une petite icône sont affichées pour les phases si certaines de leurs tâches répondent aux critères ci-dessous :
  + Asymétrie des données : taille des lectures de données > taille moyenne des lectures de données de toutes les tâches de cette phase * 2, et taille des lectures de données > 10 Mo.
  + Asymétrie temporelle : durée d'exécution > durée moyenne d'exécution de toutes les tâches de cette phase * 2, et durée d'exécution > 2 minutes.

    ![Application Spark - Graphe de travail - Icône de l’asymétrie](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

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

    ![Application Spark - Graphe de travail - Commentaires](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Onglet de diagnostic du serveur d’historique Apache Spark

Sélectionnez l’ID de tâche, puis cliquez sur **Diagnostic** dans le menu des outils pour ouvrir la Vue de diagnostic du travail. L’onglet de diagnostic comporte **l’asymétrie des données**, **l’asymétrie temporelle** et **l’analyse de l’utilisation des exécuteurs**.

+ Consultez **l’asymétrie des données**, **l’asymétrie temporelle** et **l’analyse de l’utilisation des exécuteurs** en sélectionnant chaque onglet respectivement.

    ![Diagnostic SparkUI - Onglet Asymétrie des données](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Asymétrie des données

Cliquez sur l’onglet **Asymétrie des données** pour afficher les tâches asymétriques en fonction des paramètres spécifiés.

+ **Spécifier les paramètres** : la première section affiche les paramètres qui sont utilisés pour détecter l’asymétrie des données. Par défaut, la règle est la suivante : Les données des tâches lues sont 3 fois supérieures aux données des tâches lues en moyenne, et les données des tâches lues sont supérieures à 10 Mo. Si vous souhaitez définir votre propre règle pour les tâches asymétriques, vous pouvez choisir vos propres paramètres, et les sections **Phase asymétrique** et **Graphique de l’asymétrie** sont actualisées en conséquence.

+ **Phase asymétrique** : la seconde section affiche les phases comportant des tâches asymétriques qui répondent aux critères spécifiés ci-dessus. Si plusieurs tâches asymétriques cohabitent dans une phase, la table des phases asymétriques affiche uniquement la tâche la plus décalée (par exemple, le plus important volume de données pour l’asymétrie des données).

    ![Diagnostic SparkUI - Onglet Asymétrie des données](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **Graphique de l’asymétrie** : lorsqu’une ligne de la table des phases asymétriques est sélectionnée, le graphique de l’asymétrie affiche les détails des distributions de tâche en fonction des données lues et de la durée d’exécution. Les tâches asymétriques sont en rouge et les tâches normales sont en bleu. En termes de performances, le graphique affiche jusqu’à 100 exemples de tâches. Les détails de la tâche sont affichés dans le panneau inférieur droit.

    ![SparkUI - Graphique d’asymétrie pour l’étape 10](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Asymétrie temporelle

L’onglet **Asymétrie temporelle** affiche les tâches asymétriques en fonction de la durée d’exécution des tâches.

+ **Spécifier les paramètres** : la première section affiche les paramètres qui sont utilisés pour détecter l’asymétrie temporelle. Par défaut, les critères de détection de l’asymétrie temporelle sont les suivants : la durée d’exécution de la tâche est 3 fois supérieure à la durée d’exécution moyenne et la durée d’exécution de la tâche est supérieure à 30 secondes. Vous pouvez modifier ces paramètres selon vos besoins. Les onglets **Phase asymétrique** et **Graphique de l’asymétrie** affichent les phases correspondantes et les informations sur les tâches, tout comme l’onglet **Asymétrie des données** ci-dessus.

+ Cliquez sur **Asymétrie temporelle** pour afficher les résultats filtrés dans la section **Phase asymétrique**, en fonction des paramètres définis dans la section **Spécifier les paramètres**. Cliquez sur un élément dans la section **Phase asymétrique**. Le graphique correspondant est alors illustré dans la section 3 et les détails de la tâche sont affichés dans le panneau inférieur droit.

    ![SparkUI - Diagnostic - Section Asymétrie temporelle](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Analyse de l’utilisation des exécuteurs

Le graphique portant sur l’utilisation des exécuteurs permet de visualiser l’état d’exécution et de répartition de l’exécuteur réel du travail Spark.  

+ Cliquez sur **Analyse de l’utilisation des exécuteurs** pour afficher quatre types de courbes portant sur l’utilisation des exécuteurs et connaître notamment **les exécuteurs alloués**, **les exécuteurs en cours d’exécution**, **les exécuteurs inactifs** et **les instances maximales d’exécuteur**. Pour les exécuteurs alloués, chaque événement d’ajout ou de suppression d’exécuteur augmente ou diminue le nombre d’exécuteurs alloués. Pour effectuer une comparaison, vous pouvez consulter la chronologie des événements dans l’onglet des travaux.

    ![SparkUI - Diagnostic - Onglet Exécuteurs](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ Cliquez sur l’icône de couleur pour sélectionner ou désélectionner le contenu correspondant dans tous les brouillons.

    ![SparkUI - Diagnostic - Sélectionner le graphique](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Forum Aux Questions

### <a name="1-revert-to-community-version"></a>1. Revenir à la version Communauté

Pour revenir à la version Communauté, suivez les étapes ci-dessous :

1. Ouvrez le cluster dans Ambari. Cliquez sur **Spark2** dans le volet gauche.
2. Cliquez sur l’onglet **Configurations**.
3. Développez le groupe **Custom spark2-defaults**.
4. Cliquez sur **Ajouter une propriété**, ajoutez **spark.ui.enhancement.enabled=false** et enregistrez.
5. La propriété est maintenant définie sur **false**.
6. Pour enregistrer la configuration, cliquez sur **Enregistrer**.

    ![Apache Ambari - Fonctionnalité désactivée](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

7. Cliquez sur **Spark2** dans le volet gauche ; dans l’onglet **Résumé**, cliquez sur **Serveur d’historique Spark2**.

    ![Apache Ambari Spark2 - Récapitulatif](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

8. Redémarrez le serveur d’historique en cliquant sur **Redémarrer** sur le **Serveur d’historique Spark2**.

    ![Apache Ambari Spark2 - Redémarrage de l’historique](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
9. Actualisez l’interface utilisateur web du serveur d’historique Spark : il reviendra à la version Communauté.

### <a name="2-upload-history-server-event"></a>2. Charger un événement du serveur d’historique

Si vous rencontrez une erreur de serveur d’historique, suivez ces étapes pour récupérer l’événement :

1. Téléchargez l’événement en cliquant sur **Télécharger** dans l’interface utilisateur web du serveur d’historique.

    ![Spark2 - Serveur d’historique - Téléchargement](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Cliquez sur **Envoyez-nous vos commentaires** dans l’onglet Données/Graphique.

    ![Graphe Spark - Envoyez-nous vos commentaires](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Indiquez le titre et la description de l’erreur, faites glisser le fichier zip dans le champ d’édition, puis cliquez sur **Soumettre un nouveau problème**.

    ![Apache Spark - Exemple de problème de fichier](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

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

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**Pour utiliser le fichier Bash sur le Portail Azure**

1. Lancez le [portail Azure](https://ms.portal.azure.com), puis sélectionnez votre cluster.
2. Cliquez sur **Actions de script**, puis **Soumettre une nouvelle action de script**. Remplissez le formulaire **Soumettre une action de script**, puis cliquez sur le bouton **Créer**.

    + **Type de script** : sélectionnez **Personnalisé**.
    + **Nom** : spécifiez un nom de script.
    + **URI de script Bash** : chargez le fichier Bash dans le cluster privé, puis copiez-y l’URL. Vous pouvez également utiliser l’URI fourni.

   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

   + Regardez sur **Principal** et **Travail**.
   + **Paramètres** : définissez les paramètres suivant l’utilisation de l’interpréteur de commandes.

     ![Portail Azure - Envoyer une action de script](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problèmes connus

1. Pour le moment, cela ne fonctionne que pour les clusters Spark 2.3 et 2.4.

2. Les données d’entrée/de sortie avec RDD ne s’affichent pas dans l’onglet Données.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer les ressources d’un cluster Apache Spark sur HDInsight](apache-spark-resource-manager.md)
* [Configurer les paramètres d’Apache Spark](apache-spark-settings.md)

## <a name="contact-us"></a>Nous contacter

Si vous avez des commentaires, ou que vous rencontrez d’autres problèmes dans l’utilisation de cet outil, envoyez un e-mail à l’adresse [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com).
