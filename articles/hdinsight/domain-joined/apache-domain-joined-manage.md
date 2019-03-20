---
title: Gérer des clusters HDInsight avec le pack Sécurité Entreprise - Azure
description: Découvrez comment gérer des clusters HDInsight avec le pack Sécurité Entreprise.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 0fec9173eb94aa60c3689089e4eacd2d9d47724a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200019"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Gérer des clusters HDInsight avec le pack Sécurité Entreprise
Découvrez les utilisateurs et les rôles du pack Sécurité Entreprise (ESP) HDInsight et apprenez à gérer des clusters ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Utiliser VSCode pour établir un lien avec un cluster joint à un domaine

Vous pouvez lier un cluster normal à l’aide du nom d’utilisateur Apache Ambari managé, mais également lier un cluster de sécurité Apache Hadoop à l’aide du nom d’utilisateur de domaine (par exemple : user1@contoso.com).
1. Ouvrez la palette de commandes en appuyant sur **Ctrl+Maj+P**, puis entrez **HDInsight : Link a cluster**.

   ![commande de lien du cluster](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Entrez HDInsight cluster URL -> input Username -> input Password -> select cluster type -> un indicateur indique la réussite de l’opération si la vérification est passée.
   
   ![boîte de dialogue de lien du cluster](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Le nom d’utilisateur lié et son mot de passe sont utilisés si le cluster est à la fois connecté à un abonnement Azure et lié à un cluster. 
   
3. Vous pouvez voir un cluster lié à l’aide de la commande **List cluster**. Vous pouvez désormais soumettre un script à ce cluster lié.

   ![cluster lié](./media/apache-domain-joined-manage/linked-cluster.png)

4. Vous pouvez également dissocier un cluster en entrant **HDInsight : Unlink a cluster** dans la palette de commandes.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Utiliser IntelliJ pour établir un lien avec un cluster joint à un domaine

Vous pouvez lier un cluster normal à l’aide du nom d’utilisateur Ambari managé, mais également lier un cluster de sécurité Hadoop à l’aide du nom d’utilisateur de domaine (par exemple : user1@contoso.com). 
1. Cliquez sur **Lier un cluster** dans **Azure Explorer**.

   ![menu contextuel de lien du cluster](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Saisissez le **nom du cluster**, le **nom d’utilisateur** et le **mot de passe**. Si l’authentification a échoué, vous devez vérifier le nom d’utilisateur et le mot de passe. Si vous le souhaitez, ajoutez un compte de stockage, une clé de stockage, puis sélectionnez un conteneur dans le conteneur de stockage. Les informations de stockage concernent l’explorateur de stockage dans l’arborescence de gauche.
   
   ![boîte de dialogue de lien du cluster](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Nous utilisons la clé de stockage liée, le nom d’utilisateur et le mot de passe si le cluster est à la fois connecté sur un abonnement Azure et lié à un cluster.
   > ![explorateur de stockage dans IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Si les informations d’entrée sont correctes, vous pouvez voir un cluster lié dans le nœud **HDInsight**. Vous pouvez désormais soumettre une application à ce cluster lié.

   ![cluster lié](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Vous pouvez également dissocier un cluster à partir de **Azure Explorer**.
   
   ![cluster non lié](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Utiliser Eclipse pour établir un lien avec un cluster joint à un domaine

Vous pouvez lier un cluster normal à l’aide du nom d’utilisateur Ambari managé, mais également lier un cluster de sécurité Hadoop à l’aide du nom d’utilisateur de domaine (par exemple : user1@contoso.com).
1. Cliquez sur **Lier un cluster** dans **Azure Explorer**.

   ![menu contextuel de lien du cluster](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Entrez le **Nom du cluster**, le **Nom d’utilisateur** et le **Mot de passe**, puis cliquez sur le bouton OK pour lier le cluster. Si vous le souhaitez, entrez un compte de stockage, une clé de stockage, puis sélectionnez un conteneur de stockage pour que l’explorateur de stockage fonctionne avec l’arborescence de gauche.
   
   ![boîte de dialogue de lien du cluster](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Nous utilisons la clé de stockage liée, le nom d’utilisateur et le mot de passe si le cluster est à la fois connecté sur un abonnement Azure et lié à un cluster.
   > ![Explorateur de stockage dans Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Vous pouvez voir un cluster lié dans le nœud **HDInsight** après avoir cliqué sur le bouton OK, si les informations d’entrée sont correctes. Vous pouvez désormais soumettre une application à ce cluster lié.

   ![cluster lié](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Vous pouvez également dissocier un cluster à partir de **Azure Explorer**.
   
   ![cluster non lié](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Accédez aux clusters avec Enterprise Security Package.

Enterprise Security Package (anciennement HDInsight Premium) permet à plusieurs utilisateurs d’accéder au cluster avec une authentification gérée par Active Directory et une autorisation exécutée par Apache Ranger et les ACL Storage (ACL ADLS). L’autorisation définit des limites sécurisées entre plusieurs utilisateurs et permet uniquement aux utilisateurs disposant de privilèges d’accéder aux données en fonction des stratégies d’autorisation.

La sécurité et l’isolement des utilisateurs sont des aspects importants pour un cluster HDInsight qui utilise Enterprise Security Package. Pour répondre à ces exigences, l’accès SSH au cluster avec Enterprise Security Package est bloqué. Le tableau suivant présente les méthodes d’accès recommandées pour chaque type de cluster :

|Charge de travail|Scénario|Méthode d’accès|
|--------|--------|-------------|
|Apache Hadoop|Hive – Travaux/requêtes interactifs  |<ul><li>[Beeline](#beeline)</li><li>[Affichage Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Travaux/requêtes interactifs, PySpark interactif|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin avec Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Affichage Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Scénarios de traitement par lots – Spark Submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Requête interactive (LLAP)|Interactive|<ul><li>[Beeline](#beeline)</li><li>[Affichage Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Quelconque|Installer une application personnalisée|<ul><li>[Actions de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter n’est pas installé/pris en charge dans Pack Sécurité Entreprise.

L’utilisation d’API standard est utile en matière de sécurité. Vous bénéficiez en prime des avantages suivants :

1.  **Gestion** : vous pouvez gérer votre code et automatiser vos travaux à l’aide des API standard, comme Livy, HS2, etc.
2.  **Audit** : avec SSH, il n’y a aucun moyen de contrôler qui utilise SSH pour accéder au cluster. La situation est différente lorsque les travaux sont construits via des points de terminaison standard puisqu’ils sont exécutés dans le contexte de l’utilisateur. 



### <a name="beeline"></a>Utiliser BeeLine 
Installez Beeline sur votre ordinateur et connectez-vous via l’Internet public, puis utilisez les paramètres suivants : 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Si vous avez Beeline installé localement et que vous vous connectez sur un réseau virtuel Azure, utilisez les paramètres suivants : 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Pour rechercher le nom de domaine complet d’un nœud principal, utilisez les informations contenues dans le document Gérer des clusters HDInsight à l’aide de l’API REST d’Ambari.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Utilisateurs de clusters HDInsight avec ESP
Pendant la création d’un cluster HDInsight non ESP, deux comptes d’utilisateurs sont créés :

* **Administrateur Ambari** : ce compte est également appelé *utilisateur Hadoop* ou *utilisateur HTTP*. Ce compte peut être utilisé pour vous connecter à Ambari à l’adresse https://&lt;clustername >. azurehdinsight.net. Il peut également être utilisé pour exécuter des requêtes sur des vues Ambari, exécuter des travaux à l’aide d’outils externes (par exemple, PowerShell, Templeton, Visual Studio) et s’authentifier avec le pilote ODBC Hive et les outils décisionnels (par exemple, Excel, Power BI ou Tableau).

Un cluster HDInsight avec ESP compte trois nouveaux utilisateurs, en plus de l’administrateur Ambari.

* **Administrateur Ranger** :  ce compte est le compte administrateur Apache Ranger local. Il ne s’agit pas d’un utilisateur de domaine Active Directory. Ce compte peut être utilisé pour configurer des stratégies et d’autres administrateurs utilisateurs ou administrateurs délégués (pour que ces utilisateurs puissent gérer les stratégies). Par défaut, le nom d’utilisateur est *admin* et le mot de passe est le même que le mot de passe administrateur Ambari. Le mot de passe peut être mis à jour à partir de la page Paramètres dans Ranger.
* **Utilisateur de domaine administrateur de cluster** : ce compte est un utilisateur de domaine Active Directory désigné comme administrateur du cluster Hadoop, y compris Ambari et Ranger. Vous devez fournir les informations d’identification de cet utilisateur lors de la création du cluster. Cet utilisateur possède les privilèges suivants :

  * Joindre des machines au domaine et les placer dans l’unité d’organisation que vous spécifiez lors de la création du cluster.
  * Créer des principaux de service au sein de l’unité d’organisation que vous spécifiez lors de la création du cluster.
  * Créer des entrées DNS inversées.

    Notez que les autres utilisateurs d’Active Directory possèdent également ces privilèges.

    Il existe des points de terminaison au sein du cluster (par exemple, Templeton) qui ne sont pas gérés par Ranger et ne sont donc pas sécurisés. Ces points de terminaison sont verrouillés pour tous les utilisateurs, à l’exception de l’utilisateur de domaine administrateur du cluster.
* **Standard** : pendant la création du cluster, vous pouvez fournir plusieurs groupes Active Directory. Les utilisateurs de ces groupes sont synchronisés avec Ranger et Ambari. Ce sont des utilisateurs de domaine qui ont accès uniquement aux points de terminaison gérés par Ranger (par exemple, Hiveserver2). Les stratégies RBAC et les audits sont tous applicables à ces utilisateurs.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Rôles des clusters HDInsight avec ESP
Le pack Sécurité Entreprise HDInsight contient les rôles suivants :

* Administrateur de cluster
* Opérateur de cluster
* Administrateur de services
* Opérateur de service
* Utilisateur de cluster

**Pour voir les autorisations de ces rôles.**

1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Rôles**.
3. Cliquez sur le point d’interrogation bleu pour afficher les autorisations :

    ![Autorisations des rôles HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Ouverture de l’interface utilisateur de gestion Ambari

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez votre cluster HDInsight.
3. Cliquez sur **Tableau de bord** dans le menu du haut pour ouvrir Ambari.
4. Connectez-vous à Ambari à l’aide du nom d’utilisateur de cluster administrateur domaine et le mot de passe.
5. Cliquez sur le menu déroulant **Admin** (Administrateur) dans l’angle supérieur droit, puis sur **Manage Ambari** (Gérer Ambari).

    ![Gestion de HDInsight ESP avec Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    L’interface utilisateur ressemble à ce qui suit :

    ![Interface utilisateur de gestion Ambari pour HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Énumération des utilisateurs du domaine synchronisés à partir d’Active Directory
1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Utilisateurs**. Vous devriez voir tous les utilisateurs synchronisés à partir d’Active Directory sur le cluster HDInsight.

    ![Énumération des utilisateurs de l’interface utilisateur de gestion Ambari pour HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Énumération des groupes du domaine synchronisés à partir d’Active Directory
1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Groupes**. Vous devriez voir tous les groupes synchronisés à partir d’Active Directory sur le cluster HDInsight.

    ![Énumération des groupes de l’interface utilisateur de gestion Ambari pour HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configuration des autorisations des affichages Hive
1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Views** (Affichages).
3. Cliquez sur **HIVE** pour afficher les détails.

    ![Affichages Hive de l’interface utilisateur de gestion Ambari pour HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Cliquez sur le lien **Hive View** (Affichage Hive) pour configurer les affichages Hive.
5. Faites défiler jusqu’à la section **Autorisations**.

    ![Autorisations de configuration des affichages Hive de l’interface utilisateur de gestion Ambari pour HDInsight ESP](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Cliquez sur **Add User** (Ajouter un utilisateur) ou **Add Group** (Ajouter un groupe), puis spécifiez les utilisateurs ou les groupes que peuvent utiliser les affichages Hive.

## <a name="configure-users-for-the-roles"></a>Configuration des utilisateurs pour les rôles
 Pour afficher la liste des rôles et de leurs autorisations, consultez Rôles des clusters HDInsight avec ESP.

1. Ouvrez l’interface utilisateur de gestion Ambari.  Reportez-vous à [Ouverture de l’interface utilisateur de gestion Ambari](#open-the-ambari-management-ui).
2. Dans le menu de gauche, cliquez sur **Rôles**.
3. Cliquez sur **Add User** (Ajouter un utilisateur) ou **Add Group** (Ajouter un groupe) pour affecter des utilisateurs et des groupes aux différents rôles.

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight avec le Pack Sécurité Entreprise, consultez [Configurer des clusters HDInsight avec ESP](apache-domain-joined-configure.md).
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configurer des stratégies Apache Hive pour des clusters HDInsight avec ESP](apache-domain-joined-run-hive.md).
