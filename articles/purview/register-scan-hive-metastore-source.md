---
title: Inscrire une base de données de metastore Hive et configurer des analyses dans Azure Purview
description: Cet article explique comment inscrire une base de données de metastore Hive dans Azure Purview et configurer une analyse.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 5/17/2021
ms.openlocfilehash: b259ef022d2fca8f6531a35eca619ef890019ff3
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072774"
---
# <a name="register-and-scan-hive-metastore-database"></a>Inscrire et analyser une base de données de metastore Hive

Cet article explique comment inscrire une base de données de metastore Hive dans Purview et configurer une analyse.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La source metastore Hive prend en charge une analyse complète pour extraire les métadonnées d’une **base de données de metastore Hive** et récupère la traçabilité entre les ressources de données. Les plateformes prises en charge sont Apache Hadoop, Cloudera, Hortonworks et Databricks.

## <a name="prerequisites"></a>Prérequis

1.  Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
    Pour plus d’informations, consultez [Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assurez-vous que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

3.  Vérifiez que le package \"Visual C++ Redistributable 2012 Update 4\" est installé sur la machine dotée du runtime d’intégration auto-hébergé. S\'il n’est pas déjà installé, vous pouvez le télécharger [ici](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Vous devrez télécharger manuellement le pilote JDBC de la base de données de metastore Hive sur votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Par exemple, si la base de données utilisée est mssql, veillez à télécharger le [pilote JDBC pour SQL Server de Microsoft](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

5.  Les versions de Hive prises en charge sont 2.x à 3.x.

## <a name="setting-up-authentication-for-a-scan"></a>Configuration de l’authentification pour une analyse

La seule authentification prise en charge pour une base de données de metastore Hive est l’**authentification de base**.

## <a name="register-a-hive-metastore-database"></a>Inscrire une base de données de metastore Hive

Pour inscrire une nouvelle base de données de metastore Hive dans votre catalogue de données, effectuez les actions suivantes :

1.  Accédez à votre compte Purview.

2.  Sélectionnez **Sources** dans la barre de navigation à gauche.

3.  Sélectionnez **Inscrire**.

4.  Dans Inscrire des sources, sélectionnez **Metastore** Hive. Sélectionnez **Continuer.**

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="Inscrire une source Hive" border="true":::

Dans l’écran Inscrire des sources (Metastore Hive), effectuez les actions suivantes :

1.  Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

2.  Entrez l’**URL du cluster Hive**. L’URL du cluster peut être obtenue à partir de l’URL d’Ambari ou de l’URL de l’espace de travail Databricks. Par exemple, hive.azurehdinsight.net ou adb-19255636414785.5.azuredatabricks.net

3.  Entrez l’**URL du serveur Metastore Hive.** Par exemple, sqlserver://hive.database.windows.net ou jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443

4.  Sélectionnez une collection ou créez-en une (facultatif)

5.  Terminez pour inscrire la source de données.

       :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="Configurer une source Hive" border="true":::


## <a name="creating-and-running-a-scan"></a>Création et exécution d’une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1.  Dans le centre d’administration, cliquez sur Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé

2.  Accédez aux **Sources**.

3.  Sélectionnez la base de données **Metastore Hive** inscrite.

4.  Sélectionnez **+ Nouvelle analyse**.

5.  Fournissez les renseignements ci-dessous :

    a.  **Nom** : nom de l’analyse

    b.  **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    c.  **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :

    -   Sélectionnez Authentification de base quand vous créez des informations d’identification.
    -   Spécifiez le nom d’utilisateur du metastore dans le champ d’entrée Nom d’utilisateur
    -   Stockez le mot de passe du metastore dans la clé secrète.

    Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md). 

    **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**

    Le nom d’utilisateur et le mot de passe sont accessibles à partir des deux propriétés, comme indiqué ci-dessous.

    :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    d. **Emplacement du pilote JDBC Metastore** : spécifiez le chemin de l’emplacement du pilote JDBC sur votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Ce doit être le chemin vers l’emplacement du dossier des fichiers JAR valides.

    Si vous analysez Databricks, reportez-vous à la section sur Databricks ci-dessous.

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne procédez pas à l’installation dans un compte d’utilisateur.

    e.  **Classe du pilote JDBC Metastore** : spécifiez le nom de la classe du pilote de connexion. Par exemple : \com.microsoft.sqlserver.jdbc.SQLServerDriver.
    
    **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**
    
    La classe du pilote est accessible à partir de la propriété, comme indiqué ci-dessous.
    :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    f.  **URL JDBC du metastore** : spécifiez la valeur de l’URL de connexion et définissez la connexion à l’URL du serveur de base de données du metastore. Par exemple : jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300

    **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**
    
    L’URL JDBC est accessible à partir de la propriété URL de connexion, comme indiqué ci-dessous.
    :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::

    À cette URL, ajoutez le chemin de l’emplacement où le certificat SSL est placé sur votre machine virtuelle. Le certificat SSL peut être téléchargé à partir d’[ici](../mysql/howto-configure-ssl.md).

    L’URL JDBC du metastore est donc :
    
    jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&amp;useSSL=true&sslCA=D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem

    g.  **Nom de la base de données du metastore** : spécifiez le nom de la base de données du metastore Hive
    
    Si vous analysez Databricks, reportez-vous à la section sur Databricks ci-dessous.

    **Utilisation de Databricks** : accédez à votre cluster Databricks -> Applications -> Lancer le terminal web. Exécutez l’applet de commande **cat /databricks/hive/conf/hive-site.xml**

    Le nom de la base de données est accessible à partir de la propriété URL JDBC, comme indiqué ci-dessous. Par exemple : organization1829255636414785 :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    h.  **Schéma** : spécifiez une liste de schémas Hive à importer. Par exemple : schema1; schema2. 
    
    Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut. 

    Si la liste est vide, tous les schémas disponibles sont importés.
    Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %, par exemple A%; %B; %C%; D

    -   commençant par A ou    
    -   se terminant par B ou    
    -   contenant C ou    
    -   égalant D

    L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

    i.  **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la base de données du metastore Hive à analyser.
    > [!Note]
    > **Pour analyser le metastore Databricks**
    >

    :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="analyser une source Hive" border="true":::

6.  Cliquez sur **Continuer**.

7.  Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

8.  Vérifiez votre analyse et cliquez sur **Enregistrer et exécuter**.

## <a name="viewing-your-scans-and-scan-runs"></a>Affichage des analyses et des exécutions d’analyse

1. Accédez au centre d’administration. Sélectionnez **Sources de données** sous la section **Sources et analyse**.

2. Sélectionnez la source de données souhaitée. La liste des analyses existantes sur cette source de données apparaît.

3. Sélectionnez l’analyse dont vous souhaitez afficher les résultats.

4. Cette page affiche toutes les exécutions précédentes de l’analyse ainsi que les métriques et l’état de chaque exécution de l’analyse. Elle indique également si votre analyse a été planifiée ou était manuelle, le nombre de ressources pour lesquelles ont été appliquées des classifications, le nombre total de ressources découvertes, l’heure de début et de fin de l’analyse et la durée totale de l’analyse.

## <a name="manage-your-scans"></a>Gestion de vos analyses

Pour gérer ou supprimer une analyse, effectuez les opérations suivantes :

1. Accédez au centre d’administration. Sélectionnez **Sources de données** sous la section **Sources et analyse**, puis sélectionnez la source de données souhaitée.

2. Sélectionnez l’analyse que vous souhaitez gérer. Vous pouvez modifier l’analyse en sélectionnant **Modifier**.

3. Vous pouvez supprimer votre analyse en sélectionnant **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Navigation dans le catalogue de données Azure Purview](how-to-browse-catalog.md)
- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
