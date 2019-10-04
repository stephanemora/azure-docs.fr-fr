---
title: Impossible de se connecter à Zeppelin dans Azure HDInsight
description: Impossible de se connecter à Zeppelin dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: d4bb9e090b238eacec77f4c19bbf9afb3e09a912
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091062"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scénario : Impossible de se connecter à Apache Zeppelin dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Impossible de se connecter à Apache Zeppelin après avoir modifié le mot de passe ADDS dans Active Directory.

## <a name="cause"></a>Cause :

L’utilisateur a mentionné que le `activeDirectoryRealm.systemUsername` du fichier `shiro_ini` a modifié le mot de passe Active Directory.

## <a name="resolution"></a>Résolution :

1. Vérifiez que la modification du mot de passe est la cause principale en incluant `activeDirectoryRealm.systemPassword = <new password>` dans la configuration `shiro_ini` Zeppelin dans Ambari. Supprimez le paramètre `activeDirectoryRealm.hadoopSecurityCredentialPath`. Vous trouverez ci-dessous l’emplacement de `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Si les utilisateurs peuvent maintenant se connecter à Zeppelin après l’étape 1, créez un fichier `jceks` avec le nouveau mot de passe et remplacez le `activeDirectoryRealm.hadoopSecurityCredentialPath` par le nouveau fichier.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
