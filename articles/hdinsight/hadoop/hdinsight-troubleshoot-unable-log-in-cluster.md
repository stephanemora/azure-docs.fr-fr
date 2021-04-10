---
title: Impossible de se connecter au cluster Azure HDInsight
description: Résoudre les problèmes liés à l’impossibilité de se connecter à un cluster Apache Hadoop dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944306"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scénario : impossible de se connecter au cluster Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Impossible de se connecter au cluster Azure HDInsight.

## <a name="cause"></a>Cause

Les raisons peuvent varier. N’oubliez pas d’utiliser vos informations d’identification « connexion cluster » ou « HTTP » lorsque vous vous connectez aux tableaux de bord des clusters ou des applications. Lorsque vous vous connectez à distance, utilisez vos informations d’identification SSH (Secure Shell) ou du Bureau à distance.

## <a name="resolution"></a>Résolution

Pour résoudre les problèmes courants, essayez une ou plusieurs des étapes suivantes.

* Essayez d’ouvrir le tableau de bord du cluster dans un nouvel onglet du navigateur, en mode de confidentialité.

* Si vous avez oublié vos informations d’identification SSH, [réinitialisez-les dans l’interface utilisateur d’Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]