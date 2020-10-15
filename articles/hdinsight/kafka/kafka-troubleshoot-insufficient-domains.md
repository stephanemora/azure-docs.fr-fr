---
title: Nombre insuffisant de domaines d’erreur dans la région dans Azure HDInsight
description: Échec de la création du cluster en raison d’un nombre insuffisant de domaines d’erreur dans la région dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8be7e05ac85ce0b1b10edf18d3885a07e016b9ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895023"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>Scénario : Échec de la création du cluster en raison `not sufficient fault domains in region` dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Recevez un message d’erreur semblable à `not sufficient fault domains in region` lors de la tentative de création d’un cluster Apache Kafka.

## <a name="cause"></a>Cause

Un domaine d’erreur est un regroupement logique de matériel sous-jacent dans un datacenter Azure. Chaque domaine d’erreur partage une source d’alimentation et un commutateur réseau communs. Les machines virtuelles et les disques managés mettant en œuvre les nœuds au sein d’un cluster HDInsight sont répartis dans ces domaines d’erreur. Cette architecture limite l’impact potentiel des défaillances de matériel physique.

Chaque région Azure possède un certain nombre de domaines d’erreur. Pour obtenir la liste des domaines et le nombre de domaines d’erreur qu’ils contiennent, consultez la documentation sur les [Groupes à haute disponibilité](../../virtual-machines/windows/manage-availability.md).

Dans HDInsight, les clusters Kafka doivent être provisionnés dans une région comportant au moins trois domaines d’erreur.

## <a name="resolution"></a>Résolution

Si la région pour laquelle vous souhaitez créer le cluster ne dispose pas d’un nombre suffisant de domaines d’erreur, contactez l’équipe produit pour permettre le provisionnement du cluster, même s’il n’y a pas trois domaines d’erreur.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
