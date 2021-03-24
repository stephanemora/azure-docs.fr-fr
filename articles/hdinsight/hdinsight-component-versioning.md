---
title: Composants et versions Apache Hadoop – Azure HDInsight
description: Découvrez les composants et versions d’Apache Hadoop dans Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 53ca2ac73fdec9d3b39ffc04cbb24aca707a72eb
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490447"
---
# <a name="azure-hdinsight-versions"></a>Versions d’Azure HDInsight

HDInsight regroupe les composants d’environnement Apache Hadoop et la plateforme HDInsight dans un package déployé sur un cluster. Pour plus d’informations, consultez [Fonctionnement du contrôle de version HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versions de HDInsight prises en charge

Ce tableau répertorie les versions de HDInsight qui sont disponibles dans le portail Azure et dans d’autres méthodes de déploiement telles que PowerShell, CLI et le Kit de développement logiciel (SDK) .NET.

| Version de HDInsight | SYSTÈME D’EXPLOITATION DE LA MACHINE VIRTUELLE | Date de publication | Date d’expiration du support | Date de mise hors service | Haute disponibilité |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24 septembre 2018 | | |Oui |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 avril 2017      | *30 juin 2021 |30 juin 2021 |Oui |

*Nous étendons le délai de prise en charge de certains types de cluster HDInsight 3.6. Voir [Versions du composant HDInsight 3.6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Notes de publication

Pour d’autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight](hdinsight-release-notes.md) .

## <a name="support-options-for-hdinsight-versions"></a>Options de prise en charge des versions de HDInsight

HDInsight offre une prise en charge standard définie comme une période pendant laquelle une version de HDInsight est prise en charge par le service clientèle et le support technique de Microsoft.

L’**expiration du support** signifie que Microsoft ne prend plus en charge la version spécifique de HDInsight. Celle-ci ne sera plus disponible par le biais du portail Azure pour la création de cluster.

La **mise hors service** signifie que les clusters existants d’une version de HDInsight continuent à s’exécuter tels quels. Il n’est pas possible de créer des clusters de cette version par quelque moyen que ce soit (CLI et SDK notamment). D’autres fonctionnalités du plan de contrôle, comme la mise à l’échelle manuelle et la mise à l'échelle automatique, risquent de ne plus fonctionner après la date de mise hors service. Aucun support n’est disponible pour les versions mises hors service.

## <a name="versioning-considerations"></a>Considérations relatives au contrôle de version
- Une fois déployé avec une image, un cluster n’est pas automatiquement mis à niveau vers la version de l’image la plus récente. Lors de la création de nouveaux clusters, la version d’image la plus récente sera déployée.
- Les clients doivent tester et vérifier que les applications s’exécutent correctement lorsqu’ils utilisent la nouvelle version de HDInsight.
- HDInsight se réserve le droit de modifier la version par défaut sans préavis. Si vous dépendez d’une version, spécifiez cette dernière lorsque vous créez vos clusters.
- HDInsight peut mettre hors service une version de composant OSS avant de supprimer la version de HDInsight.

## <a name="next-steps"></a>Étapes suivantes

- [Création de clusters pour Apache Hadoop, Spark, etc. dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pack Sécurité Entreprise](./enterprise-security-package.md)
- [Travailler à partir d’un PC Windows dans Apache Hadoop sur HDInsight](hdinsight-hadoop-windows-tools.md)