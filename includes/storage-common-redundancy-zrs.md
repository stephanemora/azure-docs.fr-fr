---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5f7477671e7be4e6e8fd736ab94fa7e7b1e99a6e
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184447"
---
Le stockage redondant dans une zone (ZRS) réplique vos données de façon synchrone sur trois clusters de stockage dans une seule région. Chaque cluster de stockage est physiquement séparé des autres et se trouve dans sa propre zone de disponibilité. Chaque zone de disponibilité, et le cluster ZRS qu’elle contient, est autonome, avec des utilitaires et fonctionnalités de mise en réseau séparés.

Le stockage de vos données dans un compte ZRS vous permet d’accéder et de gérer vos données au cas où une zone n’est plus disponible. Le stockage redondant dans une zone (ZRS) offre d’excellentes performances et une latence faible. Le stockage ZRS offre les mêmes [objectifs d’évolutivité](../articles/storage/common/storage-scalability-targets.md) que le [stockage localement redondant (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Envisagez le stockage ZRS pour des scénarios qui requièrent une cohérence forte, une durabilité élevée et une haute disponibilité, même si une panne ou une catastrophe naturelle rend un centre de données zonal indisponible. Le stockage ZRS offre une durabilité des objets de stockage d’au moins 99,9999999999 % (12 neuf) sur une année donnée.

Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Couverture du support et disponibilité régionale
Le stockage ZRS prend actuellement en charge les types de comptes de stockage standard [à usage général v2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts). Le stockage ZRS est disponible pour les objets blob de blocs, les objets blob de pages non-disque, les fichiers, les tables et les files d’attente. En outre, tous vos journaux [d’analyse du stockage](../articles/storage/common/storage-analytics.md) et [métriques de stockage](../articles/storage/common/storage-enable-and-view-metrics.md)

Le stockage ZRS est généralement disponible dans les régions suivantes :

- USA Est
- USA Est 2
- USA Ouest 2
- USA Centre
- Europe Nord
- Europe Ouest
- France Centre
- Asie Sud-Est

Microsoft continue d’activer le stockage ZRS dans d’autres régions Azure. Consultez la page [Mises à jour de service Azure](https://azure.microsoft.com/updates/) régulièrement pour plus d’informations sur les nouvelles régions.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Que se passe-t-il lorsqu’une zone n’est plus disponible ?

Si une zone n’est plus disponible, vos données restent résilientes. Microsoft vous recommande de continuer à suivre les pratiques pour la gestion des erreurs temporaires, telles que l’implémentation de stratégies de nouvelle tentative avec temporisation exponentielle. Lorsqu’une zone n’est pas disponible, Azure procède à des mises à jour des réseaux, telles que le rejointoiement DNS. Ces mises à jour peuvent affecter votre application si vous accédez à vos données avant qu’elles ne soient terminées.

Le stockage ZRS ne peut pas protéger vos données contre un sinistre régional, lorsque plusieurs zones sont affectées définitivement. Au lieu de cela, le stockage ZRS contribue à la résilience de vos données s’il devient temporairement indisponible. En guise de protection face à des sinistres régionaux, Microsoft recommande l’utilisation d’un stockage géoredondant (GRS). Pour plus d’informations sur le stockage GRS, consultez [Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Conversion en réplication ZRS
Aujourd’hui, vous pouvez utiliser le portail Azure ou l’API de fournisseur de ressources de stockage pour modifier le type de redondance de votre compte, tant que vous effectuez une migration vers ou à partir d’un stockage LRS, GRS et RA-GRS. Avec le stockage ZRS, toutefois, la migration n’est pas aussi simple, car elle implique le déplacement de données physique depuis un tampon de stockage unique vers plusieurs tampons au sein d’une région. 

Vous avez deux options principales pour la migration vers ou depuis le stockage ZRS. Vous pouvez copier ou déplacer manuellement des données vers un nouveau compte de stockage ZRS depuis votre compte existant. Vous pouvez également demander une migration dynamique. Microsoft vous recommande vivement d’effectuer une migration manuelle, car l’exécution de la migration dynamique n’est pas garantie. Un itinéraire de migration manuelle assure un traitement plus souple qu’une migration dynamique et vous pouvez contrôler la durée de la migration.

Vous disposez d’une variété d’options pour effectuer une migration manuelle :
- Utilisez des outils existants comme AzCopy, le kit de développement logiciel (SDK) de stockage, des outils tiers fiables, etc.
- Si vous connaissez déjà Hadoop ou HDInsight, vous pouvez attacher à la fois le compte source et le compte de destination (ZRS) à votre cluster et utiliser quelque chose comme DistCp pour paralléliser massivement le processus de copie des données
- Créer vos propres outils à l’aide d’une version du kit de développement logiciel (SDK) du stockage

Si, toutefois, une migration manuelle entraîne un temps d’arrêt de l’application et que vous ne parvenez pas à l’absorber de votre côté, Microsoft propose une option de migration dynamique. Une migration dynamique est une migration sur place qui vous permet de continuer à utiliser votre compte de stockage existant, tandis que vos données sont transférées entre les tampons de stockage source et de destination. Pendant la migration, vous aurez toujours le même niveau de contrat SLA pour la disponibilité et la durabilité que d’habitude.

La migration dynamique est toutefois fournie avec certaines restrictions. Elles sont répertoriées ci-dessous.

- Même si Microsoft répond rapidement à votre demande de migration dynamique, l’exécution de la migration n’est pas garantie. Si vous avez besoin que vos données soient dans le stockage ZRS à un certain moment, vous devez effectuer une migration manuelle. En général, plus la quantité de données dans votre compte est élevée, plus la migration des données est longue. 
- Vous ne pouvez effectuer une migration dynamique qu’à partir d’un compte à l’aide de la réplication LRS ou GRS. Si votre compte utilise le stockage RA-GRS, vous devez d’abord migrer vers un de ces types de réplication avant de continuer. Cette étape intermédiaire garantit la suppression du point de terminaison secondaire en lecture seule fournie par le stockage RA-GRS avant la migration.
- Votre compte doit contenir des données.
- Seules les migrations intra-régions sont prises en charge. Si vous souhaitez migrer vos données vers un compte de stockage ZRS situé dans une région différente de celle du compte source, vous devez effectuer une migration manuelle.
- Seuls les types de compte de stockage standard sont pris en charge. Vous ne pouvez pas migrer à partir d’un compte de stockage Premium.

Les demandes de migration dynamique passent par le portail de support Azure. À partir du portail, sélectionnez le compte de stockage que vous souhaitez convertir en stockage ZRS.
1. Cliquez sur **Nouvelle demande de support**
2. Vérifiez les concepts de base. Cliquez sur **Suivant**. 
3. Dans la section **Problème**, 
    - Laissez le niveau de gravité tel quel.
    - Type de problème = **Migration des données**
    - Catégorie = **Migrer vers un stockage ZRS au sein d’une région**
    - Titre = **Migration d’un compte ZRS** (ou un titre descriptif)
    - Détails = J’aimerais migrer vers un stockage ZRS depuis un stockage [LRS, GRS] dans la région ______. 
4. Cliquez sur **Suivant**.
5. Vérifiez que les informations de contact sont correctes dans le panneau Informations de contact.
6. Cliquez sur **Envoyer**.

Vous serez mis en contact avec une personne du support technique. Cette personne sera disponible pour vous fournir toute assistance dont vous pourrez avoir besoin. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS classique : une option héritée pour la redondance des objets blob de blocs
> [!NOTE]
> Les comptes ZRS classiques sont prévus pour être un abandon et une migration requise sur le 31 mars 2021. Microsoft enverra plus de détails aux clients ZRS classique avant la désapprobation. Microsoft prévoit de fournir un processus de migration automatisée de ZRS classique vers ZRS ultérieurement.

>[!NOTE]
> Lorsque le stockage ZRS est [généralement disponible](#support-coverage-and-regional-availability) dans une région, vous ne pourrez plus créer de compte ZRS classique à partir du portail dans cette même région. Toutefois, vous pourrez toujours en créer un par d’autres moyens, tels que Microsoft PowerShell et Azure CLI, ’est-à-dire, jusqu’à ce que ZRS classique soit déconseillé.

ZRS classique réplique les données de manière asynchrone entre les centres de données d’une à deux régions. Il est possible qu’un réplica ne soit disponible que si Microsoft lance le basculement vers la région secondaire. ZRS classique est disponible uniquement pour les **objets blob de blocs** dans les comptes de stockage [à usage général v1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts). Un compte ZRS classique ne peut pas être converti vers ou depuis le stockage LRS ou GRS et n’a pas de fonctionnalité de mesures ou de journalisation.

Les comptes ZRS classiques ne peuvent pas être convertis en ou à partir d’un stockage LRS, GRS ou RA-GRS. De plus, les comptes ZRS classiques ne prennent en charge ni les métriques ni la journalisation.

Pour migrer manuellement des données de compte ZRS vers ou depuis un compte LRS, ZRS classique, GRS ou RA-GRS, utilisez AzCopy, l’Explorateur Stockage Azure, Azure PowerShell ou Azure CLI. Vous pouvez également créer votre propre solution de migration avec l’une des bibliothèques clientes de Stockage Azure.
