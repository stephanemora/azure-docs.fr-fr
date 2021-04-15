---
title: 'Niveaux d’accès pour Stockage Blob Azure : chaud, froid et archive'
description: En savoir plus sur les niveaux d’accès chaud, froid et archive pour Stockage Blob Azure. Examinez les comptes de stockage qui prennent en charge la hiérarchisation.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 957973cc4f53dba10ed9d635c8e3f69fd66ee33b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278420"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Niveaux d’accès pour Stockage Blob Azure : chaud, froid et archive

Le service de stockage Azure propose différents niveaux d’accès, ce qui vous permet de stocker vos objets blob de la manière la plus économique. Les niveaux d’accès disponibles incluent les suivants :

- **Chaud** : optimisé pour le stockage des données souvent sollicitées.
- **Froid** : optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 30 jours.
- **Archive** : optimisé pour le stockage des données rarement sollicitées et stockées depuis au moins 180 jours, sous des conditions de latence flexibles, de l’ordre de quelques heures.

Les considérations suivantes s’appliquent aux différents niveaux de stockage :

- Le niveau d’accès peut être défini sur un objet blob pendant ou après le chargement.
- Seuls les niveaux d’accès chaud et froid peuvent être définis au niveau du compte. Le niveau d’accès archive peut être appliqué uniquement au niveau de l’objet blob.
- Les données du niveau d’accès froid offrent une disponibilité légèrement inférieure, mais présentent toujours des caractéristiques de durabilité élevée, de latence d’extraction et de débit similaires à celles des données chaudes. Concernant les données froides, une disponibilité légèrement inférieure et des coûts d’accès supérieurs sont des compromis acceptables pour des coûts de stockage globaux plus faibles, par rapport aux données chaudes. Pour plus d’informations, consultez la page [Contrat SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Les données du niveau d’accès archive sont stockées hors connexion. Le niveau d’accès archive offre les coûts de stockage les plus bas, mais également les coûts d’accès et la latence les plus élevés.
- Les niveaux à chaud et à froid prennent en charge toutes les options de redondance. Le niveau archivage prend en charge uniquement LRS, GRS et RA-GRS.
- Les limites de stockage des données sont établies au niveau du compte et non par niveau d’accès. Vous pouvez choisir d’utiliser toute votre limite sur un seul niveau ou sur les trois niveaux.

Les données stockées dans le cloud connaissent une croissance exponentielle. Pour gérer les coûts liés à vos besoins de stockage en pleine expansion, il est utile d’organiser vos données selon des attributs tels que la fréquence d’accès et la période de rétention prévue pour optimiser les coûts. Les données stockées dans le cloud peuvent être différentes selon le mode de génération, de traitement et d’accès tout au long de leur durée de vie. Certaines données sont activement sollicitées et modifiées tout au long de leur durée de vie. Certaines sont fréquemment sollicitées au début de leur durée de vie, puis les accès se raréfient considérablement à mesure qu’elles deviennent plus anciennes. D’autres sont inactives dans le cloud dès le départ et sont peu, voire pas sollicitées après avoir été stockées.

Chacun des scénarios d’accès aux données peut bénéficier des avantages d’un niveau d’accès différent, gage d’optimisation pour un modèle d’accès particulier. Les niveaux d’accès chaud, froid et archive permettent à Stockage Blob Azure de répondre à ce besoin de niveaux d’accès différenciés avec des modèles de tarification distincts.

Les outils et bibliothèques de client suivants prennent tous en charge la hiérarchisation au niveau de l’objet blob et le stockage archive.

- Portail Azure
- PowerShell
- Outils Azure CLI
- Bibliothèque de client .NET
- Bibliothèque de client Java
- Bibliothèque de client Python
- Bibliothèque de client Node.js

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Comptes de stockage prenant en charge la hiérarchisation

La hiérarchisation des données de stockage d’objets entre les niveaux d’accès chaud, froid et archive est prise en charge sur les comptes Stockage Blob ou Usage général v2 (GPv2). Les comptes Usage général v1 (GPv1) ne prennent pas en charge la hiérarchisation. Vous pouvez facilement convertir vos comptes Stockage Blob ou GPv1 en comptes GPv2 depuis le portail Azure. GPv2 offre de nouveaux prix et fonctionnalités pour les objets blob, les fichiers et les files d’attente. Certaines fonctionnalités et réductions tarifaires sont proposées uniquement dans les comptes GPv2. Certaines charges de travail peuvent être plus coûteuses sur GPv2 que sur GPv1. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

Les comptes Stockage Blob et GPv2 exposent l’attribut **Niveau d’accès** au niveau du compte. Cet attribut vous permet de spécifier le niveau d’accès par défaut pour tous les objets blob ne disposant pas déjà d’un niveau défini explicitement au niveau de l’objet. Le niveau du compte ne s’applique pas aux objets disposant d’un niveau défini explicitement. Le niveau d’accès archive ne peut être appliqué qu’au niveau de l’objet. Vous pouvez passer d’un niveau d’accès à un autre à tout moment.

Utilisez des comptes GPv2 à la place des comptes de Stockage Blob pour la hiérarchisation. GPv2 prend en charge toutes les fonctionnalités prises en charge par les comptes Stockage Blob, ainsi que beaucoup d’autres. La tarification entre les comptes Stockage Blob et GPv2 est presque identique, mais certaines nouvelles fonctionnalités et réductions tarifaires sont uniquement disponibles sur les comptes GPv2.

La structure de tarification entre des comptes GPv1 et GPv2 est différente et les clients doivent évaluer soigneusement le fait de passer à des comptes GPv2. Vous pouvez facilement convertir un compte de stockage d’objets blob ou GPv1 existant vers un compte GPv2 en un simple clic. Pour plus d’informations, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Niveau d’accès chaud

Le niveau d’accès chaud possède des coûts de stockage plus élevés que les niveaux d’accès froid et archive, mais également les coûts d’accès les plus faibles. Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès chaud :

- Données activement utilisées ou censées être fréquemment sollicitées en lecture et écriture
- Données conservées pour le traitement et la migration finale vers le niveau d’accès froid

## <a name="cool-access-tier"></a>Niveau d’accès froid

Le niveau d’accès froid possède des coûts de stockage plus faibles et des coûts d’accès plus élevés que le stockage chaud. Ce niveau est prévu pour les données qui resteront dans le niveau froid pendant au moins 30 jours. Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès froid :

- Sauvegarde à court terme et récupération d’urgence
- Données plus anciennes peu utilisées mais qui doivent être disponibles immédiatement si nécessaire
- Jeux de données volumineux qui doivent être stockés de manière économique tout en permettant la collecte de plus de données à des fins de traitement ultérieur

## <a name="archive-access-tier"></a>Niveau d’accès archive

Le niveau d’accès archive dispose du plus faible coût de stockage, mais les coûts d’extraction de données sont plus élevés par rapport aux niveaux chaud et froid. Les données doivent rester dans le niveau d’accès archive pendant au moins 180 jours ; sinon, elles sont soumises à des frais de suppression anticipée. La récupération des données du niveau de stockage archive peut prendre plusieurs heures selon la priorité de la réactivation spécifiée. Pour les petits objets, une réactivation de haute priorité permet de récupérer l’objet à partir de l’archive en moins d’une heure. Pour en savoir plus, consultez [Réalimenter les données d’objets blob à partir du niveau Archive](storage-blob-rehydration.md).

Tant qu’un objet blob se trouve dans un stockage archive, les données blob sont hors connexion et ne peuvent pas être lues ni modifiées. Pour lire ou télécharger un objet blob dans une archive, vous devez d’abord le réalimenter vers un niveau en ligne. Vous ne pouvez pas prendre de captures instantanées d’un objet blob dans un stockage archive. Toutefois, les métadonnées de l’objet blob restent en ligne et disponibles, ce qui vous permet de lister l’objet blob, ses propriétés, ses métadonnées et ses balises d’index. Ni la définition ni la modification des métadonnées d’objet blob ne sont autorisées dans l’archive. Toutefois, vous pouvez définir et modifier les balises d’index des objets blob. Pour les objets blob en archive, les seules opérations valides sont [Obtenir les propriétés d’un objet blob](/rest/api/storageservices/get-blob-properties), [Obtenir les métadonnées de l’objet blob](/rest/api/storageservices/get-blob-metadata), [Définir des étiquettes d’objet blob](/rest/api/storageservices/set-blob-tags), [Obtenir les étiquettes d’objet blob](/rest/api/storageservices/get-blob-tags), [Rechercher des objets blob par étiquettes](/rest/api/storageservices/find-blobs-by-tags), [Liste des blobs](/rest/api/storageservices/list-blobs), [Définir un niveau d’objet blob](/rest/api/storageservices/set-blob-tier), [Copier l’objet blob](/rest/api/storageservices/copy-blob) et [Supprimer l’objet blob](/rest/api/storageservices/delete-blob).

Voici quelques exemples de scénarios d’utilisation pour le niveau d’accès archive :

- Sauvegarde à long terme, sauvegarde secondaire et jeux de données d’archivage
- Données d’origine (brutes) qui doivent être conservées, même après leur traitement sous un format final exploitable
- Données de conformité et d’archivage qui doivent être stockées à long terme et qui sont très rarement sollicitées

> [!NOTE]
> Le niveau archive n’est pas pris en charge sur les comptes ZRS, GZRS et RA-GZRS. La migration de LRS vers GRS est prise en charge à condition qu’aucun objet blob n’ait été déplacé vers le niveau archive lorsque le compte était défini sur LRS. Un compte peut être redéfini sur GRS si la mise à jour est effectuée moins de 30 jours après la définition du compte sur LRS et si aucun objet blob n’a été déplacé vers le niveau archive lorsque le compte était défini sur LRS.

## <a name="account-level-tiering"></a>Hiérarchisation au niveau du compte

Les objets blob des trois niveaux d’accès peuvent coexister au sein d’un même compte. Tout objet blob ne disposant pas d’un niveau explicitement attribué déduit le niveau à partir du paramètre de niveau d’accès du compte. Si le niveau d’accès émane du compte, vous voyez la propriété de l’objet blob **Niveau d’accès déduit** définie sur la valeur « True », et la propriété de l’objet blob **Niveau d’accès** correspond au niveau du compte. Dans le portail Azure, la propriété du _niveau d’accès déduit_ est affichée avec le niveau d’accès des objets blob comme **Chaud (déduit)** ou **Froid (déduit)** .

La modification du niveau d’accès du compte s’applique à tous les objets de _niveau d’accès déduit_ stockés dans le compte et ne possédant pas un ensemble de niveau explicite. Si vous passez le niveau du compte de chaud à froid, vous serez facturé pour les opérations d’écriture (par 10 000) de tous les objets blobs n’ayant aucun niveau configuré dans les comptes GPv2 uniquement. Ce changement n’est pas facturé dans les comptes Stockage Blob. Vous serez facturé pour les opérations de lecture (par 10 000) et d’extraction de données (par Go) si vous passez votre compte Stockage Blob ou GPv2 d’un stockage froid à chaud.

Seuls les niveaux d’accès chaud et froid peuvent être choisis comme le niveau d’accès par défaut du compte. Le niveau archive ne peut être choisi qu’au niveau d’un objet. Au moment du chargement d’un objet blob, vous pouvez spécifier le niveau d’accès de votre choix (chaud, froid ou archive) indépendamment du niveau par défaut du compte. Cette fonctionnalité vous permet d’écrire des données directement dans le niveau archive pour réaliser des économies à partir du moment où vous créez des données dans le stockage d’objets blob.

## <a name="blob-level-tiering"></a>Hiérarchisation au niveau de l’objet blob

La hiérarchisation au niveau de l’objet blob vous permet de charger des données vers le niveau d’accès de votre choix à l’aide des opérations [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block List](/rest/api/storageservices/put-block-list) et de modifier le niveau de vos données au niveau de l’objet à l’aide de l’opération [Définir un niveau d’objet blob](/rest/api/storageservices/set-blob-tier) ou de la fonctionnalité [Gestion de cycle de vie](#blob-lifecycle-management). Vous pouvez charger des données vers le niveau d'accès requis, puis facilement modifier le niveau d’accès (chaud, froid ou archive) d’un objet blob, lorsque les modes d’utilisation changent, sans avoir à déplacer des données entre les comptes. Toutes les demandes de changement de niveau se produisent immédiatement et les changements de niveau entre chaud et froid sont instantanés. La réactivation d’un objet blob à partir d’un niveau archive peut prendre plusieurs heures.

L’heure de la dernière modification du niveau de l’objet blob est exposée via la propriété de l’objet blob **Access Tier Change Time**. La propriété **Access Tier Change Time** est une propriété de niveau blob qui n’est pas mise à jour lors de la modification du niveau de compte par défaut. Les propriétés de compte et les propriétés de blob sont séparées. La mise à jour de la propriété **Access Tier Change Time** sur chaque blob dans un compte de stockage à chaque changement du niveau d’accès par défaut du compte aurait un coût prohibitif.

Quand un objet blob est remplacé au niveau d’accès chaud ou froid, le nouvel objet blob a le même niveau que l’ancien objet blob, sauf si le niveau d’accès du nouvel objet blob est explicitement défini au moment de la création. Lorsqu’un objet blob est stocké au niveau archive, il ne peut pas être remplacé ; le téléchargement du même objet blob n’est donc pas possible dans ce scénario.

> [!NOTE]
> Le stockage archive et la hiérarchisation au niveau de l’objet blob prennent en charge uniquement les objets blob de blocs.

### <a name="blob-lifecycle-management"></a>Gestion de cycle de vie des objets blob

La gestion du cycle de vie du stockage des objets blob offre une stratégie enrichie, basée sur des règles, que vous pouvez utiliser pour faire passer vos données au niveau d’accès le plus adapté et faire expirer les données à la fin de leur cycle de vie. Pour plus d’informations, consultez [Optimiser les coûts en automatisant les niveaux d’accès au Stockage Blob Azure](storage-lifecycle-management-concepts.md).

> [!NOTE]
> Pour le moment, les données stockées dans le niveau d’accès d’objets blob de blocs (performance Premium) ne peuvent pas être déplacées dans le niveau chaud, froid ou archive avec [Définir le niveau du blob](/rest/api/storageservices/set-blob-tier) ou à l’aide de la gestion du cycle de vie du Stockage Blob Azure.
> Pour déplacer les données, effectuez une copie synchrone du compte de stockage d’objets blob de blocs dans le niveau d’accès chaud d’un autre compte à l’aide de l’[API Put Block From URL](/rest/api/storageservices/put-block-from-url) ou d’une version d’AzCopy qui prend en charge cette API.
> L’API **Put Block From URL** copie les données sur le serveur de manière synchrone, ce qui signifie que l’appel est effectué uniquement quand toutes les données ont été déplacées de l’emplacement d’origine sur le serveur vers l’emplacement de destination.

### <a name="blob-level-tiering-billing"></a>Facturation de la hiérarchisation au niveau de l’objet blob

Lorsqu’un objet blob est chargé ou que son niveau est modifié, il est facturé au tarif correspondant au moment du chargement ou du changement de niveau.

Quand un objet blob est déplacé vers un niveau plus froid (chaud -> froid, chaud -> archive ou froid -> archive), l’opération est facturée comme une opération d’écriture dans le niveau de destination, où les taux du niveau de destination s’appliquent également aux opérations d’écriture (par 10 000) ainsi qu’à l’écriture des données (par Go).

Lorsqu’un objet blob est déplacé vers un niveau plus chaud (archive->froid, archive->chaud ou froid->chaud), l’opération est facturée comme une lecture à partir du niveau source, facturée aux tarifs des opérations de lecture (par 10 000) et d’extraction de données (par Go) du niveau source. Des frais de [suppression anticipée](#cool-and-archive-early-deletion) peuvent également s’appliquer pour tout objet blob déplacé hors du niveau froid ou archive. [La réactivation des données à partir du niveau archive](storage-blob-rehydration.md) prend du temps et les données sont facturées au prix du niveau archive jusqu’à leur restauration en ligne et la définition du niveau chaud ou froid pour le blob.

Le tableau suivant résume la facturation des changements de niveau.

| | **Tarif d’écriture (opération + accès)** | **Tarif de lecture (opération + accès)** |
| ---- | ----- | ----- |
| **Set Blob Tier** | chaud -> froid<br> chaud -> archive<br> froid -> archive | archive -> froid<br> archive -> chaud<br> froid -> chaud

### <a name="cool-and-archive-early-deletion"></a>Suppression anticipée dans les niveaux Froid et Archive

Les objets blob déplacés vers le niveau d’accès froid (comptes GPv2 uniquement) sont soumis à une période de suppression anticipée du niveau d’accès froid de 30 jours. Les objets blob déplacés vers le niveau archive sont soumis à une période de suppression anticipée de 180 jours. Ces charges sont calculées au prorata. Par exemple, si un objet blob est déplacé vers le niveau archive puis supprimé ou déplacé vers le niveau d’accès chaud après 45 jours, des frais de suppression anticipée équivalents à 135 (180 moins 45) jours de stockage de cet objet blob dans le niveau archive vous seront facturés.

Voici quelques informations concernant le déplacement entre les niveaux froid et archive :

1. S’il est déduit qu’un objet blob se trouve au niveau froid en fonction du niveau d’accès par défaut du compte de stockage et que l’objet blob est déplacé vers le niveau archive, il n’y a aucuns frais de suppression anticipée.
1. Si un objet blob est explicitement déplacé vers le niveau froid, puis déplacé vers le niveau archive, des frais de suppression anticipée s’appliquent.

Calculez l’heure de suppression anticipée en utilisant la propriété blob **Dernière modification**, s’il n’y a pas eu de changement de niveau d’accès. Sinon, utilisez l’heure de modification du niveau d’accès vers froid ou archive en affichant la propriété de l’objet blob : **access-tier-change-time**. Pour plus d’informations sur les propriétés de l’objet blob, consultez [Get Blob Properties](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparaison des options de stockage d’objets blob de blocs

Le tableau suivant présente une comparaison du stockage d’objets blob de blocs de performances premium, et les niveaux d’accès chaud, froid et archive.

|                                           | **Performances Premium**   | **Niveau chaud** | **Niveau froid**       | **Niveau archive**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilité**                          | 99,9 %                     | 99,9 %        | 99 %                 | Hors connexion           |
| **Disponibilité** <br> **(Lectures RA-GRS)**  | N/A                       | 99,99 %       | 99,9 %               | Hors connexion           |
| **Frais d’utilisation**                         | Coûts de stockage supérieurs, coût d’accès et de transaction inférieur | Coûts de stockage supérieurs, coûts d'accès et de transaction inférieurs | Coûts de stockage inférieurs, coûts d'accès et de transaction supérieurs | Coûts de stockage les plus faibles, coûts d'accès et de transaction les plus élevés |
| **Durée de stockage minimale**              | N/A                       | N/A          | 30 jours<sup>1</sup> | 180 jours
| **Latence** <br> **(Temps jusqu’au premier octet)** | Millisecondes à un chiffre | millisecondes | millisecondes        | heures<sup>2</sup> |

<sup>1</sup> Les objets du niveau froid sur des comptes GPv2 ont une durée de rétention minimale de 30 jours. Les comptes Stockage Blob n’ont pas de durée de rétention minimale pour le niveau d’accès froid.

<sup>2</sup> Stockage archive prend actuellement en charge deux priorités de réactivation, haute et standard, offrant différents coûts et latences d’extraction. Pour plus d’informations, consultez [Réalimenter les données d’objets blob à partir du niveau Archive](storage-blob-rehydration.md).

> [!NOTE]
> Les comptes Stockage Blob présentent les mêmes objectifs de performance et de scalabilité que les comptes de stockage à usage général v2. Pour plus d’informations, consultez [Objectifs de performance et de scalabilité pour Stockage Blob](scalability-targets.md).

## <a name="pricing-and-billing"></a>Tarification et facturation

Tous les comptes de stockage utilisent un modèle tarifaire pour le stockage d’objets blob de blocs basé sur le niveau de chaque objet blob. Gardez à l’esprit les conditions de facturation suivantes :

- **Coûts de stockage** : Les coûts de stockage des données varient en fonction de la quantité de données stockées et du niveau d’accès. Le coût par gigaoctet diminue à mesure que le niveau refroidit.
- **Coûts d’accès aux données** : les frais d’accès aux données augmentent à mesure que le niveau refroidit. Pour les données des niveaux d’accès froid et archive, des frais d’accès aux données en lecture vous sont facturés par gigaoctet.
- **Coûts de transaction** : il existe des frais par transaction pour tous les niveaux, augmentant à mesure que le niveau refroidit.
- **Coûts de transfert de données de géoréplication** : ces coûts s’appliquent uniquement aux comptes pour lesquels la géoréplication est configurée, notamment GRS et RA-GRS. Le transfert de données de géoréplication implique des frais par gigaoctet.
- **Coûts de transfert de données sortantes** : les transferts de données sortantes (données transférées hors d’une région Azure) sont facturés pour l’utilisation de la bande passante par gigaoctet. Cette facturation est cohérente avec les comptes de stockage à usage général.
- **Modification du niveau d’accès** : La modification du niveau d’accès du compte entraîne des frais de modification de niveau pour tous les objets blob ne possédant pas de niveau défini explicitement. Pour plus d’informations sur la modification du niveau d’accès pour un objet blob unique, reportez-vous à [Facturation de la hiérarchisation au niveau de l’objet blob](#blob-level-tiering-billing).

    Le changement du niveau d’accès pour un objet blob lorsque la gestion des versions des objets blob est activée, ou si l’objet blob a des instantanés, peut donner lieu à des frais supplémentaires. Pour plus d’informations sur les objets blob lorsque la gestion des versions est activée, consultez [Tarification et facturation](versioning-overview.md#pricing-and-billing) dans la documentation sur le contrôle de version des objets blob. Pour plus d’informations sur les objets blob avec instantanés, consultez [Tarification et facturation](snapshots-overview.md#pricing-and-billing) dans la documentation sur les instantanés d’objet blob.

> [!NOTE]
> Pour plus d’informations sur la tarification des objets blob de blocs, consultez [Tarification d’objet blob de blocs](https://azure.microsoft.com/pricing/details/storage/blobs/). Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails sur les tarifs de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="availability"></a>Disponibilité

Différents niveaux d’accès, ainsi que la hiérarchisation au niveau de l’objet blob sont disponibles dans certaines régions. Pour avoir une liste complète, voir [Régions Azure disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment gérer les objets blob et comptes dans les niveaux d’accès.

- [Comment gérer le niveau d’un objet blob dans un compte de stockage Azure](manage-access-tier.md)
- [Comment gérer le niveau d’accès par défaut d’un compte de stockage Azure](../common/manage-account-default-access-tier.md)
- [Optimiser les coûts en automatisant les niveaux d’accès au stockage Blob Azure](storage-lifecycle-management-concepts.md)
