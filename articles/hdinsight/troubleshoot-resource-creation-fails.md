---
title: Résoudre les problèmes de création de ressources dans Azure HDInsight
description: Cet article aborde les erreurs courantes liées à la capacité, ainsi que les techniques d’atténuation adaptées.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: a47781bcdf60d863d6dcf77d70bc0ee496a51388
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538797"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Résoudre les problèmes de création de ressources dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erreur : Le déploiement va entraîner le dépassement du quota de « 800 »

Azure a une limite de quota de 800 déploiements par groupe de ressources. Des quotas sont appliqués pour chaque groupe de ressources, abonnement, compte ou autre étendue. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que le nombre autorisé, le message d’erreur indique que le quota a été dépassé.

Pour résoudre ce problème, supprimez les déploiements qui ne sont plus nécessaires à l’aide du portail Azure, de CLI ou de PowerShell.

Pour plus d’informations, consultez [Résoudre les erreurs liées aux quotas de ressources](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erreur : Le nombre maximal de nœuds dépasse le nombre de cœurs disponibles dans cette région

Votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une ressource avec plus de cœurs que le nombre autorisé, le message d’erreur indique que le quota a été dépassé.

Pour demander une augmentation de quota, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com), puis sélectionnez **Aide + Support** .

1. Sélectionnez **Nouvelle demande de support** .

1. Sous l’onglet **Informations de base** dans la page **Nouvelle demande de support** , fournissez les informations suivantes :

   * **Type de problème :** Sélectionnez **Limites du service et de l’abonnement (quotas)** .
   * **Abonnement :** Sélectionnez l’abonnement que vous souhaitez modifier.
   * **Type de quota**  : Sélectionnez **HDInsight** .

Pour plus d’informations, consultez [Créer un ticket de support pour augmenter le nombre de cœurs](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support** . Pour plus d’informations, consultez [Création d’une demande de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).