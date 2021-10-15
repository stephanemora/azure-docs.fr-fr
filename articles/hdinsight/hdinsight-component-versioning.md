---
title: Composants et versions Apache Hadoop – Azure HDInsight
description: Découvrez les composants et versions d’Apache Hadoop dans Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 16b9e7f2b790e6f4e758f07c4f352b30357ae32b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277306"
---
# <a name="azure-hdinsight-versions"></a>Versions d’Azure HDInsight

HDInsight regroupe les composants d’environnement Apache Hadoop et la plateforme HDInsight dans un package déployé sur un cluster. Pour plus d’informations, consultez [Fonctionnement du contrôle de version HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versions de HDInsight prises en charge

Ce tableau répertorie les versions de HDInsight qui sont disponibles dans le portail Azure et dans d’autres méthodes de déploiement telles que PowerShell, CLI et le Kit de développement logiciel (SDK) .NET.

| Version de HDInsight | SYSTÈME D’EXPLOITATION DE LA MACHINE VIRTUELLE | Date de publication| Type de support | Date d’expiration du support | Date de mise hors service | Haute disponibilité |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 18.0.4 LTS |24 septembre 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Oui |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 avril 2017      | [De base](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Le support standard a expiré le 30 juin 2021 pour tous les types de clusters.<br> Le support de base expire le 3 avril 2022. Consultez [Composants et versions de HDInsight 3.6](hdinsight-36-component-versioning.md) pour plus d’informations sur les types de clusters. |4 avril 2022 |Oui |

L’**expiration du support** signifie que Microsoft ne prend plus en charge la version spécifique de HDInsight. Celle-ci ne sera plus disponible par le biais du Portail Azure pour la création de cluster.

La **mise hors service** signifie que les clusters existants d’une version de HDInsight continuent à s’exécuter tels quels. Il n’est pas possible de créer des clusters de cette version par quelque moyen que ce soit (CLI et SDK notamment). D’autres fonctionnalités du plan de contrôle, comme la mise à l’échelle manuelle et la mise à l'échelle automatique, risquent de ne plus fonctionner après la date de mise hors service. Aucun support n’est disponible pour les versions mises hors service.

## <a name="support-options-for-hdinsight-versions"></a>Options de prise en charge des versions de HDInsight

Une prise en charge est définie comme durée pendant laquelle une version de HDInsight est prise en charge par le support technique et le service clientèle Microsoft. HDInsight propose deux types de prise en charge : 
- **Support standard**
- **Support de base**

### <a name="standard-support"></a>Support standard

Le support standard fournit des mises à jour et un support sur les clusters HDInsight. Microsoft recommande de créer des solutions en utilisant la version entièrement prise en charge la plus récente. 

Le support standard comprend les éléments suivants :
- Possibilité de créer des demandes de support sur des clusters HDInsight 4.0.
- Prise en charge de la résolution des problèmes des solutions construites sur des clusters 4.0. 
- Demandes de redémarrage de services ou de nœuds.
- Analyse de la cause racine sur les demandes de support.
- Analyse de la cause racine ou correctifs pour améliorer les performances des travaux ou des requêtes.
- Analyse de la cause racine ou correctifs pour améliorer les modifications initiées par le client, par exemple, la modification des configurations de service ou des problèmes dus à des actions de script personnalisées.
- Mises à jour du produit pour les correctifs de sécurité critiques jusqu’à la mise hors service de la version.
- Mises à jour délimitées du produit pour le fournisseur de ressources HDInsight.
- Correctifs ou modifications sélectifs des images HDInsight 4.0 ou des versions des composants des logiciels Open Source (OSS).

### <a name="basic-support"></a>Support de base

Le support de base fournit un service limité au fournisseur de ressources HDInsight. Les images HDInsight et les composants OSS (Open Source Software) ne sont pas desservis. Seuls les correctifs de sécurité critiques sont corrigés sur les clusters HDInsight. 

Le support de base comprend les éléments suivants :
- Utilisation continue des clusters HDInsight 3.6 existants.
- Capacité des clients HDInsight 3.6 existants à créer de nouveaux clusters 3.6.
- Possibilité d’effectuer un scale-up ou un scale-down des clusters HDInsight 3.6 à l’aide de la mise à l’échelle automatique ou de la mise à l’échelle manuelle.
- Mises à jour délimitées du produit pour le fournisseur de ressources HDInsight.
- Mises à jour du produit pour les correctifs de sécurité critiques jusqu’à la mise hors service de la version.
- Possibilité de créer des demandes de support sur des clusters HDInsight 3.6.
- Demandes de redémarrage de services ou de nœuds.

Le support de base n’inclut pas les éléments suivants :
- Correctifs ou modifications des images HDInsight 3.6 ou des versions des composants des logiciels Open Source (OSS).
- Prise en charge de la résolution des problèmes des solutions construites sur des clusters 3.6. 
- Ajout de nouvelles caractéristiques ou fonctionnalités.
- Support pour les conseils ou les requêtes ad-hoc.
- Analyse de la cause racine sur les demandes de support.
- Analyse de la cause racine ou correctifs pour améliorer les performances des travaux ou des requêtes.
- Analyse de la cause racine ou correctifs pour améliorer les modifications initiées par le client, par exemple, la modification des configurations de service ou des problèmes dus à des actions de script personnalisées.

Microsoft n’encourage pas la création de pipelines ou de solutions d’analyse sur les clusters en support de base. Nous vous recommandons de migrer les clusters existants vers la version entièrement prise en charge la plus récente. 

## <a name="release-notes"></a>Notes de publication

Pour d’autres notes de publication sur les dernières versions de HDInsight, consultez la page [Notes de publication de HDInsight](hdinsight-release-notes.md) .

## <a name="versioning-considerations"></a>Considérations relatives au contrôle de version
- Une fois déployé avec une image, un cluster n’est pas automatiquement mis à niveau vers la version de l’image la plus récente. Lors de la création de nouveaux clusters, la version d’image la plus récente sera déployée.
- Les clients doivent tester et vérifier que les applications s’exécutent correctement lorsqu’ils utilisent la nouvelle version de HDInsight.
- HDInsight se réserve le droit de modifier la version par défaut sans préavis. Si vous dépendez d’une version, spécifiez cette dernière lorsque vous créez vos clusters.
- HDInsight peut mettre hors service une version de composant OSS avant de supprimer la version de HDInsight.

## <a name="next-steps"></a>Étapes suivantes

- [Création de clusters pour Apache Hadoop, Spark, etc. dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pack Sécurité Entreprise](./enterprise-security-package.md)
- [Travailler à partir d’un PC Windows dans Apache Hadoop sur HDInsight](hdinsight-hadoop-windows-tools.md)
