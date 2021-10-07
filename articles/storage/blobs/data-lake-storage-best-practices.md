---
title: Bonnes pratiques d’utilisation d’Azure Data Lake Storage Gen2 | Microsoft Docs
description: Découvrez les meilleures pratiques pour l’ingestion des données, la sécurité des données et les performances liées à l’utilisation d’Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: b2d631d165b4a8cd585fbe012d9ab9afaa2e6bf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555429"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Bonnes pratiques d’utilisation d’Azure Data Lake Storage Gen2

Cet article présente les meilleures pratiques recommandées en matière de sécurité, de résilience et de structure de répertoire dans un compte de stockage avec Data Lake Storage Gen2. Pour connaître les meilleures pratiques recommandées concernant l’optimisation des performances, consultez [Optimiser Azure Data Lake Storage Gen2 pour les performances](data-lake-storage-performance-tuning-guidance.md).

## <a name="security-considerations"></a>Considérations relatives à la sécurité

### <a name="use-security-groups-instead-of-individual-users"></a>Utiliser des groupes de sécurité plutôt que des utilisateurs individuels

Lorsque vous appliquez des listes de contrôle d’accès (ACL, access-control list), utilisez toujours des groupes de sécurité Azure AD comme principal attribué dans une entrée de liste de contrôle d’accès. Résistez à la possibilité d’attribuer directement des utilisateurs ou des principaux de service individuels. L’utilisation de cette structure vous permettra d’ajouter et de supprimer des utilisateurs ou des principaux de service sans devoir réappliquer les ACL sur la structure de répertoires dans son ensemble. Au lieu de cela, vous pouvez simplement ajouter ou supprimer des utilisateurs et des principaux de service dans le groupe de sécurité Azure AD approprié.

Pour plus d’informations sur l’application de cette meilleure pratique, consultez [Groupes de sécurité](data-lake-storage-access-control-model.md#security-groups).

Pour obtenir des informations générales sur le modèle de contrôle d’accès Data Lake Storage Gen2, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

### <a name="configure-the-azure-storage-firewall-with-azure-service-access"></a>Configurer le pare-feu de Stockage Azure avec un accès au service Azure

Activez le pare-feu de Stockage Azure pour limiter le vecteur d’attaques externes. Pour accéder à votre compte de stockage à partir d’un service tel qu’[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), déployez une instance de ce service sur votre réseau virtuel. Ensuite, vous pouvez configurer le pare-feu pour accorder l’accès au compte de stockage pour ce service.

Pour plus d’informations sur l’application de cette meilleure pratique, consultez [Accorder l’accès aux services Azure approuvés](../common/storage-network-security.md).

Pour obtenir des recommandations générales en matière de sécurité, consultez [Recommandations de sécurité pour Stockage Blob](security-recommendations.md).

## <a name="resiliency-considerations"></a>Remarques relatives à la résilience

Lors de la conception d’un système avec Data Lake Storage Gen2 ou n’importe quel service cloud, prenez en compte vos exigences en matière de disponibilité et la façon de répondre à des interruptions potentielles du service. Un problème peut être localisé dans une instance spécifique ou même dans la région. Être préparé pour ces deux éventualités est important. En fonction de l’accord de niveau de service de votre charge de travail en matière d’objectif de temps de récupération et d’objectif de point de récupération, vous choisirez une stratégie plus ou moins agressive pour la haute disponibilité et la récupération d’urgence.

### <a name="high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence

Data Lake Storage Gen2 gère déjà trois réplications pour se protéger contre des défaillances matérielles localisées. Les options de réplication comme le stockage redondant interzone (ZRS) et le stockage géo-redondant interzone (GZRS), améliorent la disponibilité. Grâce à ces capacités, les charges de travail peuvent réagir à une interruption de service en basculant sur une instance répliquée séparément localement ou dans une nouvelle région.

Pour vous préparer à la défaillance catastrophique d’une région, veillez à répliquer les données dans une autre région à l’aide des options de réplication telles que le stockage géoredondant (GRS) et le stockage géo-redondant avec accès en lecture (RA-GRS). Tenez également compte de vos exigences pour des cas extrêmes, comme l’altération des données, et pour lesquels vous voudrez peut-être créer régulièrement des instantanés sur lesquels vous replier. En fonction de l’importance et de la taille des données, pensez à prendre des instantanés delta à intervalles de 1, 6 et 24 heures, en fonction des tolérances au risque.

Outre le choix d’un modèle de réplication approprié, envisagez des moyens d’aider les clients qui se connectent à basculer automatiquement sur la région secondaire grâce à la surveillance des déclencheurs, à la durée des tentatives infructueuses ou peut-être à l’envoi d’une notification aux administrateurs pour une intervention manuelle. N’oubliez pas qu’il y a un compromis entre basculer et attendre qu’un service soit de nouveau en ligne.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Utiliser Distcp pour déplacer des données entre deux emplacements

Diminutif de Distributed Copy, DistCp est un outil en ligne de commande Linux fourni avec Hadoop et qui permet le déplacement de données distribuées entre deux emplacements. Les deux emplacements peuvent être Data Lake Storage Gen2, Hadoop Distributed File System (HDFS) ou S3. Cet outil utilise des tâches MapReduce sur un cluster Hadoop (par exemple, HDInsight) pour effectuer un scale-out sur tous les nœuds. Distcp est considéré comme l’un des moyens les plus rapides de déplacer du Big Data sans appliances spéciales de compression de réseau. Cet outil fournit aussi une option pour ne mettre à jour que les valeurs delta entre deux emplacements, gère les essais automatiques et la mise à l’échelle dynamique de calcul. Cette approche est incroyablement efficace quand il est question de répliquer des éléments tels que des tables Hive ou Spark qui peuvent disposer de nombreux fichiers volumineux dans un seul répertoire, et que vous ne voulez copier que les données modifiées. Pour ces raisons, Distcp est l’outil le plus recommandé pour la copie de données entre des magasins Big Data.

Les tâches de copie peuvent être déclenchées par des workflows Apache Oozie à l’ide de déclencheurs de fréquence ou de données, ainsi que des tâches Cron Linux. Pour les tâches de réplication intensives, nous vous recommandons d’ajouter un cluster Hadoop HDInsight distinct qui peut être réglé et mis à l’échelle spécialement pour les tâches de copie. Ainsi, les tâches de copie n’interfèrent pas avec les tâches critiques. Si vous exécutez des réplications à une fréquence assez importante, le cluster peut même être arrêté entre chaque tâche. Si vous basculez vers une région secondaire, veillez à ce qu’un autre cluster soit aussi ajouté à cette région pour répliquer de nouvelles données sur le compte principal Data Lake Storage Gen2 quand il sera de nouveau opérationnel. Pour obtenir des exemples d’utilisation de DistCp, consultez [Utiliser DistCp pour copier des données entre des objets blob de Stockage Azure et Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Utiliser Azure Data Factory pour planifier des tâches de copie

Vous pouvez aussi utiliser [Azure Data Factory](../../data-factory/introduction.md) pour planifier des tâches de copie à l’aide d’une activité de copie, et même le configurer pour une exécution selon une fréquence spécifique par le biais de l’Assistant de copie. N’oubliez pas qu’Azure Data Factory dispose d’une limite d’unités de déplacement de données cloud (DMU), et finit par atteindre la limite de débit/calcul pour des charges de travail de données volumineuses. Azure Data Factory n’offre pas non plus de mises à jour des valeurs delta entre les comptes Data Lake Storage Gen2 pour l’instant. Les répertoires comme les tableaux Hive nécessitent donc une copie complète pour être répliqués. Pour plus d’informations sur la copie avec Data Factory, consultez l’[article sur la fabrique de données](../../data-factory/load-azure-data-lake-storage-gen2.md).

## <a name="monitoring-considerations"></a>Surveillance - Éléments à prendre en compte

Data Lake Storage Gen2 fournit des métriques dans le portail Azure sous le compte Data Lake Storage Gen2 et dans Azure Monitor. La disponibilité de Data Lake Storage Gen2 est affichée dans le portail Azure. Pour obtenir la disponibilité la plus à jour d’un compte Data Lake Storage Gen2, vous devez exécuter vos propres tests synthétiques afin de valider la disponibilité. D’autres métriques telles que l’utilisation totale du stockage, les requêtes de lecture/écriture et les entrées/sorties peuvent être exploitées grâce à la supervision des applications, et peuvent également déclencher des alertes quand des seuils (par exemple la latence moyenne ou le nombre d’erreurs par minute) sont dépassés.

## <a name="directory-layout-considerations"></a>Considérations relatives à la disposition du répertoire

Lors de l’ingestion de données, il est important de prévoir la structure des données afin de pouvoir utiliser avec efficacité la sécurité, le partitionnement et le traitement. La plupart des recommandations suivantes sont applicables à toutes les charges de travail de Big Data. Chaque charge de travail a des prérequis différents concernant l’utilisation des données, mais vous trouverez ci-dessous des dispositions communes à prendre en compte lorsque vous utilisez des scénarios d’Internet des objets (IoT) ou de traitement.

### <a name="iot-structure"></a>Structure IoT

Dans des charges de travail IoT peuvent se trouver un grand nombre de données ingérées, couvrant de nombreux produits, appareils, organisations et clients. Il est important de planifier la disposition du répertoire pour des questions d’organisation, de sécurité et de traitement efficace des données pour les consommateurs en aval. Voici un modèle général de disposition à prendre en compte :

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

Par exemple, la télémétrie d’arrivée d’un moteur d’avion dans le Royaume-Uni ressemble à la structure suivante :

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

Dans cet exemple, en plaçant la date à la fin de la structure de répertoire, vous pouvez utiliser des listes de contrôle d’accès pour sécuriser plus facilement les régions et les sujets à des utilisateurs et des groupes spécifiques. Si vous placez la structure de données au début, il serait bien plus difficile de sécuriser ces régions et sujets. Par exemple, si vous souhaitez fournir un accès uniquement aux données provenant du Royaume-Uni ou à certains plans, vous devez appliquer une autorisation distincte pour de nombreux répertoires sous chaque répertoire horaire. Cette structure augmenterait également de façon exponentielle le nombre de répertoires au fil du temps.

### <a name="batch-jobs-structure"></a>Structure de tâches de traitement par lots

Une approche couramment utilisée dans le traitement par lots consiste à placer les données dans un répertoire « in ». Ensuite, une fois les données traitées, les nouvelles données sont placées dans un répertoire « out » pour que les processus en aval puissent les consommer. Cette structure de répertoire est parfois utilisée pour des travaux qui nécessitent un traitement sur des fichiers individuels et qui ne nécessitent pas forcément de traitement parallèle massif sur des jeux de données volumineux. Tout comme la structure IoT recommandée plus haut, une bonne structure de répertoire dispose de répertoires de niveau parent pour des choses telles que la région et les sujets (par exemple l’organisation, le produit ou le producteur). Tenez compte de la date et de l’heure dans la structure pour permettre une meilleure organisation, des recherches filtrées, la sécurité et l’automatisation du traitement. Le niveau granularité de la structure de date est déterminé par l’intervalle de chargement ou de traitement des données, par exemple à l’heure, quotidien, ou mensuel.

Parfois le traitement de fichiers échoue en raison de corruption des données ou de formats inattendus. Dans ces cas, une structure de répertoire peut bénéficier d’un dossier **/bad** dans lequel les fichiers sont déplacés pour une inspection plus approfondie. La tâche de traitement par lots peut aussi gérer le rapport ou la notification de ces fichiers *bad* en vue d’une intervention manuelle. Considérez la structure de modèle suivante :

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

Par exemple, une firme marketing qui reçoit quotidiennement des extraits de données de mises à jour client de la part de leurs clients en Amérique du Nord. Avant et après avoir été traité, il peut ressembler à l’extrait de code suivant :

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

Dans le cas courant de traitement des données par lots directement dans des bases de données, telles que des bases de données Hive ou des bases de données SQL traditionnelles, il n’est pas nécessaire d’avoir un répertoire **/in** ou **/out**, car la sortie se trouve déjà dans un dossier différent pour la table Hive ou la base de données externe. Par exemple, les extraits quotidiens des clients se trouvent dans leurs répertoires respectifs. Ensuite, un service tel qu’[Azure Data Factory](../../data-factory/introduction.md), [Apache Oozie](https://oozie.apache.org/) ou [Apache Airflow](https://airflow.apache.org/) déclencherait un travail Hive ou Spark quotidien pour traiter et écrire les données dans une table Hive.

## <a name="see-also"></a>Voir aussi

- [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
- [Optimiser Azure Data Lake Storage Gen2 pour les performances](data-lake-storage-performance-tuning-guidance.md)
- [Guide de l’autostoppeur vers le lac de données](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
