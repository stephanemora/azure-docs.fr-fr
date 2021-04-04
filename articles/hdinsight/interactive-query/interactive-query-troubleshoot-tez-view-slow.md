---
title: La vue Apache Ambari Tez se charge lentement dans Azure HDInsight
description: La vue Apache Ambari Tez peut se charger lentement ou ne pas se charger du tout dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930761"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]