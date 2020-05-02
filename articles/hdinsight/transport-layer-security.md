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
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183498"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Protocole TLS dans Azure HDInsight

Les connexions au cluster HDInsight via le point de terminaison de cluster public `https://CLUSTERNAME.azurehdinsight.net` sont transmises par proxy via des nœuds de passerelle de cluster. Ces connexions sont sécurisées à l’aide d’un protocole appelé TLS. L’application de versions ultérieures du protocole TLS sur les passerelles améliore la sécurité de ces connexions. Pour plus d’informations sur les raisons pour lesquelles vous devez utiliser des versions plus récentes du protocole TLS, consultez [Résolution du problème TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Par défaut, les clusters Azure HDInsight acceptent les connexions TLS 1.2 sur les points de terminaison HTTPS publics et des versions antérieures pour la compatibilité descendante. Vous pouvez contrôler la version minimale du protocole TLS prise en charge sur les nœuds de passerelle lors de la création du cluster à l’aide du Portail Azure ou d’un modèle Resource Manager. Pour le portail, sélectionnez la version de TLS sous l’onglet **Sécurité + réseau** lors de la création du cluster. Pour un modèle Resource Manager au moment du déploiement, utilisez la propriété **minSupportedTlsVersion**. Pour obtenir un exemple de modèle, consultez [Modèle de démarrage rapide TLS 1.2 minimum pour HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Cette propriété prend en charge trois valeurs : « 1.0 », « 1.1 » et « 1.2 », qui correspondent respectivement à TLS 1.0+, TLS 1.1+ et TLS 1.2+.

> [!IMPORTANT]
> À compter du 30 juin 2020, Azure HDInsight appliquera TLS 1.2 ou des versions ultérieures pour toutes les connexions HTTPS. Nous vous recommandons de vous assurer que tous vos clients sont prêts à gérer TLS 1.2 ou versions ultérieures. Pour plus d’informations, voir [Application de TLS 1.2 par Azure HDInsight](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Étapes suivantes

* [Planifier un réseau virtuel pour Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).
* [Créer des réseaux virtuels pour les clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* [Groupes de sécurité réseau](../virtual-network/security-overview.md).
