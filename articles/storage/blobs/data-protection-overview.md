---
title: Vue d’ensemble de la protection des données
titleSuffix: Azure Storage
description: Les options de protection des données disponibles pour les données de vos Stockage Blob et Azure Data Lake Storage Gen2 vous permettent de protéger celles-ci contre la suppression ou le remplacement. Si vous devez récupérer des données qui ont été supprimées ou remplacées, ce guide peut vous aider à choisir l’option de récupération la mieux adaptée à votre scénario.
services: storage
author: tamram
ms.service: storage
ms.date: 04/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: 90c83397089b77d30694041a37debc0731ea2a38
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304254"
---
# <a name="data-protection-overview"></a>Vue d’ensemble de la protection des données

Le service Stockage Azure fournit une protection des données pour Stockage Blob et Azure Data Lake Storage Gen2 pour vous aider à préparer des scénarios où vous devez récupérer des données supprimées ou remplacées. Il est important de réfléchir à la meilleure façon de protéger vos données avant que se produise un incident susceptible de les compromettre. Ce guide peut vous aider à déterminer à l’avance les fonctionnalités de protection des données que requiert votre scénario, ainsi que la manière de les implémenter. Si vous devez récupérer des données supprimées ou remplacées, cette vue d’ensemble fournit également des conseils sur la façon de procéder, en fonction de votre scénario.

Dans la documentation sur le service Stockage Azure, la *protection des données* fait référence à des stratégies de protection du compte de stockage et des données qu’il contient contre la suppression ou la modification, ou de restauration de données après suppression ou modification. Le service Stockage Azure offre également des options de *récupération d’urgence*, dont plusieurs niveaux de redondance pour protéger vos données contre les pannes de service dues à des problèmes matériels ou à des catastrophes naturelles, et un basculement géré par le client en cas d’indisponibilité du centre de données dans la région primaire. Pour plus d’informations sur la protection des données contre les pannes de service, consultez [Récupération d’urgence](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Recommandations pour la protection de base des données

Si vous recherchez une couverture de protection de base des données pour votre compte de stockage et les données qu’il contient, Microsoft recommande de suivre les étapes suivantes pour commencer :

- Configurez un verrou Azure Resource Manager sur le compte de stockage pour protéger le compte contre la suppression ou les changements de configuration. [En savoir plus…](../common/lock-account-resource.md)
- Activez la suppression réversible de conteneur pour le compte de stockage afin de pouvoir récupérer un conteneur supprimé et son contenu. [En savoir plus…](soft-delete-container-enable.md)
- Enregistrez l’état d’un blob à intervalles réguliers :
  - Pour les charges de travail du Stockage Blob, activez le contrôle de version des blobs pour enregistrer automatiquement l’état de vos données chaque fois qu’un blob est remplacé. [En savoir plus…](versioning-enable.md)
  - Pour les charges de travail d’Azure Data Lake Storage, prenez des captures instantanées manuelles pour enregistrer l’état de vos données à un moment précis dans le temps. [En savoir plus…](snapshots-overview.md)

Ces options, ainsi que d’autres options de protection des données pour d’autres scénarios, sont décrites plus en détail dans la section suivante.

Pour obtenir une vue d’ensemble des coûts liés à ces fonctionnalités, consultez [Résumé des considérations relatives aux coûts](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Vue d’ensemble des options de protection des données

Le tableau suivant récapitule les options disponibles dans Stockage Azure pour les scénarios de protection des données courants. Choisissez les scénarios qui s’appliquent à votre situation pour en savoir plus sur les options disponibles. Notez que certaines fonctionnalités ne sont pas disponibles à ce stade pour les comptes de stockage pour lesquels un espace de noms hiérarchique est activé.

| Scénario | Option de protection des données | Recommandations | Avantage de la protection | Disponible pour Data Lake Storage |
|--|--|--|--|--|
| Empêcher la suppression ou la modification d’un compte de stockage. | Verrou Azure Resource Manager<br />[En savoir plus…](../common/lock-account-resource.md) | Verrouillez l’ensemble de vos comptes de stockage avec un verrou Azure Resource Manager pour empêcher la suppression du compte de stockage. | Protège le compte de stockage contre la suppression et les changements de configuration.<br /><br />Ne protège pas les conteneurs ou blobs du compte contre la suppression ou le remplacement. | Oui |
| Empêcher la suppression ou la modification d’un conteneur et de ses blobs pendant un intervalle de temps que vous contrôlez. | Stratégie d’immuabilité sur un conteneur<br />[En savoir plus…](storage-blob-immutable-storage.md) | Définissez une stratégie d’immuabilité sur un conteneur pour protéger des documents vitaux pour l’entreprise, par exemple, en réponse à des exigences de conformité légales ou réglementaires. | Protège un conteneur et ses blobs contre les suppressions et les remplacements.<br /><br />Quand une stratégie de conservation légale ou de rétention à durée déterminée est activée, le compte de stockage est également protégé contre la suppression. Les conteneurs pour lesquels aucune stratégie d’immuabilité n’a été définie ne sont pas protégés contre la suppression. | Oui, en préversion |
| Restaurer un conteneur supprimé dans un intervalle spécifié. | Suppression réversible de conteneur (préversion)<br />[En savoir plus…](soft-delete-container-overview.md) | Activez la suppression réversible de conteneur pour tous les comptes de stockage, avec un intervalle de rétention minimal de 7 jours.<br /><br />Activez le contrôle de version et la suppression réversible des blobs avec une suppression réversible de conteneur pour protéger des blobs individuels dans un conteneur.<br /><br />Stockez les conteneurs qui nécessitent différentes périodes de rétention dans des comptes de stockage distincts. | Vous pouvez restaurer un conteneur et son contenu supprimés pendant la période de rétention.<br /><br />Seules des opérations au niveau du conteneur (par exemple, [Supprimer un conteneur](/rest/api/storageservices/delete-container)) peuvent être restaurées. La suppression réversible de conteneur ne vous permet pas de restaurer un blob individuel dans le conteneur si celui-ci est supprimé. | Oui, en préversion |
| Enregistrer automatiquement l’état d’un blob dans une version antérieure quand il est remplacé. | Contrôle de version des objets blob<br />[En savoir plus…](versioning-overview.md) | Activez le contrôle de version des blobs, ainsi que la suppression réversible de conteneur et de blob, pour les comptes de stockage où vous avez besoin d’une protection optimale pour les données de blob.<br /><br />Stockez les données de blob qui ne nécessitent pas de contrôle de version dans un compte distinct pour limiter les coûts. | Chaque opération d’écriture de blob crée une nouvelle version. La version actuelle d’un objet blob peut être restaurée à partir d’une version antérieure si la version actuelle est supprimée ou remplacée. | Non |
| Restaurer un blob supprimé ou une version de blob dans un intervalle spécifié. | Suppression réversible de blob<br />[En savoir plus…](soft-delete-blob-overview.md) | Activez la suppression réversible de blob pour tous les comptes de stockage, avec un intervalle de rétention minimal de 7 jours.<br /><br />Activez le contrôle de version de blob et la suppression réversible de conteneur avec la suppression réversible de blob pour une protection optimale des données de blob.<br /><br />Stockez les blobs qui nécessitent différentes périodes de rétention dans des comptes de stockage distincts. | Vous pouvez restaurer un blob supprimé ou une version de blob au cours de la période de rétention. | Non |
| Restaurer un ensemble d’objets blob de blocs à un point antérieur dans le temps. | Restauration dans le temps<br />[En savoir plus…](point-in-time-restore-overview.md) | Pour utiliser une restauration à un instant dans le passé afin de revenir à un état antérieur, concevez votre application pour supprimer des objets blob de blocs individuels au lieu de supprimer des conteneurs. | Un ensemble d’objets blob de blocs peut être rétabli dans l’état où il était à un point spécifique dans le passé.<br /><br />Seules les opérations effectuées sur des objets blob de blocs sont annulées. Les opérations effectuées sur des conteneurs, des objets blob de pages ou des objets blob d’ajouts ne sont pas annulées. | Non |
| Enregistrer manuellement l’état d’un blob à un moment donné. | Instantané d’objet blob<br />[En savoir plus…](snapshots-overview.md) | Alternative recommandée au contrôle de version de blob quand le contrôle de version n’est pas adapté pour votre scénario, pour des raisons de coût ou autres, ou quand un espace de noms hiérarchique est activé pour le compte de stockage. | Vous pouvez restaurer un blob remplacé à partir d’une capture instantanée. Si le blob est supprimé, les captures instantanées le sont également. | Oui, en préversion |
| Un blob peut être supprimé ou remplacé, mais les données sont copiées régulièrement dans un deuxième compte de stockage. | Solution personnalisée pour copier les données vers un deuxième compte à l’aide de la réplication d’objet de Stockage Azure ou d’un outil tel qu’AzCopy ou Azure Data Factory. | Recommandé pour protéger votre tranquillité d’esprit contre des actions intentionnelles inattendues ou des scénarios imprévisibles.<br /><br />Créez le deuxième compte de stockage dans la même région que le compte principal afin d’éviter les frais de sortie. | Les données peuvent être restaurées à partir du deuxième compte de stockage si le compte principal est compromis d’une manière ou d’une autre. | AzCopy et Azure Data Factory sont pris en charge.<br /><br />La réplication d’objet n’est pas prise en charge. |

## <a name="data-protection-by-resource-type"></a>Protection des données par type de ressource

Le tableau suivant récapitule les options de protection des données de Stockage Azure en fonction des ressources qu’elles protègent.

| Option de protection des données | Protège un compte contre la suppression | Protège un conteneur contre la suppression | Protège un blob contre la suppression | Protège un blob contre les remplacements |
|--|--|--|--|--|
| Verrou Azure Resource Manager | Oui | Non<sup>1</sup> | Non | Non |
| Stratégie d’immuabilité sur un conteneur | Oui<sup>2</sup> | Oui | Oui | Oui |
| Suppression réversible de conteneur | Non | Oui | Non | Non |
| Contrôle de version de blob<sup>3</sup> | Non | Non | Oui | Oui |
| Suppression réversible de blob<sup>3</sup> | Non | Non | Oui | Oui |
| Restauration à un instant dans le passé<sup>3</sup> | Non | Non | Oui | Oui |
| Instantané d’objet blob | Non | Non | Non | Oui |
| Solution personnalisée pour copier des données vers un deuxième compte<sup>4</sup> | Non | Oui | Oui | Oui |

<sup>1</sup> Un verrou Azure Resource Manager ne protège pas un conteneur contre la suppression.<br />
<sup>2</sup> Quand une stratégie de conservation légale ou de rétention à durée déterminée est activée pour un conteneur, le compte de stockage est également protégé contre la suppression.<br />
<sup>3</sup> Pas pris en charge actuellement pour les charges de travail Data Lake Storage.<br />
<sup>4</sup> AzCopy et Azure Data Factory sont des options prises en charge pour les charges de travail de Stockage Blob et de Data Lake Storage. La réplication d’objets est prise en charge uniquement pour les charges de travail de Stockage Blob.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Récupérer des données supprimées ou remplacées

Si vous devez récupérer des données qui ont été remplacées ou supprimées, la façon de procéder dépend des options de protection des données que vous avez activées et de la ressource affectée. Le tableau suivant décrit les actions que vous pouvez entreprendre pour récupérer les données.

| Ressource supprimée ou remplacée | Actions de récupération possibles | Exigences pour la récupération |
|--|--|--|
| Compte de stockage | Tenter de récupérer le compte de stockage supprimé<br />[En savoir plus…](../common/storage-account-recover.md) | Le compte de stockage a été créé à l’origine avec le modèle de déploiement Azure Resource Manager, et a été supprimé au cours des 14 derniers jours. Aucun nouveau compte de stockage portant le même nom n’a été créé depuis la suppression du compte d’origine. |
| Conteneur | Récupérer le conteneur supprimé de manière réversible et son contenu<br />[En savoir plus…](soft-delete-container-enable.md) | La suppression réversible de conteneur est activée, et la période de rétention de suppression réversible de conteneur n’a pas encore expiré. |
| Conteneurs et objets blob | Restaurer des données à partir d’un deuxième compte de stockage | Toutes les opérations de conteneur et de blob ont été répliquées sur un deuxième compte de stockage. |
| Blob (tout type) | Restaurer un objet blob à partir d’une version précédente<sup>1</sup><br />[En savoir plus…](versioning-enable.md) | Le contrôle de version de blob est activé, et le blob a une ou plusieurs versions précédentes. |
| Blob (tout type) | Récupérer un blob supprimé de manière réversible<sup>1</sup><br />[En savoir plus…](soft-delete-blob-enable.md) | La suppression réversible de blob est activée et l’intervalle de rétention de suppression réversible n’a pas expiré. |
| Blob (tout type) | Restaurer un blob à partir d’un instantané<br />[En savoir plus…](snapshots-manage-dotnet.md) | Le blob a une ou plusieurs captures instantanées. |
| Ensemble d’objets blob de blocs | Récupérer un ensemble d’objets blob de blocs dans l’état où ils étaient à un point antérieur dans le temps<sup>1</sup><br />[En savoir plus…](point-in-time-restore-manage.md) | La restauration à un instant dans le passé est activée et le point de restauration s’inscrit dans l’intervalle de rétention. Le compte de stockage n’a pas été compromis ou endommagé. |
| Version de blob | Récupérer une version supprimée de manière réversible<sup>1</sup><br />[En savoir plus…](soft-delete-blob-enable.md) | La suppression réversible de blob est activée et l’intervalle de rétention de suppression réversible n’a pas expiré. |

<sup>1</sup> Non pris en charge actuellement pour les charges de travail Data Lake Storage.

## <a name="summary-of-cost-considerations"></a>Récapitulatif des considérations relatives au coût

Le tableau suivant récapitule les considérations relatives au coût pour les différentes options de protection des données décrites dans ce guide.

| Option de protection des données | Considérations relatives au coût |
|-|-|
| Verrou Azure Resource Manager pour un compte de stockage | Aucuns frais pour configurer un verrou sur un compte de stockage. |
| Stratégie d’immuabilité sur un conteneur | Aucuns frais pour configurer une stratégie d’immuabilité sur un conteneur. |
| Suppression réversible de conteneur | Aucuns frais pour activer la suppression réversible de conteneur pour un compte de stockage. Les données d’un conteneur supprimé de manière réversible sont facturées au même tarif que les données actives jusqu’à ce que le conteneur supprimé de manière réversible soit supprimé définitivement. |
| Contrôle de version des objets blob | Aucuns frais pour activer le contrôle de version de blob pour un compte de stockage. Une fois le contrôle de version de blob activé, chaque opération d’écriture ou de suppression sur un blob dans le compte crée une nouvelle version, ce qui peut entraîner des coûts de capacité accrus.<br /><br />Une version de blob est facturée sur la base de pages ou de blocs uniques. Par conséquent, les coûts augmentent à mesure que le blob de base diverge d’une version particulière. La modification du niveau d’un blob ou d’une version de blob peut avoir une incidence sur la facturation. Pour plus d’informations, consultez [Tarification et facturation](versioning-overview.md#pricing-and-billing).<br /><br />Utilisez la gestion du cycle de vie pour supprimer des versions antérieures en fonction des besoins afin de contrôler les coûts. Pour plus d’informations, consultez [Optimiser les coûts en automatisant les niveaux d’accès au Stockage Blob Azure](storage-lifecycle-management-concepts.md). |
| Suppression réversible de blob | Aucun frais pour activer la suppression réversible de blob pour un compte de stockage. Les données d’un blob supprimé de manière réversible sont facturées au même tarif que les données actives jusqu’à ce que le blob supprimé de manière réversible soit supprimé définitivement. |
| Restauration dans le temps | Aucuns frais pour activer la restauration à un instant dans le passé pour un compte de stockage. En revanche, l’activation de la restauration à un instant dans le passé a également pour effet d’activer le contrôle de version, la suppression réversible et le flux de modification des blobs, qui peuvent occasionner des frais supplémentaires.<br /><br />Vous êtes facturé pour la restauration à un instant dans le passé quand vous effectuez une opération de restauration. Le coût d’une opération de restauration dépend de la quantité de données restaurées. Pour plus d’informations, consultez [Tarification et facturation](point-in-time-restore-overview.md#pricing-and-billing). |
| Instantanés d’objet blob | Les données d’un instantané sont facturées sur la base de pages ou de blocs uniques. Par conséquent, les coûts augmentent à mesure que le blob de base diverge de la capture instantanée. La modification du niveau d’un blob ou d’une capture instantanée peut avoir une incidence sur la facturation. Pour plus d’informations, consultez [Tarification et facturation](snapshots-overview.md#pricing-and-billing).<br /><br />Utilisez la gestion du cycle de vie pour supprimer des captures instantanées antérieures en fonction des besoins afin de contrôler les coûts. Pour plus d’informations, consultez [Optimiser les coûts en automatisant les niveaux d’accès au Stockage Blob Azure](storage-lifecycle-management-concepts.md). |
| Copier des données dans un deuxième compte de stockage | La maintenance des données dans un deuxième compte de stockage occasionne des coûts de capacité et de transaction. Si le deuxième compte de stockage se trouve dans une région différente de celle du compte source, la copie des données vers ce deuxième compte entraînera en sus des frais de sortie. |

## <a name="disaster-recovery"></a>Récupération d'urgence

Le service Stockage Azure maintient toujours plusieurs copies de vos données afin qu’elles soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant et des catastrophes naturelles majeures. La redondance garantit que votre compte de stockage répond à ses objectifs de disponibilité et de durabilité, même en cas de défaillance. Pour plus d’informations sur la configuration de votre compte de stockage pour la haute disponibilité, consultez [Redondance de Stockage Azure](../common/storage-redundancy.md).

En cas de défaillance d’un centre de données, si votre compte de stockage est redondant dans deux régions géographiques (géoredondant), vous avez la possibilité de basculer votre compte de la région primaire vers la région secondaire. Pour plus d’informations, consultez [Récupération d’urgence et basculement de compte de stockage](../common/storage-disaster-recovery-guidance.md).

Le basculement géré par le client n’est actuellement pas pris en charge pour les comptes de stockage pour lesquels un espace de noms hiérarchique est activé. Pour plus d’informations, consultez [Fonctionnalités du stockage de blobs disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Étapes suivantes

- [Redondance de Stockage Azure](../common/storage-redundancy.md)
- [Récupération d'urgence et basculement de compte de stockage](../common/storage-disaster-recovery-guidance.md)
