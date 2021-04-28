---
title: Référence des paramètres de configuration d’Azure HDInsight
description: Présentation de la configuration de l’extension Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: e5bfa66f7c7d22617c17c6bd57eff373574021f4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142060"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Référence des paramètres de configuration d’Azure HDInsight

L’extension Outils Spark et Hive pour Visual Studio Code est hautement configurable. Cette page décrit les paramètres clés que vous pouvez utiliser.  

Pour des informations générales sur l’utilisation des paramètres dans VS Code, consultez [Paramètres de l’utilisateur et de l’espace de travail](https://code.visualstudio.com/docs/getstarted/settings) et la [Référence des variables](https://code.visualstudio.com/docs/editor/variables-reference) pour plus d’informations sur la prise en charge des variables prédéfinies.

## <a name="open-the-azure-hdinsight-configuration"></a>Ouvrir la configuration Azure HDInsight

1. Ouvrez d’abord un dossier pour créer les paramètres de l’espace de travail.
2. Appuyez sur **Ctrl + Maj + P** ou accédez à **Affichage** -> **Palette de commandes...** pour afficher toutes les commandes.
3. Recherchez **Définir la configuration**.
4. Développez **Extensions** dans le répertoire de gauche, puis sélectionnez **Configuration de HDInsight**.

 ![Image de Configuration de HDI](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Paramètres généraux :   

|  Propriété   | Default | Description   |
| ----- | ----- |----- |
| HDInsight : Environnement Azure | Azure | Environnement Azure |
| HDInsight : Désactiver le lien Ouvrir l’enquête | Activée | Activer/désactiver l’ouverture de l’enquête HDInsight |
| HDInsight : Activer Ignorer l’installation de PySpark | Désactivé | Activer/désactiver Ignorer l’installation de Pyspark |
| HDInsight : Activer les conseils de connexion | Désactivé | Lorsque cette option est cochée, une invite s’affiche lors de la connexion à Azure |
| HDInsight : Version précédente de l’extension | Afficher le numéro de version de l’extension actuelle | Afficher la version précédente de l’extension|
| HDInsight : Famille de polices des résultats | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | Définir la famille de polices de la grille de résultats, laisser vide pour utiliser la police de l’éditeur |
| HDInsight : Taille de police des résultats | 13 |Définir la taille de police de la grille de résultats, laisser vide pour utiliser la taille de l’éditeur |
| Cluster HDInsight : Cluster lié | -- | URL des clusters liés. Vous pouvez également modifier le fichier JSON à définir |
| HDInsight Hive : Appliquer la localisation | Désactivé | [Facultatif] Options de configuration pour effectuer la localisation dans les paramètres régionaux configurés dans VSCode (vous devez redémarrer VSCode pour appliquer les paramètres)|
| HDInsight Hive : Copier les en-têtes include | Désactivé | [Facultatif] Option de configuration pour copier les résultats de la Vue Résultats |
| HDInsight Hive : Copier Supprimer nouvelle ligne | Activée | [Facultatif] Options de configuration pour copier les résultats multilignes de la vue Résultats |
| HDInsight Hive › Format : Aligner les définitions de colonne dans les colonnes | Désactivé | Spécifie si les définitions de colonne doivent être alignées |
| HDInsight Hive › Format : Casse du type de données | aucun | Spécifie si la mise en forme des types de données est MAJUSCULES, minuscules ou aucune (sans mise en forme) |
| HDInsight Hive › Format : Casse des mots clés | aucun | Spécifie si la mise en forme des mots clés est MAJUSCULES, minuscules ou aucune (sans mise en forme) |
| HDInsight Hive › Format : Placer des virgules avant l’instruction suivante | Désactivé | Spécifie si des virgules doivent être placées au début de chaque instruction dans une liste, par exemple ’,mycolumn2’ plutôt qu’à la fin, par exemple ’mycolumn1,’|
| HDInsight Hive › Format : Placer les références de l’instruction SELECT sur une nouvelle ligne | Désactivé | Les références à des objets dans une instruction SELECT doivent-elles être fractionnées en lignes distinctes ? Par exemple, pour « SELECT C1, C2 FROM T1 », C1 et C2 se trouveront sur des lignes distinctes
| HDInsight Hive : Informations de débogage du journal | Désactivé | [Facultatif] Journaliser la sortie de débogage dans la console VS Code (Aide -> Activer/désactiver les outils de développement) 
| Hive HDInsight : Messages ouverts par défaut | Activée | True pour ouvrir par défaut le volet de messages, false pour le fermer|
| HDInsight Hive : Famille de polices des résultats | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | Définir la famille de polices de la grille de résultats, laisser vide pour utiliser la police de l’éditeur |
| HDInsight Hive : Taille de police des résultats | 13 | Définir la taille de police de la grille de résultats, laisser vide pour utiliser la taille de l’éditeur |
| HDInsight Hive › Enregistrer en tant que CSV : Inclure les en-têtes | Activée | [Facultatif] Quand la valeur est true, les en-têtes de colonnes sont inclus dans l’enregistrement des résultats au format CSV |
| HDInsight Hive : Raccourcis | -- | Raccourcis associés à la fenêtre de résultats |
| HDInsight Hive : Afficher le temps du lot| Désactivé | [Facultatif] Indique si la durée d’exécution doit être indiquée pour chaque lot |
| HDInsight Hive : Sélection de volet fractionné | Suivant | [Facultatif] Options de configuration pour définir la colonne dans laquelle les nouveaux volets de résultats doivent s’ouvrir |
| Envoi de tâche HDInsight : Configuration du cluster | -- | Configuration de cluster |
| Envoi de tâche HDInsight : Configuration de Livy | -- | Configuration de Livy. POST/batches |
| HDInsight Jupyter : Ajouter les résultats| Activée | Indique s’il faut ajouter les résultats à la fenêtre de résultats, ou effacer et afficher. |
| HDInsight Jupyter : Langues | -- | Paramètres par défaut par langue. |
| HDInsight Jupyter › Journal : Verbose | Désactivé | Si la journalisation verbose est activée |
| HDInsight Jupyter › Notebook : Arguments de démarrage | Peut ajouter un élément | Arguments de ligne de commande ’jupyter notebook’ Chaque argument est un élément distinct dans le tableau. Pour une liste complète, entrez ’jupyter notebook--help’ dans une fenêtre de terminal. |
| HDInsight Jupyter › Notebook : Dossier de démarrage | ${workspaceRoot} |-- |
| HDInsight Jupyter : Extension Python activée | Activée | Utilisez Python-Interactive-Window de l’extension ms-python lors de l’envoi de travaux interactifs pySpark. Sinon, utilisez notre propre fenêtre Jupyter |
| HDInsight Spark.NET : 7z | C:\Program Files\7-Zip | <Chemin vers 7z.exe> |
| HDInsight Spark.NET : HADOOP_HOME | D:\winutils | <Chemin vers bin\winutils.exe> Windows uniquement |
| HDInsight Spark.NET : JAVA_HOME | C:\Program Files\Java\jdk1.8.0_201\ | Chemin de base de Java|
| HDInsight Spark.NET : SCALA_HOME | C:\Program Files (x86)\scala\ | Chemin de base de Scala |
| HDInsight Spark.NET : SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Chemin de base de Spark |
| Hive : Conserver les onglets de résultat de requête | Désactivé | Hive PersistQueryResultTabs |
| Hive : Sélection de volet fractionnée | Suivant | [Facultatif] Options de configuration pour définir la colonne dans laquelle les nouveaux volets de résultats doivent s’ouvrir |
| Hive interfactif : Copier dossier exécutable | Désactivé | S’il faut copier le dossier du runtime du service interactif Hive dans le dossier tmp de l’utilisateur |
| Serveur interactif Hql : Port wrapper | 13424 | Port de service interactif Hive |
| Serveur de langage HQL : Port wrapper de langue | 12342 | Le port de service de langage Hive que les serveurs écoutent. |
| Serveur de langage HQL : Nombre maximal de problèmes | 100 | Contrôle le nombre maximal de problèmes générés par le serveur. |
| Calcul Synapse Spark : Environnement Azure du calcul Synapse Spark | blank | Environnement Azure du calcul Synapse Spark |
| Envoi de tâche de pool Synapse Spark : Configuration de Livy | -- | Configuration de Livy. POST/batches
| Envoi de tâches de pool Synapse Spark : Configuration du cluster de pool Synapse Spark | -- | Configuration du pool Synapse Spark |


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur Azure HDInsight pour VSCode, consultez [ Outils Spark et Hive pour Visual Studio Code](/sql/big-data-cluster/spark-hive-tools-vscode).
- Pour voir une vidéo de démonstration sur l’utilisation de Spark et Hive pour Visual Studio Code, consultez [Spark et Hive pour Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).