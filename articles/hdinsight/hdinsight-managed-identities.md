---
title: Les identités dans Azure HDInsight
description: Fournit une vue d’ensemble de l’implémentation d’identités gérées dans Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 5012b669b7460a44cb2732d7db7bf76fd1f567cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715766"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Les identités dans Azure HDInsight

Une identité gérée est une identité inscrite dans Azure Active Directory (Azure AD) dont informations d’identification sont gérées par Azure. Avec des identités gérées, vous n’avez pas besoin d’inscrire des principaux de service dans Azure AD ou mettre à jour les informations d’identification telles que les certificats.

Identités gérées utilisable dans Azure HDInsight pour permettre à vos clusters pour accéder aux services de domaine Azure AD, accès à Azure Key Vault ou accéder aux fichiers dans le stockage Azure Data Lake Gen2.

Il existe deux types d’identités gérées : affectée à l’utilisateur et attribué par le système. Azure HDInsight utilise des identités gérées affectée à l’utilisateur. Une identité gérée affectée à l’utilisateur est créée comme une ressource Azure qui vous pouvez ensuite affecter à une ou plusieurs instances de service Azure autonome. En revanche, une identité gérée attribué par le système est créée dans Azure AD et ensuite automatiquement activée directement sur une instance de service Azure particulier. La durée de vie de cette identité gérée attribué par le système est ensuite liée à la durée de vie de l’instance de service, il est activé sur.

## <a name="hdinsight-managed-identity-implementation"></a>Implémentation de l’identité gérée de HDInsight

Dans Azure HDInsight, les identités sont configurées sur chaque nœud du cluster. Ces composants d’identité, toutefois, ne sont utilisables par le service HDInsight. Il n’existe actuellement aucune méthode prise en charge de générer des jetons d’accès à l’aide des identités gérées installées sur les nœuds de cluster HDInsight. Pour certains services Azure, des identités gérées sont implémentées avec un point de terminaison que vous pouvez utiliser pour acquérir des jetons d’accès pour l’interaction avec d’autres services Azure sur votre propre.

## <a name="create-a-managed-identity"></a>Créer une identité gérée

Identités gérées peuvent être créées avec une des méthodes suivantes :

* [Portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interface de ligne de commande Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Les étapes restantes pour la configuration de l’identité gérée varient selon le scénario où il sera utilisé.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scénarios d’identité gérée dans Azure HDInsight

Les identités sont utilisées dans Azure HDInsight dans plusieurs scénarios. Consultez les documents connexes pour le programme d’installation détaillée et des instructions de configuration :

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-managed-identity)
* [Pack Sécurité Entreprise](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Étapes suivantes

* [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)
