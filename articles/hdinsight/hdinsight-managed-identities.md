---
title: Identités managées dans Azure HDInsight
description: Offre une vue d’ensemble de l’implémentation d’identités managées dans Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: e7be8fbf5f6c2c59e93d48729785dd34bae5955e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327383"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identités managées dans Azure HDInsight

Une identité managée est une identité inscrite dans Azure Active Directory (Azure AD) dont les informations d’identification sont gérées par Azure. Grâce aux identités managées, vous n’avez pas besoin d’inscrire des principaux de service dans Azure AD, ni de tenir à jour les informations d’identification telles que les certificats.

L’utilisation d’identités managées dans Azure HDInsight permet aux clusters d’accéder aux services de domaine Azure AD, à Azure Key Vault ou aux fichiers stockés dans Azure Data Lake Storage Gen2.

Il existe deux types d’identités managées : celles affectées par l’utilisateur et celles affectées par le système. Azure HDInsight utilise des identités managées affectées par l’utilisateur. Une identité managée attribuée par l’utilisateur est créée en tant que ressource Azure autonome, laquelle peut ensuite être attribuée à une ou plusieurs instances de service Azure. En revanche, une identité managée affectée par le système est créée dans Azure AD, après quoi elle est automatiquement activée directement sur une instance de service Azure particulière. La durée de vie de cette identité managée affectée par le système est alors liée à celle de l’instance de service sur laquelle l’identité managée est activée.

## <a name="hdinsight-managed-identity-implementation"></a>Implémentation des identités managées dans HDInsight

Dans Azure HDInsight, des identités managées sont provisionnées sur chaque nœud du cluster. Ces composants d’identités sont toutefois utilisables uniquement par le service HDInsight. Il n’existe actuellement aucune méthode prise en charge pour générer des jetons d’accès avec les identités managées installées sur des nœuds de cluster HDInsight. Pour certains services Azure, des identités managées sont implémentées avec un point de terminaison que vous pouvez utiliser pour acquérir des jetons d’accès vous permettant d’interagir vous-même avec d’autres services Azure.

## <a name="create-a-managed-identity"></a>Créer une identité managée

Vous pouvez créer des identités managées au moyen de l’une de ces méthodes :

* [Portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interface de ligne de commande Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Les autres étapes à suivre pour configurer l’identité managée varient en fonction du scénario d’utilisation.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scénarios d’utilisation des identités managées dans Azure HDInsight

Les identités managées sont utilisées dans Azure HDInsight dans de multiples scénarios. Consultez les documents connexes pour obtenir des instructions d’installation et de configuration détaillées :

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pack Sécurité Entreprise](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka avec BYOK (Bring Your Own Key) activé](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Étapes suivantes

* [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)
