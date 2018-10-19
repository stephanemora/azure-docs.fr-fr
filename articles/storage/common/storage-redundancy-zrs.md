---
title: Générer des applications de stockage Azure hautement disponibles sur le stockage redondant dans une zone (ZRS) | Microsoft Docs
description: Le stockage redondant dans une zone (ZRS) offre un moyen simple de générer des applications hautement disponibles. ZRS protège contre les défaillances matérielles dans le data center et contre certains sinistres régionaux.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: 0a4ebefd89414fa62ca69f2f9cc726f471694bee
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733614"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Stockage redondant dans une zone (ZRS) : applications de stockage Azure hautement disponibles
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Couverture du support et disponibilité régionale
Le stockage ZRS prend actuellement en charge les types de comptes de stockage standard à usage général v2. Pour plus d’informations sur les types de comptes de stockage, consultez l’article [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

Le stockage ZRS est disponible pour les objets blob de blocs, les objets blob de pages non-disque, les fichiers, les tables et les files d’attente.

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

Le stockage ZRS ne peut pas protéger vos données contre un sinistre régional, lorsque plusieurs zones sont affectées définitivement. Au lieu de cela, le stockage ZRS contribue à la résilience de vos données s’il devient temporairement indisponible. En guise de protection face à des sinistres régionaux, Microsoft recommande l’utilisation d’un stockage géoredondant (GRS). Pour plus d’informations sur le stockage GRS, consultez [Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure](storage-redundancy-grs.md).

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
> Le 31 mars 2021, Microsoft abandonnera et migrera les comptes ZRS classiques. Avant cet abandon, les clients du stockage ZRS classique recevront plus d’informations. 
>
> Lorsque le stockage ZRS est mis à la [disposition générale](#support-coverage-and-regional-availability) dans une région, les clients ne peuvent plus créer de compte ZRS classiques à partir du portail dans cette région. L’utilisation de Microsoft PowerShell et d’Azure CLI pour créer des comptes ZRS classiques est prise en charge tant que le stockage ZRS classique n’est pas abandonné.

ZRS classique réplique les données de manière asynchrone entre les centres de données d’une à deux régions. Il est possible qu’un réplica ne soit disponible que si Microsoft lance le basculement vers la région secondaire. Un compte ZRS classique ne peut pas être converti vers ou depuis le stockage LRS ou GRS et n’a pas de fonctionnalité de mesures ou de journalisation.

ZRS classique est disponible uniquement pour les **objets blob de blocs** dans les comptes de stockage à usage général v1 (GPv1). Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

Les comptes ZRS classiques ne peuvent pas être convertis en ou à partir d’un stockage LRS, GRS ou RA-GRS. De plus, les comptes ZRS classiques ne prennent en charge ni les métriques ni la journalisation.

Pour migrer manuellement des données de compte ZRS vers ou depuis un compte LRS, ZRS classique, GRS ou RA-GRS, utilisez AzCopy, l’Explorateur Stockage Azure, Azure PowerShell ou Azure CLI. Vous pouvez également créer votre propre solution de migration avec l’une des bibliothèques clientes de Stockage Azure.

## <a name="see-also"></a>Voir aussi
- [Réplication Azure Storage](storage-redundancy.md)
- [Stockage localement redondant (LRS) : redondance des données à faible coût pour le stockage Azure](storage-redundancy-lrs.md)
- [Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure](storage-redundancy-grs.md)