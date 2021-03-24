---
title: Options de sécurité pour Hive dans Azure HDInsight
description: Options de sécurité pour Hive dans les clusters standard et ESP.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869716"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Options de sécurité pour Hive dans Azure HDInsight

Ce document décrit les options de sécurité recommandées pour Hive dans HDInsight. Ces options peuvent être configurées par le biais d’Ambari.

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="`Security Options for Hive`" border="true":::

## <a name="hiveserver2-authentication"></a>Authentification HiveServer2

Pour les clusters standard, le paramètre recommandé pour l’authentification HiveServer2 est la valeur par défaut, qui est None. Pour activer l’authentification, nous vous recommandons de procéder à une mise à niveau vers un cluster [ESP](../domain-joined/hdinsight-security-overview.md) (Pack Sécurité Entreprise). 

Pour les clusters ESP, l’authentification [Kerberos](https://web.mit.edu/Kerberos/) est activée par défaut. Les modules d’authentification enfichables (PAM) et les schémas d’authentification personnalisés ne sont pas pris en charge.

## <a name="hiveserver2-authorization"></a>Autorisation HiveServer2

Pour les clusters standard, le paramètre par défaut est None. [SqlStdAuth (autorisation basée sur les normes SQL)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) peut être activée. L’autorisation via [Apache Ranger](https://ranger.apache.org/) n’est pas prise en charge pour les clusters standard. Nous vous recommandons de procéder à une mise à niveau vers un cluster ESP pour l’autorisation Ranger. 

Pour les clusters ESP, l’autorisation via Ranger est activée par défaut. 


## <a name="ssl-encryption-for-hiveserver2"></a>Chiffrement SSL pour HiveServer2

L’activation de Hiveserver2 SSL n’est pas recommandée pour les clusters standard ou ESP. SSL est plutôt activé sur la passerelle. [Le chiffrement en transit](../domain-joined/encryption-in-transit.md) peut être activé pour chiffrer les communications entre les nœuds de cluster à l’aide du protocole [IPSec (Internet Protocol Security)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de l’authentification HiveServer2](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Vue d’ensemble de l’autorisation HiveServer2](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Activation de l’autorisation Hive basée sur les normes SQL](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger avec Hive](../domain-joined/apache-domain-joined-run-hive.md)
