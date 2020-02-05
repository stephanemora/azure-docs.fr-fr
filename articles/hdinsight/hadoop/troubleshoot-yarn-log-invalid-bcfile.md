---
title: Impossible de lire le journal Apache Yarn dans Azure HDInsight
description: Étapes de détection de problèmes et résolutions possibles pour les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776038"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scénario : Impossible de lire le journal Apache Yarn dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Les journaux Apache Yarn disponibles dans le compte de stockage ne sont pas explicites. L’analyseur de fichiers ne fonctionne pas et génère le message d’erreur suivant :

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Cause :

Le journal Apache Yarn est agrégé au format `IndexFile`, ce qui n’est pas pris en charge par l’analyseur de fichiers.

## <a name="resolution"></a>Résolution

1. Dans un navigateur web, accédez à `https://CLUSTERNAME.azurehdinsight.net`, où `CLUSTERNAME` est le nom de votre cluster.

1. À partir de l’interface utilisateur Ambari, accédez à **YARN** > **Configurations** > **Avancé** > **yarn-site avancé**.

1. Pour le stockage WASB : La valeur par défaut pour `yarn.log-aggregation.file-formats` est `IndexedFormat,TFile`. Remplacez la valeur par `TFile`.

1. Pour le stockage ADLS : La valeur par défaut pour `yarn.nodemanager.log-aggregation.compression-type` est `gz`. Remplacez la valeur par `none`.

1. Enregistrez la modification, puis redémarrez tous les services affectés.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
