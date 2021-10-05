---
title: Vue d’ensemble de la réactivation des objets blob à partir du niveau archive
description: Lorsqu’un objet blob se trouve dans le niveau d’accès archive, il est considéré comme hors connexion et ne peut être ni lu ni modifié. Pour pouvoir lire ou modifier des données dans un objet blob archivé, vous devez d’abord le réactiver dans un niveau en ligne, à savoir le niveau chaud ou froid.
services: storage
author: tamram
ms.author: tamram
ms.date: 08/31/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 2c4eac524ecda8a2b90036748fd2a6f2a389a3cd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823736"
---
# <a name="overview-of-blob-rehydration-from-the-archive-tier"></a>Vue d’ensemble de la réactivation des objets blob à partir du niveau archive

Lorsqu’un objet blob se trouve dans le niveau d’accès archive, il est considéré comme hors connexion et ne peut être ni lu ni modifié. Pour pouvoir lire ou modifier des données dans un objet blob archivé, vous devez d’abord le réactiver dans un niveau en ligne, à savoir le niveau chaud ou froid. Il existe deux options possibles pour réactiver un objet blob stocké au niveau archive :

- [Copier un objet blob archivé dans un niveau en ligne](#copy-an-archived-blob-to-an-online-tier) : vous pouvez réactiver un objet blob archivé en le copiant dans un nouvel objet blob au niveau chaud ou froid avec l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) ou [Copier l’objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url). Microsoft recommande cette option pour la plupart des scénarios.

- [Remplacer le niveau d’accès d’un objet blob par un niveau en ligne](#change-a-blobs-access-tier-to-an-online-tier) : vous pouvez réactiver un objet blob archivé au niveau chaud ou froid en modifiant son niveau avec l’opération [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier).

La réactivation d’un objet blob à partir du niveau archive peut prendre plusieurs heures. Microsoft recommande de réactiver les objets blob volumineux pour des performances optimales. La réactivation simultanée de plusieurs petits objets blob est susceptible de prendre plus de temps.

Vous pouvez configurer [Azure Event Grid](../../event-grid/overview.md) de façon à déclencher un événement en cas de réactivation d’un objet blob du niveau archive vers un niveau en ligne et à envoyer cet événement à un gestionnaire d’événements. Pour plus d’informations, consultez [Gestion d’un événement déclenché lors de la réactivation d’objets blob](#handle-an-event-on-blob-rehydration).

Pour plus d’informations sur les niveaux d’accès du Stockage Azure, consultez [Niveaux d’accès du Stockage Blob Azure : chaud, froid et archive](storage-blob-storage-tiers.md).

## <a name="rehydration-priority"></a>Priorité de réactivation

Lorsque vous réactivez un objet blob, vous pouvez définir la priorité de l’opération de réactivation avec l’en-tête *x-ms-rehydrate-priority* facultatif sur une opération [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier) ou [Copier l’objet blob](/rest/api/storageservices/copy-blob)/[Copier l’objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url). Les options de priorité de réactivation sont les suivantes :

- **Priorité standard** : La requête de réalimentation est traitée dans l’ordre dans lequel elle a été reçue et peut prendre jusqu’à 15 heures.
- **Priorité élevée** : la demande de réactivation est prioritaire par rapport aux demandes de priorité standard. Elle peut être effectuée en moins d’une heure pour les objets de taille inférieure à 10 Go.

Pour connaître la priorité de réactivation alors que l’opération de réactivation est en cours, appelez [Obtenir les propriétés de l’objet blob](/rest/api/storageservices/get-blob-properties) afin de retourner la valeur de l’en-tête `x-ms-rehydrate-priority`. La propriété de priorité de réactivation renvoie *Standard* ou *Élevée*.

La priorité standard constitue l’option de réactivation par défaut. Une réactivation de priorité élevée est plus rapide, mais aussi plus coûteuse qu’une réactivation de priorité standard. Elle peut prendre plus d’une heure en fonction de la taille de l’objet blob et de la demande actuelle. Microsoft recommande de réserver la réactivation de priorité élevée aux situations de restauration de données d’urgence.

Pour plus d’informations sur les différences tarifaires entre les demandes de réactivation de priorité standard et de priorité élevée, consultez [Tarification du Stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copier un objet blob archivé dans un niveau en ligne

La première option pour déplacer un objet blob du niveau archive vers un niveau en ligne consiste à copier l’objet blob archivé dans un nouvel objet blob de destination qui se trouve dans le niveau chaud ou froid. Vous pouvez utiliser l’opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) ou l’opération [Copier l’objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url) pour copier l’objet blob. Lorsque vous copiez un objet blob archivé dans un nouvel objet blob d’un niveau en ligne, l’objet blob source reste inchangé dans le niveau archive.

Vous devez copier l’objet blob archivé dans un nouvel objet blob sous un autre nom ou dans un autre conteneur. Il n’est pas possible de remplacer l’objet blob source en effectuant la copie dans le même objet blob.

Microsoft recommande d’effectuer une opération de copie dans la plupart des scénarios impliquant de déplacer un objet blob du niveau archive vers un niveau en ligne, pour les raisons suivantes :

- Une opération de copie évite les frais de suppression anticipée qui sont évalués si un objet blob passe du niveau archive à un autre niveau avant que la période de 180 jours ne soit écoulée. Pour plus d’informations, voir [Niveau d’accès archive](storage-blob-storage-tiers.md#archive-access-tier).
- Si une stratégie de gestion du cycle de vie est en vigueur pour le compte de stockage, le fait de réactiver un objet blob avec [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier) peut donner lieu à un scénario dans lequel la stratégie de cycle de vie déplace l’objet blob vers le niveau archive après la réactivation, car l’heure de dernière modification dépasse le seuil défini pour la stratégie. Une opération de copie laisse l’objet blob source dans le niveau archive et crée un nouvel objet blob avec un autre nom et une nouvelle heure de dernière modification. Il n’y a donc aucun risque que l’objet blob réactivé ne soit renvoyé au niveau archive par la stratégie de cycle de vie.

La copie d’un blob à partir du niveau archive peut prendre plusieurs heures, en fonction de la priorité de réactivation sélectionnée. En arrière-plan, l’opération Copier l’objet blob lit l’objet blob source archivé pour créer un nouvel objet blob en ligne dans le niveau de destination sélectionné. Il peut arriver que le nouvel objet blob soit visible quand vous répertoriez les objets blob dans le conteneur parent avant la fin de l’opération de réactivation. Cependant, son niveau est défini sur archive. Les données ne sont pas disponibles tant que l’opération de lecture sur l’objet blob source du niveau archive n’est pas terminée et que le contenu de l’objet blob n’est pas écrit dans le nouvel objet blob de destination d’un niveau en ligne. Le nouvel objet blob étant une copie indépendante, ni sa modification ni sa suppression n’ont d’incidence sur l’objet blob source du niveau archive.

Pour savoir comment réactiver un objet blob en le copiant dans un niveau en ligne, consultez [Réactivation d’un objet blob avec une opération de copie](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation).

> [!IMPORTANT]
> Ne supprimez pas l’objet blob source avant la fin de la réactivation. La copie de l’objet blob de destination risquerait de ne pas aboutir. Vous pouvez gérer l’événement qui se déclenche lorsque l’opération de copie est terminée pour savoir quand supprimer l’objet blob source en toute sécurité. Pour plus d’informations, consultez [Gestion d’un événement déclenché lors de la réactivation d’objets blob](#handle-an-event-on-blob-rehydration).

La copie d’un objet blob archivé dans un niveau de destination en ligne n’est prise en charge qu’au sein du même compte de stockage. Il n’est pas possible de copier un objet blob archivé dans un objet blob de destination qui se trouve également dans le niveau archive.

Le tableau suivant indique le comportement d’une opération de copie d’objet blob, en fonction du niveau de l’objet blob source et de l’objet blob de destination.

|  | **Source de niveau chaud** | **Source de niveau froid** | **Source de niveau d’archive** |
|--|--|--|--|
| **Destination de niveau chaud** | Prise en charge | Prise en charge | Pris en charge au sein du même compte. Réactivation de l’objet blob nécessaire. |
| **Destination de niveau froid** | Prise en charge | Prise en charge | Pris en charge au sein du même compte. Réactivation de l’objet blob nécessaire. |
| **Destination du niveau d’archive** | Prise en charge | Prise en charge | Non pris en charge |

## <a name="change-a-blobs-access-tier-to-an-online-tier"></a>Remplacement du niveau d’accès d’un objet blob par un niveau en ligne

La deuxième option pour réactiver un objet blob du niveau archive à un niveau en ligne consiste à modifier son niveau en appelant [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier). Cette opération permet de remplacer le niveau de l’objet blob archivé par le niveau chaud ou froid.

Une demande [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier) ne peut pas être annulée une fois lancée. Pendant l’opération de réactivation, le paramètre de niveau d’accès de l’objet blob continue à s’afficher comme archivé jusqu’à ce que le processus de réactivation soit terminé. À la fin de l’opération, la propriété de niveau d’accès est mise à jour pour refléter le nouveau niveau.

Pour savoir comment réactiver un objet blob en remplaçant son niveau par un niveau en ligne, consultez [Réactivation d’un objet blob en modifiant son niveau](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier).

> [!CAUTION]
> La modification du niveau d’un objet blob n’a pas d’incidence sur son heure de dernière modification. Si une stratégie de [gestion du cycle de vie](./lifecycle-management-overview.md) est en vigueur pour le compte de stockage, le fait de réactiver un objet blob avec **Définir le niveau de l’objet blob** peut donner lieu à un scénario dans lequel la stratégie de cycle de vie déplace l’objet blob vers le niveau archive après la réactivation, car l’heure de dernière modification dépasse le seuil défini pour la stratégie.
>
> Pour éviter ce scénario, réactivez l’objet blob archivé en le copiant (cf. section [Copie d’un objet blob archivé dans un niveau en ligne](#copy-an-archived-blob-to-an-online-tier)). L’opération de copie crée une nouvelle instance de l’objet blob avec une heure de dernière modification mise à jour. Elle ne déclenche pas la stratégie de gestion du cycle de vie.

## <a name="check-the-status-of-a-blob-rehydration-operation"></a>Vérification de l’état d’une opération de réactivation d’un objet blob

Pendant l’opération de réactivation d’un objet blob, vous pouvez appeler l’opération [Obtenir les propriétés de l’objet blob](/rest/api/storageservices/get-blob-properties) pour connaître son état. Pour savoir comment vérifier l’état d’une opération de réactivation, consultez [Vérification de l’état d’une opération de réactivation](archive-rehydrate-to-online-tier.md#check-the-status-of-a-rehydration-operation).

## <a name="handle-an-event-on-blob-rehydration"></a>Gérer un événement lors de la réhydratation d’objets blob

La réactivation d’un objet blob archivé peut prendre jusqu’à 15 heures. Il est par ailleurs inefficace d’interroger de manière répétée **Obtenir les propriétés de l’objet blob** pour déterminer si la réactivation est terminée. Le recours à [Azure Event Grid](../../event-grid/overview.md) pour capturer l’événement qui se déclenche à l’issue de la réactivation offre de meilleures performances et une optimisation des coûts.

Azure Event Grid déclenche l’un des deux événements suivants lors de la réactivation d’objets blob, en fonction de l’opération utilisée :

- L’événement **Microsoft.Storage.BlobCreated** se déclenche lors de la création d’un objet blob. Dans le contexte de la réactivation des objets blob, il est mis en œuvre lorsqu’une opération [Copier l’objet blob](/rest/api/storageservices/copy-blob) ou [Copier l’objet blob à partir d’une URL](/rest/api/storageservices/copy-blob-from-url) crée un objet blob de destination dans le niveau chaud ou froid et que les données de l’objet blob sont entièrement réactivées à partir du niveau archive.
- L’événement **Microsoft.Storage.BlobTierChanged** se déclenche lors de la modification du niveau d’un objet blob. Dans le contexte de la réactivation des objets blob, il est mis en œuvre lorsqu’une opération [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier) remplace le niveau d’un objet blob archivé par le niveau chaud ou froid.

Pour savoir comment capturer un événement déclenché en cas de réactivation et l’envoyer à un gestionnaire d’événements Azure Functions, consultez [Exécution d’une fonction Azure Functions en réponse à un événement de réactivation d’objets blob](archive-rehydrate-handle-event.md).

Pour plus d’informations sur la gestion des événements dans le Stockage blob, consultez [Réaction aux événements du Stockage Blob Azure](storage-blob-event-overview.md) et [Stockage Blob Azure en tant que source Event Grid](../../event-grid/event-schema-blob-storage.md).

## <a name="pricing-and-billing"></a>Tarification et facturation

Une opération de réactivation effectuée avec [Définir le niveau de l’objet blob](/rest/api/storageservices/set-blob-tier) est facturée selon les transactions de lecture de données et la taille de l’extraction de données. Une réactivation de priorité élevée présente des coûts d’opération et d’extraction de données plus élevés qu’une réactivation de priorité standard. La réalimentation à haute priorité apparaît sous la forme d’un élément de ligne distinct sur votre facture. Si une demande de priorité élevée visant à retourner un blob archivé de quelques gigaoctets prend plus de cinq heures, le tarif de récupération haute priorité n’est pas appliqué. Toutefois, les tarifs de récupération standard s’appliquent toujours.

La copie d’un objet blob archivé sur un niveau en ligne avec [Copier l’objet blob](/rest/api/storageservices/copy-blob) ou [Copier l’objet à partir d’une URL](/rest/api/storageservices/copy-blob-from-url) est facturée selon les transactions de lecture de données et la taille de l’extraction de données. La création de l’objet blob de destination dans un niveau en ligne est facturée selon les transactions d’écriture de données. Aucun frais de suppression précoce n’est appliqué lorsque vous copiez vers un objet blob en ligne car l’objet blob source reste inchangé dans le niveau archive. Les frais d’extraction de priorité élevée s’appliquent si cette option est sélectionnée.

Les objets blob dans le niveau Archive doivent être stockés pendant un minimum de 180 jours. La suppression et la modification du niveau d’un objet blob archivé avant l’expiration de la période de 180 jours entraînent des frais de suppression anticipée. Pour plus d’informations, voir [Niveau d’accès archive](storage-blob-storage-tiers.md#archive-access-tier).

Pour plus d’informations sur la tarification de la réalimentation des données et des objets blob de blocs, consultez [Présentation de la tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Pour plus d’informations sur les frais de transfert de données sortantes, consultez la page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="see-also"></a>Voir aussi

- [Stockage Blob Azure : niveaux d’accès chaud, froid et archive](storage-blob-storage-tiers.md)
- [Réalimenter un objet blob archivé dans un niveau en ligne](archive-rehydrate-to-online-tier.md)
- [Exécution d’une fonction Azure en réponse à un événement de réactivation d’objets blob](archive-rehydrate-handle-event.md)
- [Réaction aux événements de stockage Blob](storage-blob-event-overview.md)