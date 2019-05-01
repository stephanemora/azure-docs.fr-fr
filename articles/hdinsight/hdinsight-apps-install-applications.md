---
title: Installer des applications tierces sur Azure HDInsight
description: Découvrez comment installer des applications Hadoop tierces sur Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: 7cbcc8ac05e4541406abd08c14006a8abc0c91ca
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699305"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installer des applications Apache Hadoop tierces sur Azure HDInsight

Découvrez comment installer une application [Apache Hadoop](https://hadoop.apache.org/) tierce sur Azure HDInsight. Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même.  

La liste suivante affiche les applications publiées :

|Application |Ou les types de cluster | Description |
|---|---|---|
|AtScale Intelligence Platform |Hadoop |AtScale Active votre cluster HDInsight sur un serveur OLAP de montée en puissance, ce qui vous permet d’interroger des milliards de lignes de données de manière interactive à l’aide d’outils décisionnels vous connaissez, propriétaire et appréciez déjà – à partir de Microsoft Excel, Power BI, Tableau Software ou qlikview. |
|CDAP 4.2, 4.3 pour HDInsight |hbase |CDAP est la première plate-forme d’intégration pour le big data qui accélère la valeur pour Hadoop et permet au service informatique fournir des données en libre-service. Source ouverte et extensible, CDAP supprime les barrières à l’innovation. Requirements: 4 nœuds de région, min D3 v2. |
|Datameer |Hadoop |Libre-service plateforme évolutive du Datameer pour la préparation, l’exploration et qui régissent vos données pour l’analytique accélère transformer des données multisource complexes en informations métier précieuses, fournir des insights plus rapides et plus réfléchies à une échelle de l’entreprise. |
|Dataiku DSS sur HDInsight |Hadoop, Spark |Dataiku DSS à une plateforme de science des données enterprise qui permet aux chercheurs de données et analystes de données collaborent pour concevoir et exécuter des services et nouveaux produits de données plus efficacement, transformer des données brutes en prédictions percutantes. |
|WANdisco Fusion HDI App - 2.12.3, 2.12.1, point 2.11.2 |Hadoop, Spark, HBase, Storm, Kafka |Conservation des données cohérente dans un environnement distribué est un défi d’opérations de données volumineuses. WANdisco Fusion, une plateforme de logiciels d’entreprise, résout ce problème en activant la cohérence des données non structurées sur n’importe quel environnement. |
|SparklingWater H2O pour HDInsight |Spark |H2O Sparkling Water prend en charge les algorithmes distribués suivants : GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, détection d’anomalie, Autoencoders. |
|Striim pour l’intégration de données en temps réel et HDInsight |Hadoop, HBase, Storm, Spark, Kafka |Striim (prononcé « stream ») est un end-to-end intégration de données + plateforme d’analyse décisionnelle de diffusion en continu, l’activation d’ingestion en continu, le traitement et l’analytique de flux de données disparates. |
|Jumbune pour HDInsight |Hadoop, Spark |À un niveau élevé, Jumbune aide les entreprises de 1. Accélération de Tez, moteur MapReduce et Spark basés Hive, Java, les performances de charge de travail Scala. 2. Cluster Hadoop proactive analyse, 3. L’établissement d’une gestion de qualité des données sur le système de fichiers distribués. |
|Kyligence Enterprise |Hadoop, HBase, Spark |Kyligence entreprise s’appuyant sur Apache Kylin, permet de BI Big Data. Comme un moteur OLAP enterprise sur Hadoop, Kyligence entreprise met à votre disposition analyste d’entreprise à l’architecture de BI sur Hadoop avec l’entrepôt de données standard et à la méthodologie de BI. |
|Spark Job Server for KNIME Spark Executor |Spark |Spark Job Server for KNIME Spark Executor permet de connecter la plateforme d’Analytique KNIME aux clusters de HDInsight. |
|Étoile Presto sur Azure HDInsight, Presto étoile (v0.213-e) |Hadoop |Presto est un moteur de requête de le SQL de distribuée rapide et évolutif. Conçu pour séparer le calcul du stockage, Presto est parfait pour interroger des données dans les bases de données Azure Data Lake Storage, Stockage Blob Azure, SQL et NoSQL et autres sources de données. |
|StreamSets Data Collector pour HDInsight Cloud |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector est un moteur léger, puissant qui diffuse les données en temps réel. Utiliser le collecteur de données à l’itinéraire et traiter les données dans votre flux de données. Il est fourni avec une licence d’essai de 30 jours. |
|[Trifacta Wrangler Enterprise](https://www.trifacta.com/) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise pour HDInsight prend en charge les données d’entreprise wrangling pour n’importe quelle échelle des données. Le coût d’exécution Trifacta sur Azure est une combinaison de coûts d’abonnement Trifacta ainsi que les coûts d’infrastructure Azure pour les machines virtuelles. |
|Plateforme de données d’UNIFI 3.1 |Hadoop, HBase, Storm, Spark |La plateforme de données Unifi est une suite intégrée libre-service d’outils de données conçu pour permettre à l’utilisateur des activités à relever les défis de données générer des revenus incrémentiels, réduire les coûts ou la complexité opérationnelle. |
|Unraveldata APM |Spark |Élucidez application de données pour un cluster HDInsight Spark. |
|Catalogue de données de finition |Spark |Finition catalogues, organise et régit les données à l’aide d’intelligence artificielle pour auto-baliser des données avec des termes professionnels. Le catalogue Waterline est un composant important pour la réussite de l’analytique en libre-service, de la conformité et de la gouvernance et des différentes initiatives de gestion informatique. |

Les instructions fournies dans cet article utilisent le portail Azure. Vous pouvez également exporter le modèle Azure Resource Manager à partir du portail ou obtenir une copie de ce modèle auprès des fournisseurs et utiliser Azure PowerShell et Azure Classic CLI pour déployer le modèle.  Consultez [Créer des clusters Apache Hadoop dans HDInsight avec des modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Conditions préalables
Pour installer des applications HDInsight sur un cluster HDInsight existant, vous devez disposer d’un cluster HDInsight. Pour en créer un, consultez [Créer des clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). Vous pouvez également installer des applications HDInsight lorsque vous créez un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installer des applications dans des clusters existants
La procédure suivante montre comment installer des applications HDInsight dans un cluster HDInsight existant.

**Installer une application HDInsight**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, accédez à **tous les services** > **Analytique** > **HDInsight clusters**.
3. Sélectionnez un cluster HDInsight à partir de la liste.  Si vous ne disposez pas d’un tel cluster, vous devez d’abord en créer un.  Consultez la page [Créer des clusters](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Sous le **paramètres** catégorie, sélectionnez **Applications**. Vous pouvez voir une liste des applications installées dans la fenêtre principale. 
   
    ![Applications HDInsight - menu du portail](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Sélectionnez **+ ajouter** à partir du menu. Vous pouvez voir une liste des applications disponibles.  Si **+ ajouter** est grisée, que signifie qu’il y a aucune application pour cette version du cluster HDInsight.
   
    ![Applications HDInsight - applications disponibles](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Sélectionnez une des applications disponibles, puis suivez les instructions pour accepter les conditions juridiques.

Vous pouvez voir l’état d’installation à partir des notifications du portail (cliquez sur l’icône représentant une cloche en haut du portail). L’application installée s’affiche dans la liste Applications installées.

## <a name="install-applications-during-cluster-creation"></a>Installer des applications lors de la création du cluster
Vous pouvez également installer des applications HDInsight lorsque vous créez un cluster. Au cours du processus, les applications HDInsight sont installées une fois le cluster créé et en cours d’exécution. Pour installer des applications pendant la création du cluster à l’aide du portail Azure, vous utilisez le **personnalisé** option au lieu de la valeur par défaut **création rapide** option.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Répertorier les applications HDInsight installées et les propriétés
Le portail affiche une liste des applications HDInsight installées pour un cluster et les propriétés de chaque application installée.

**Répertorier une application HDInsight et afficher des propriétés**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, accédez à **tous les services** > **Analytique** > **HDInsight clusters**.
3. Sélectionnez un cluster HDInsight à partir de la liste.
4. Sous le **paramètres** catégorie, sélectionnez **Applications**. Vous pouvez voir une liste des applications installées dans la fenêtre principale. 
   
    ![Applications HDInsight - applications installées](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Sélectionnez une des applications installées pour afficher la propriété. Listes des propriétés :

    |Propriété | Description |
    |---|---|
    |Nom de l’application |Nom de l’application. |
    |Statut |État de l’application. |
    |Page web |L’URL de l’application web que vous avez déployée sur le nœud de périphérie. Les informations d’identification sont les mêmes que celles de l’utilisateur HTTP configurées pour le cluster. |
    |Point de terminaison SSH |Vous pouvez utiliser SSH pour vous connecter au nœud de périphérie. Les informations d’identification sont les mêmes que celles de l’utilisateur SSH configurées pour le cluster. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Description | Description de l’application. |

6. Pour supprimer une application, cliquez avec le bouton droit sur l’application, puis sur **Supprimer** dans le menu contextuel.

## <a name="connect-to-the-edge-node"></a>Connexion au nœud de périmètre
Vous pouvez vous connecter au nœud de périmètre à l’aide de HTTP et SSH. Vous trouverez les informations de point de terminaison sur le [portail](#list-installed-hdinsight-apps-and-properties). Pour en savoir plus, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Les informations d’identification du point de terminaison HTTP sont les informations d’identification de l’utilisateur HTTP configurées pour le cluster HDInsight ; les informations d’identification du point de terminaison SSH sont les informations d’identification SSH configurées pour le cluster HDInsight.

## <a name="troubleshoot"></a>Résolution des problèmes
Consultez [Dépanner l’installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Étapes suivantes
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md): découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight](hdinsight-apps-publish-applications.md) : découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN : Installer une application HDInsight](https://msdn.microsoft.com/library/mt706515.aspx) : découvrez comment définir les applications HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md): apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Créer des clusters Apache Hadoop sous Linux dans HDInsight à l’aide de modèles Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) : apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.
* [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md): apprenez à utiliser un nœud de périmètre vide pour accéder au cluster HDInsight, tester des applications HDInsight et héberger des applications HDInsight.

