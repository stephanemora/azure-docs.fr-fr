---
title: Erreur InvalidNetworkSecurityGroupSecurityRules – Azure HDInsight
description: La création de cluster échoue en générant le code d’erreur InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894144"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Scénario : InvalidNetworkSecurityGroupSecurityRules – Échec de la création d’un cluster dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous recevez le code d’erreur `InvalidNetworkSecurityGroupSecurityRules`, accompagné d’une description similaire à ce qui suit : « Les règles de sécurité dans le Groupe de sécurité réseau configurées avec le sous-réseau n’autorisent pas la connectivité entrante et/ou sortante requise. »

## <a name="cause"></a>Cause :

Ce problème est probablement lié aux règles du [groupe de sécurité réseau](../../virtual-network/virtual-network-vnet-plan-design-arm.md) entrant qui sont configurées pour votre cluster.

## <a name="resolution"></a>Résolution

Accédez au Portail Microsoft Azure et identifiez le groupe de sécurité réseau associé au sous-réseau dans lequel le cluster est en cours de déploiement. Dans la section **Règles de sécurité de trafic entrant**, assurez-vous que les règles autorisent l’accès entrant au port 443 pour les adresses IP indiquées [ici](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
