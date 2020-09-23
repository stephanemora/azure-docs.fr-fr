---
title: Protocole TLS dans Azure HDInsight
description: TLS (Transport Layer Security) et SSL (Secure Sockets Layer) sont des protocoles de chiffrement qui permettent la sécurité des communications sur un réseau d’ordinateurs.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 6e46cca28c049a794db617797d5f09e9f3b5720d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006889"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Protocole TLS dans Azure HDInsight

Les connexions au cluster HDInsight via le point de terminaison de cluster public `https://CLUSTERNAME.azurehdinsight.net` sont transmises par proxy via des nœuds de passerelle de cluster. Ces connexions sont sécurisées à l’aide d’un protocole appelé TLS. L’application de versions ultérieures du protocole TLS sur les passerelles améliore la sécurité de ces connexions. Pour plus d’informations sur les raisons pour lesquelles vous devez utiliser des versions plus récentes du protocole TLS, consultez [Résolution du problème TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Par défaut, les clusters Azure HDInsight acceptent les connexions TLS 1.2 sur les points de terminaison HTTPS publics et des versions antérieures pour la compatibilité descendante. Vous pouvez contrôler la version minimale du protocole TLS prise en charge sur les nœuds de passerelle lors de la création du cluster à l’aide du Portail Azure ou d’un modèle Resource Manager. Pour le portail, sélectionnez la version de TLS sous l’onglet **Sécurité + réseau** lors de la création du cluster. Pour un modèle Resource Manager au moment du déploiement, utilisez la propriété **minSupportedTlsVersion**. Pour obtenir un exemple de modèle, consultez [Modèle de démarrage rapide TLS 1.2 minimum pour HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Cette propriété prend en charge trois valeurs : « 1.0 », « 1.1 » et « 1.2 », qui correspondent respectivement à TLS 1.0+, TLS 1.1+ et TLS 1.2+.


## <a name="next-steps"></a>Étapes suivantes

* [Planifier un réseau virtuel pour Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).
* [Créer des réseaux virtuels pour les clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* [Groupes de sécurité réseau](../virtual-network/security-overview.md).
