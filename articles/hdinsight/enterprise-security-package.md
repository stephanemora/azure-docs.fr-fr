---
title: Pack Sécurité Entreprise pour Azure HDInsight
description: Découvrez les composants et versions du Pack Sécurité Entreprise dans Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 07210350826001bd3e0be6e04be211c9de43695a
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997870"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Pack Sécurité Entreprise pour Azure HDInsight

Le package de sécurité d’entreprise est un package facultatif que vous pouvez ajouter à votre cluster HDInsight dans le cadre du flux de travail de création du cluster. Le package de sécurité d’entreprise prend en charge les aspects suivants :

* Intégration à Active Directory pour l’authentification.

    Avant, vous pouviez créer des clusters HDInsight avec un utilisateur administrateur local et un utilisateur SSH local. L’utilisateur administrateur local pouvait accéder à tous les fichiers, dossiers, tables et colonnes.  Avec le Pack Sécurité Entreprise, vous activez le contrôle d’accès en fonction du rôle en intégrant HDInsight à votre Active Directory, qui inclut Active Directory en local, Azure Active Directory Domain Services ou Active Directory sur une machine virtuelle IaaS. L’administrateur de domaine sur le cluster peut autoriser les utilisateurs à utiliser leurs propres nom d’utilisateur et mot de passe d’entreprise (domaine).

    Pour plus d'informations, consultez les pages suivantes :

    * [Introduction aux fonctions de sécurité Apache Hadoop sur la base de clusters HDInsight joints à un domaine](./domain-joined/hdinsight-security-overview.md)

    * [Planifier des clusters Apache Hadoop Azure joints à un domaine dans HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Configurer un environnement de bac à sable joint à un domaine](./domain-joined/apache-domain-joined-configure.md)

    * [Configurer les clusters HDInsight joints à un domaine avec Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorisation pour les données

  * Intégration à Apache Ranger pour l’autorisation sur les files d’attente Yarn Hive et Spark SQL.
  * Vous pouvez définir le contrôle d’accès sur les fichiers et dossiers.

    Pour plus d’informations, consultez [Configurer les stratégies Apache Hive dans HDInsight joint à un domaine](./domain-joined/apache-domain-joined-run-hive.md)

* Affichage des journaux d’audit pour surveiller les accès et les stratégies configurées.

## <a name="supported-cluster-types"></a>Types de cluster pris en charge

Seuls les types de cluster suivants prennent en charge le package de sécurité d’entreprise :

* Hadoop (HDInsight 3.6 uniquement)
* Spark
* Kafka
* hbase
* Interactive Query

## <a name="support-for-azure-data-lake-storage"></a>Prise en charge d’Azure Data Lake Storage

Le Pack Sécurité Entreprise prend en charge l’utilisation d’Azure Data Lake Storage comme stockage principal et stockage complémentaire.

## <a name="pricing-and-service-level-agreement-sla"></a>Tarification et contrat SLA

Pour plus d’informations sur les tarifs et le contrat SLA du package de sécurité d’entreprise, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Étapes suivantes

* [Création de clusters pour Apache Hadoop, Spark, etc. dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Travailler à partir d’un PC Windows dans Apache Hadoop sur HDInsight](hdinsight-hadoop-windows-tools.md)
* [Notes de publication Hortonworks associées aux versions d’Azure HDInsight](./hortonworks-release-notes.md)
* [Composants Apache sur HDInsight](./hdinsight-component-versioning.md)