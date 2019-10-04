---
title: Azure HDInsight - Questions fréquentes (FAQ)
description: Présentation d’Azure HDInsight
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213869"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight : Questions fréquentes (FAQ)

Cet article fournit des réponses à certaines questions fréquemment posées sur l’exécution d’[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Création et suppression des clusters HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Comment provisionner un cluster HDInsight ?

Pour connaître les types de clusters HDInsight disponibles et les méthodes de provisionnement, consultez [Configurer des clusters dans HDInsight avec Apache Hadoop, Apache Spark, Apache Kafka, etc](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Comment supprimer un cluster HDInsight existant ?

Pour plus d’informations sur la suppression d’un cluster non utilisé, consultez [Suppression d’un cluster HDInsight à l’aide de votre navigateur, PowerShell ou l’interface de ligne de commande Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster). Il est recommandé d’attendre au moins 30 à 60 minutes entre la création et la suppression d’un cluster. Sinon, l’opération peut échouer et retourner le message d’erreur suivant :

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Comment sélectionner un nombre de cœurs ou de nœuds adapté à ma charge de travail ?

Le nombre de cœurs nécessaire, ainsi que d’autres options de configuration, dépendent de différents facteurs.

Pour plus d’informations, consultez [Planification de la capacité pour les clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Que faire lorsque le provisionnement du cluster échoue en raison d’un problème de capacité ?

Cette section aborde les erreurs courantes liées à la capacité, ainsi que les techniques d’atténuation adaptées.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Error: Le déploiement va entraîner le dépassement du quota de « 800 »

Azure a une limite de quota de 800 déploiements par groupe de ressources. Des quotas sont appliqués pour chaque groupe de ressources, abonnement, compte ou autre étendue. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle qui comprend plus de cœurs que la quantité autorisée, vous recevez un message d’erreur indiquant que le quota a été dépassé.

Pour résoudre ce problème, supprimez les déploiements qui ne sont plus nécessaires à l’aide du portail Azure, de CLI ou de PowerShell.

Pour plus d’informations, consultez [Résoudre les erreurs liées aux quotas de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Error: Le nombre maximal de nœuds dépasse le nombre de cœurs disponibles dans cette région

Votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une ressource qui comprend plus de cœurs que la quantité autorisée, vous recevez un message d’erreur indiquant que le quota a été dépassé.

Pour demander une augmentation de quota, procédez comme suit :

1. Accédez au portail Azure, puis sélectionnez Aide + Support.
2. Sélectionnez Nouvelle demande de support.
3. Dans la page Nouvelle demande de support, sélectionnez les options suivantes sous l’onglet De base :

   * Type de problème : Limites du service et de l’abonnement (quotas)
   * Abonnement : l’abonnement que vous souhaitez modifier
   * Type de quota : HDInsight

Pour plus d’informations, consultez [Créer un ticket de support pour augmenter le nombre de cœurs](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quels sont les différents types de nœuds d’un cluster HDInsight ?

Les clusters Azure HDInsight présentent différents types de machines virtuelles ou nœuds. Chaque type de nœud joue un rôle dans le fonctionnement du système.

Pour plus d’informations, consultez [Types de ressources des clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Composants individuels

### <a name="can-i-install-additional-components-on-my-cluster"></a>Puis-je installer des composants supplémentaires dans un cluster ?

Oui, vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de l’une des méthodes suivantes :

* Utilisation de scripts pendant ou après la création. Ces scripts sont appelés à l’aide d’une [action de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Cette option de configuration peut être utilisée dans le portail Azure, dans des applets de commande HDInsight Windows PowerShell ou dans le SDK HDInsight .NET. 
* Utilisation de sudo ou d’autres méthodes après le provisionnement du cluster.
* Utilisation de la [plateforme d’applications HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) pour installer des applications de l’écosystème.

Toutefois, les équipes du support technique Microsoft ne peuvent proposer leur aide que dans les cas suivants :

* Problèmes ou erreurs qui se produisent lors du chargement du script. Les erreurs survenant lors de l’exécution de scripts personnalisés n’entrent pas dans le cadre d’un ticket de support. 
* Des applications supplémentaires qui font partie du processus 

Pour obtenir la liste des composants pris en charge, consultez [Quels sont les composants et versions d’Apache Hadoop disponibles avec HDInsight ?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

La prise en charge des composants individuels peut également varier selon le type de cluster. Par exemple, Spark n’est pas pris en charge sur un cluster Kafka, et vice versa.

Pour les applications ou les services extérieurs au processus de création d’un cluster, contactez le fournisseur/fournisseur de services correspondant pour obtenir de l’aide. Vous pouvez également consulter les sites de la communauté pour obtenir de l’aide concernant ces actions. De nombreux sites de la communauté sont disponibles, par exemple, le [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight) ou le site [Stack Overflow](https://stackoverflow.com/questions/tagged/hdinsight). Par ailleurs, les projets Apache ont des sites de projet sur le [site web Apache](https://apache.org/). [Hadoop](https://hadoop.apache.org/) en est un exemple.

 Pour voir d’autres questions relatives au support Azure, consultez les [Questions fréquentes (FAQ) sur le support Azure](https://azure.microsoft.com/en-us/support/faq/).

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Est-il possible de mettre à niveau chacun des composants qui sont préinstallés sur le cluster ?

Si vous mettez à niveau des composants intégrés ou des applications qui sont préinstallées sur votre cluster, la configuration obtenue ne sera pas prise en charge par Microsoft. Ces configurations système n’ont pas été testées par Microsoft. Essayez d’utiliser une autre version du cluster HDInsight qui a peut-être déjà le composant mis à niveau préinstallé.

Par exemple, la mise à niveau de Hive en tant que composant n’est pas prise en charge. HDInsight est un service managé, et de nombreux services ont été testés et intégrés au serveur Ambari. Le fait de mettre à niveau Hive séparément entraîne la modification des binaires indexés des autres composants, ce qui entraîne des problèmes d’intégration des composants dans votre cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark et Kafka peuvent-ils s’exécuter sur le même cluster HDInsight ?

Non, il n’est pas possible d’exécuter Apache Kafka et Apache Spark sur un même cluster HDInsight. Les utilisateurs doivent créer un cluster pour chacun afin d’éviter les conflits de ressources.

### <a name="how-do-i-change-timezone-in-ambari"></a>Comment modifier le fuseau horaire dans Ambari ?

1. Ouvrez l’interface utilisateur web d’Ambari à l’adresse https://CLUSTERNAME.azurehdinsight.net, où CLUSTERNAME correspond au nom de votre cluster.
2. En haut à droite, sélectionnez Administrateur | Paramètres. 

   ![Paramètres Ambari](media/hdinsight-faq/ambari-settings.png)

3. Dans la fenêtre Paramètres utilisateur, sélectionnez le nouveau fuseau horaire dans la liste déroulante Fuseau horaire, puis cliquez sur Enregistrer.

   ![Paramètres utilisateur Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Comment effectuer la migration d’un metastore existant vers Azure SQL Server ? 

Pour effectuer la migration de SQL Server vers Azure SQL Server, consultez [Tutoriel : Migrer SQL Server vers une base de données unique ou mise en pool dans Azure SQL Database hors connexion à l’aide de DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Le metastore Hive est-il supprimé en même temps que le cluster ?

Cela dépend du type de metastore pour lequel votre cluster est configuré.

Pour un metastore par défaut : Le metastore par défaut fait partie du cycle de vie du cluster. Lorsque vous supprimez un cluster, le metastore et les métadonnées correspondants sont également supprimés.

Pour un metastore personnalisé : Le cycle de vie du metastore n’est pas lié au cycle de vie d’un cluster. Par conséquent, vous pouvez créer et supprimer des clusters sans perdre de métadonnées. Les métadonnées telles que vos schémas Hive sont conservées, même après avoir supprimé et recréé le cluster HDInsight.

Pour plus d’informations, consultez [Utiliser des magasins de métadonnées externes dans Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>La migration d’un metastore Hive entraîne-t-elle la migration des stratégies par défaut de la base de données Ranger ?

Non, la définition de stratégie se trouve dans la base de données Ranger. Par conséquent, la migration de la base de données Ranger n’entraîne pas la migration de la stratégie.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Est-il possible d’effectuer la migration d’un metastore Hive entre un cluster Pack Sécurité Entreprise (ESP) et un cluster non ESP, ou inversement ?

Oui, vous pouvez procéder à la migration d’un metastore Hive entre un cluster ESP et un cluster non ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Comment estimer la taille d’une base de données metastore Hive ?

Un metastore Hive est utilisé pour stocker les métadonnées des sources de données utilisées par le serveur Hive. Par conséquent, les exigences de taille sont affectées par le nombre de sources de données que vous serez amené à utiliser pour Hive, ainsi que par la complexité des sources de données. La taille ne peut donc pas être estimée à l’avance. Comme indiqué dans [Bonnes pratiques concernant Hive metastore](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices), le fait de démarrer au niveau S2 fournit 50 DTU et 250 Go de stockage. Si vous rencontrez un goulot d’étranglement, vous pouvez effectuer un scale-up de la base de données.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>En dehors d’Azure SQL Database, existe-t-il d’autres bases de données qui peuvent être utilisées en tant que metastore externe ?

Non, Microsoft permet seulement d’utiliser Azure SQL Database comme un metastore personnalisé externe.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Peut-on partager un metastore entre plusieurs clusters ?

Oui, vous pouvez partager un metastore personnalisé entre plusieurs clusters, à condition que ceux-ci utilisent la même version de HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Connectivité et réseaux virtuels  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quelles sont les conséquences du blocage des ports 22 et 23 sur mon réseau ?

Si vous bloquez les ports 22 et 23, vous ne disposerez pas d’un accès SSH au cluster. Ces ports ne sont pas utilisés par le service HDInsight.

Pour plus d’informations, consultez les documents suivants :

* [Contrôle du trafic réseau](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Sécuriser le trafic entrant vers les clusters HDInsight dans un réseau virtuel avec point de terminaison privé](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
* [Adresses IP de gestion HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Puis-je déployer une machine virtuelle supplémentaire au sein d’un même sous-réseau en tant que cluster HDInsight ?

Oui, vous pouvez déployer une machine virtuelle supplémentaire au sein d’un même sous-réseau en tant que cluster HDInsight. Les configurations suivantes sont possibles :

* Nœuds de périphérie : Vous pouvez ajouter un autre nœud de périphérie au cluster, comme décrit dans [Utiliser des nœuds de périphérie vides sur des clusters Apache Hadoop dans HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node). 
* Nœuds autonomes :  Vous pouvez ajouter une machine virtuelle autonome au même sous-réseau et accéder au cluster à partir de cette machine virtuelle à l’aide du point de terminaison privé `https://<CLUSTERNAME>-int.azurehdinsight.net`. Pour plus d’informations, consultez [Contrôle du trafic réseau](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic).

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Puis-je ajouter un cluster HDInsight existant à un autre réseau virtuel ?

Non, vous ne pouvez pas ajouter un cluster HDInsight existant à un autre réseau virtuel. Le réseau virtuel doit être spécifié au moment du provisionnement. Si aucun réseau virtuel n’est spécifié lors du provisionnement, le déploiement crée un réseau interne qui n’est pas accessible de l’extérieur. Pour plus d’informations, consultez [Ajouter HDInsight à un réseau virtuel existant](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet).

## <a name="security-and-certificates"></a>Sécurité et certificats

Quelles sont les recommandations de Microsoft concernant la protection contre les programmes malveillants pour les clusters Azure HD Insight ?

Pour plus d’informations sur la protection contre les programmes malveillants, consultez [Microsoft Antimalware pour les services cloud Azure et les machines virtuelles](https://docs.microsoft.com/azure/security/fundamentals/antimalware).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Comment créer un keytab pour un cluster ESP HDInsight ?

Vous pouvez créer un keytab Kerberos pour le nom d’utilisateur de votre domaine. Vous pourrez utiliser ce keytab ultérieurement pour vous authentifier auprès de clusters distants joints à un domaine sans entrer de mot de passe. Notez les lettres majuscules dans le nom de domaine :

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Puis-je utiliser un locataire Azure Active Directory existant pour créer un cluster ESP HDInsight ?

L’activation d’Azure Active Directory Domain Services (AAD-DS) est un prérequis à la création d’un cluster ESP HDInsight. Hadoop open source s’appuie sur Kerberos pour l’authentification (et non sur OAuth).

Pour joindre des machines virtuelles Azure à un domaine, vous devez disposer d’un contrôleur de domaine. AAD-DS est le contrôleur de domaine managé. Il est considéré comme une extension d’AAD fournissant tous les éléments Kerberos qui sont nécessaires pour créer un cluster Hadoop sécurisé de façon managée. En tant que service managé, HDInsight s’intègre à AAD-DS pour fournir une sécurité de bout en bout.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Puis-je utiliser un certificat auto-signé dans une configuration LDAP sécurisée d’AAD-DS et provisionner un cluster ESP ?

Même s’il est recommandé d’utiliser un certificat émis par une autorité de certification, l’utilisation d’un certificat auto-signé est également prise en charge pour l’ESP.

Pour plus d'informations, consultez les pages suivantes :

* [Activer Azure AD-DS](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds)
* [Tutoriel : Configurer le protocole LDAP sécurisé pour un domaine managé Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Comment tirer (pull) les activités de connexion affichées dans Ranger ?

Pour les besoins d’audit, Microsoft recommande d’activer les journaux Azure Monitor, comme décrit dans [Utiliser les journaux Azure Monitor pour superviser les clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Puis-je désactiver Clamscan sur un cluster ?

Clamscan est le logiciel antivirus qui s’exécute sur le cluster HDInsight. Il est utilisé par la sécurité Azure (azsecd) pour protéger vos clusters contre les attaques de virus. Microsoft recommande vivement aux utilisateurs de ne pas apporter de modifications à la configuration Clamscan par défaut.

Ce processus n’interfère pas avec les cycles des autres processus et ne les supprime pas non plus. Il passe toujours à un autre processus. Les pics d’utilisation du processeur de Clamscan ne doivent être visibles que lorsque le système est inactif.  

Dans les scénarios où vous devez contrôler la planification, vous pouvez utiliser les étapes suivantes :

1. Désactivez l’exécution automatique à l’aide de la commande suivante :

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. Créez un travail Cron qui exécute cette commande en tant que racine :

   ``/usr/local/bin/azsecd manual -s clamav``

Pour plus d’informations sur la configuration et l’exécution d’un travail Cron, consultez [How do I set up a Cron job ?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job).

## <a name="storage"></a>Stockage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Puis-je ajouter Azure Data Lake Storage Gen2 à un cluster HDInsight existant en tant que compte de stockage supplémentaire ?

Non. Vous ne pouvez pas ajouter Azure Data Lake Storage Gen2 à un cluster HDInsight existant qui utilise le stockage Blob comme stockage primaire. Pour plus d’informations, consultez [Comparer les options de stockage à utiliser avec les clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options). 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Comment trouver le principal de service qui est lié à un compte de stockage Data Lake ?

Vous pouvez vérifier ces paramètres dans l’accès Data Lake Storage Gen1, sous les propriétés de votre cluster, sur le portail. Pour plus d’informations, consultez [Vérifier la configuration du cluster](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up).
 
Comment calculer l’utilisation des comptes de stockage et des conteneurs d’objets blob pour mes clusters HDInsight ?
Vous pouvez utiliser l’une des procédures suivantes :

* [Utiliser PowerShell](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* Vous pouvez également trouver la taille du dossier /user/hive/.Trash/ sur les clusters HDI à l’aide de la commande suivante :

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Comment configurer l’audit de mon compte de stockage Blob ?

Pour auditer les comptes de stockage Blob, vous devez configurer la supervision du compte de stockage Blob à l’aide de la procédure décrite dans [Surveillance d’un compte de stockage dans le portail Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account). Un journal HDFS-audit fournit uniquement des informations d’audit pour le système de fichiers HDFS local (hdfs://mycluster).  Il n’inclut pas les opérations qui sont effectuées sur le stockage distant.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Comment transférer des fichiers entre un conteneur d’objets blob et un nœud principal HDInsight ?

Vous pouvez transférer des fichiers entre un conteneur d’objets blob et un nœud principal HDInsight en exécutant un script shell sur votre nœud principal, comme dans l’exemple suivant :

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Le fichier *filenames.txt* contient le chemin absolu des fichiers du conteneurs d’objets blob.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Existe-t-il des plug-ins Ranger pour le stockage ?

Il n’existe pas de plug-in Ranger pour le stockage Blob, Azure Data Lake Storage (ADLS) Gen1 ou Azure Data Lake Storage Gen2. Pour les clusters ESP, utilisez ADLS en tant que bonne pratique et définissez des autorisations affinées manuellement au niveau du système de fichiers à l’aide des outils HDFS. En outre, les clusters ESP effectuent une partie du contrôle d’accès du système de fichiers en utilisant AAD au niveau du cluster lorsque vous utilisez ADLS. 

Vous devez pouvoir utiliser l’explorateur Stockage Azure afin d’affecter des stratégies d’accès aux données aux groupes de sécurité dont font partie vos utilisateurs, en utilisant les procédures décrites dans les articles suivants :

* [Comment définir des autorisations pour que les utilisateurs Azure AD puissent interroger des données dans Data Lake Storage Gen2 avec Hive ou d’autres services ?](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Définir des autorisations au niveau de fichiers et de répertoires à l'aide de l'Explorateur Stockage Azure avec Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Puis-je augmenter le stockage HDFS sur un cluster sans augmenter la taille du disque des nœuds worker ?

Non, vous ne pouvez pas augmenter la taille du disque d’un nœud worker. La seule façon d’augmenter la taille du disque est de supprimer le cluster et de le recréer sur des machines virtuelles de travail plus volumineuses.

Microsoft ne recommande pas l’utilisation de HDFS pour stocker des données dans HDInsight, car les données seront supprimées lorsque vous supprimerez le cluster. Nous vous recommandons plutôt de stocker vos données dans Azure. Le scale-up du cluster peut également augmenter la capacité de votre cluster HDInsight.

## <a name="edge-nodes"></a>Nœuds de périmètre

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Puis-je ajouter un nœud de périphérie après la création du cluster ?

Oui, vous pouvez ajouter un nœud de périphérie vide à un cluster HDInsight existant ou à un nouveau cluster.

Pour plus d'informations, consultez [Utiliser les nœuds de périphérie vides sur les clusters Apache Hadoop dans HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node).

### <a name="how-can-i-connect-to-an-edge-node"></a>Comment me connecter à un nœud de périphérie ?

Après avoir créé un nœud de périphérie, vous pouvez vous y connecter à l’aide du SSH sur le port 22. Le nom du nœud de périphérie se trouve sur le portail du cluster (il s’agit généralement des noms se terminant par « -ed »).

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Pourquoi les scripts persistants ne s’exécutent-ils pas automatiquement sur les nœuds de périphérie nouvellement créés ?

Les scripts persistants servent à personnaliser les nouveaux nœuds Worker qui sont ajoutés au cluster lors d’opérations de mise à l’échelle. Ils ne sont pas utilisés avec les nœuds de périphérie.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quels sont les appels d’API REST qui permettent de tirer (pull) l’affichage des requêtes Tez à partir du cluster ?

Vous pouvez utiliser les points de terminaison REST suivants pour tirer (pull) les informations nécessaires, dont la réponse sera au format JSON. Les en-têtes d’authentification de base peuvent être utilisés pour exécuter ces requêtes.

* Affichage « Requête Tez » – https://`<cluster name>`.azurehdinsight.net/ws/v1/timeline/ID_REQUÊTE_HIVE/

* Affichage « DAG Tez » – https://`<cluster name>`.azurehdinsight.net/ws/v1/timeline/ID_DAG_TEZ/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Comment récupérer les détails de configuration du cluster HDI à l’aide d’un utilisateur Azure Active Directory ?

Pour négocier des jetons d’authentification appropriés avec votre utilisateur AAD, traversez la passerelle en utilisant le format suivant :

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/versions_stack/1/versions_référentiel/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Comment utiliser l’API RESTful Ambari pour superviser les performances de YARN ?

Si la commande curl est appelée dans le même réseau virtuel (appairé ou non), utilisez la commande suivante :

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
Si la commande est appelée de l’extérieur du réseau virtuel ou à partir d’un réseau virtuel non appairé, le format de la commande est le suivant :
 
* Pour un cluster non ESP : ``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* Pour un cluster ESP : ``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> Curl vous invite à entrer un mot de passe. Vous devez entrer un mot de passe valide pour le nom d’utilisateur de connexion du cluster.

## <a name="billing"></a>Facturation

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Combien coûte le déploiement d’un cluster HDInsight ?

Pour plus d’informations sur les tarifs et les questions fréquentes relatives à la facturation, consultez la page [Tarifs Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

### <a name="how-do-i-cancel-my-subscription"></a>Comment annuler mon abonnement ?

Pour plus d’informations sur l’annulation des abonnements, consultez [Annulation de votre abonnement Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Pour les abonnements avec paiement à l’utilisation, que se passe-t-il après l’annulation de mon abonnement ?

Pour plus d’informations sur votre abonnement après son annulation, consultez [What happens after I cancel my subscription?](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription).

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Pourquoi la version 1.2.1000 de Hive s’affiche-t-elle au lieu de la version 2.1 dans l’interface utilisateur Ambari, alors que j’exécute un cluster HDInsight 3.6 ?

Même si seule la version 1.2 s’affiche dans l’interface utilisateur Ambari, HDInsight 3.6 contient bien Hive 1.2 et Hive 2.1.

## <a name="other-faq"></a>Autres questions fréquentes

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Quelles sont les fonctionnalités de traitement du flux en temps réel qui sont proposées par HDInsight ?

Pour plus d’informations sur les fonctionnalités d’intégration du traitement de flux dans Azure HDInsight, consultez [Sélectionner une technologie de traitement de flux dans Azure](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existe-t-il un moyen de terminer dynamiquement le nœud principal du cluster lorsque le cluster est inactif pendant une période donnée ?

Non, vous ne pouvez pas arrêter dynamiquement le nœud principal du cluster. Dans ce cas, vous pouvez utiliser Azure Data Factory.
