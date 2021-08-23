---
title: La vue Apache Ambari Tez se charge lentement dans Azure HDInsight
description: La vue Apache Ambari Tez peut se charger lentement ou ne pas se charger du tout dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0078a01e691844e6ad2e3b4133fa4d0913b240db
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290572"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scénario : La vue Apache Ambari Tez se charge lentement dans Azure HDInsight

Cet article décrit la procédure à suivre pour résoudre les problèmes rencontrés lors de l’utilisation de composants Interactive Query dans des clusters Azure HDInsight.

## <a name="issue"></a>Problème

La vue Apache Ambari Tez peut se charger lentement ou ne pas se charger du tout. Lors du chargement de la vue Ambari Tez, il se peut que les processus sur nœuds principaux cessent de répondre.

## <a name="cause"></a>Cause

L’accès aux API Yarn ATS peut parfois avoir des performances médiocres sur les clusters créés avant octobre 2017 lorsque le cluster est exécuté sur un grand nombre de travaux Hive.

## <a name="resolution"></a>Résolution

Il s’agit d’un problème qui a été corrigé en octobre 2017. Si vous recréez votre clustervous ne rencontrerez plus ce problème.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]