---
title: Composants et versions Apache Hadoop – Azure HDInsight
description: Découvrez les composants et versions d’Apache Hadoop dans Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726581"
---
# <a name="azure-hdinsight-versions"></a>Versions d’Azure HDInsight

HDInsight regroupe les composants d’environnement Apache Hadoop et la plateforme HDInsight dans un package déployé sur un cluster. Pour plus d’informations, consultez [Fonctionnement du contrôle de version HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versions de HDInsight prises en charge

Ce tableau répertorie les versions de HDInsight qui sont disponibles dans le portail Azure et dans d’autres méthodes de déploiement telles que PowerShell, CLI et le Kit de développement logiciel (SDK) .NET.

| Version de HDInsight | SYSTÈME D’EXPLOITATION DE LA MACHINE VIRTUELLE | Date de publication| Type de support | Date d’expiration du support | Date de mise hors service | Haute disponibilité |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24 septembre 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Oui |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 avril 2017      | [De base](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Expiration du support standard : 30 juin 2021 <br> Expiration du support de base : 3 avril 2022 |4 avril 2022 |Oui |

*À partir du 1er juillet 2021, Microsoft proposera un Support de base pour certains types de cluster HDI 3.6. Voir [Versions du composant HDInsight 3.6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Notes de publication

Pour d’autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight](hdinsight-release-notes.md) .

## <a name="support-options-for-hdinsight-versions"></a>Options de prise en charge des versions de HDInsight

Une prise en charge est définie comme durée pendant laquelle une version de HDInsight est prise en charge par le support technique et le service clientèle Microsoft. HDInsight propose deux types de prise en charge : 
- **Support standard** est une période pendant laquelle Microsoft fournit des mises à jour et une prise en charge sur les clusters HDInsight.  
    Nous vous recommandons de créer des solutions à l’aide de la version entièrement prise en charge la plus récente. 
- **Support de base** est une période pendant laquelle Microsoft fournit une maintenance limitée au fournisseur de ressources HDInsight. Les images HDInsight et les composants OSS (Open Source Software) ne sont pas desservis.   Seuls les correctifs de sécurité critiques sont corrigés sur les clusters HDInsight.  
  Microsoft n’encourage pas la création de nouveaux clusters ou la création de nouvelles solutions quand une version est en Support de base. Nous vous recommandons de migrer les clusters existants vers la version entièrement prise en charge la plus récente. 

L’**expiration du support** signifie que Microsoft ne prend plus en charge la version spécifique de HDInsight. Celle-ci ne sera plus disponible par le biais du Portail Azure pour la création de cluster.

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
