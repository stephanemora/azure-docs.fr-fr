---
title: Impossible de créer un notebook Jupyter dans Azure HDInsight
description: Décrit les étapes de détection de problèmes et résolutions possibles pour les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 61e7cd8d37108b8f4eea88c4f6b6b2a8cdbfd605
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186800"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Impossible de créer un notebook Jupyter dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Lors du démarrage d’un notebook Jupyter, vous recevez un message d’erreur contenant ce qui suit :

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Cause :

Non-concordance des versions.

## <a name="resolution"></a>Résolution

1. Utilisez la [commande ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) pour vous connecter à votre cluster. Modifiez la commande ci-dessous en remplaçant CLUSTERNAME par le nom de votre cluster, puis entrez la commande :

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ouvrez `_version.py` en exécutant la commande suivante :

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Remplacez **5** par **4** afin que la ligne modifiée apparaisse comme suit :

    ```python
    version_info = (4, 0, 3)
    ```

    Enregistrez les modifications en entrant **Ctrl+X**, **Y**, **Entrée**.

1. Dans un navigateur web, accédez à `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`, où `CLUSTERNAME` est le nom de votre cluster.

1. Redémarrez le service Jupyter.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
