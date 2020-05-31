---
title: Instantanés d’objet blob
titleSuffix: Azure Storage
description: Découvrez comment créer un instantané d’un blob en lecture seule pour sauvegarder des données blob à un moment donné.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 26646ad5ae42d2fa301a7f894084a78a64c73bd4
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884279"
---
# <a name="blob-snapshots"></a>Instantanés d’objet blob

Un instantané est une version en lecture seule d'un objet blob capturé à un instant donné.

> [!NOTE]
> Le contrôle de version des objets blob (préversion) offre un autre moyen de conserver des copies historiques d’un objet blob. Pour plus d’informations, consultez [Contrôle de version des objets blob (préversion)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>À propos des instantanés blob

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Un instantané d’un objet blob est identique à l’objet blob de base, à la différence que l’URI de l’objet blob a une valeur **DateTime** à la fin qui indique l’heure à laquelle l’instantané a été pris. Par exemple, si l’URI de l’objet blob de pages est `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l’URI de l’instantané est du type `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Tous les instantanés partagent l’URI de l’objet blob de base. La seule distinction entre l’objet blob de base et l’instantané est la valeur **DateTime** ajoutée à la fin.
>

Un objet blob peut avoir plusieurs instantanés. Les instantanés persistent jusqu’à ce qu’ils soient explicitement supprimés, ce qui signifie qu’un instantané ne peut pas survivre à son blob de base. Vous pouvez énumérer les instantanés associés à l’objet blob de base pour effectuer le suivi de vos instantanés actuels.

Lorsque vous créez un instantané d’un objet blob, les propriétés système de l’objet blob sont copiées dans l’instantané avec les mêmes valeurs. Les métadonnées de l’objet blob de base sont également copiées dans l’instantané, sauf si vous spécifiez des métadonnées distinctes pour l’instantané lorsque vous le créez. Une fois créé, un instantané peut être lu, copié ou supprimé, mais pas modifié.

Aucun bail associé à l’objet blob de base n’a d’incidence sur l’instantané. Vous ne pouvez pas acquérir de bail pour un instantané.

Un fichier de disque dur virtuel est utilisé pour stocker les informations et l’état actuels d’un disque de machine virtuelle. Vous pouvez détacher un disque de la machine virtuelle ou arrêter la machine virtuelle, puis prendre une capture instantanée de son fichier de disque dur virtuel. Vous pourrez par la suite utiliser ce fichier de capture instantanée pour récupérer le fichier de disque dur virtuel à ce moment précis et recréer la machine virtuelle.

## <a name="understand-how-snapshots-accrue-charges"></a>Présentation des frais liés aux instantanés

La création d’un instantané, c’est-à-dire d’une copie en lecture seule d’un objet blob, peut entraîner des frais de stockage de données supplémentaires pour votre compte. Lors de la conception de votre application, il est important de savoir comment ces frais peuvent s’accumuler pour pouvoir réduire les coûts.

### <a name="important-billing-considerations"></a>Considérations importantes relatives à la facturation

La liste suivante contient des points clés à prendre en compte lors de la création d’un instantané.

- Des frais s’appliquent à votre compte de stockage pour des pages ou des blocs uniques, qu’ils soient dans l’objet blob ou dans l’instantané. Aucuns frais supplémentaires ne sont imputés à votre compte pour les instantanés associés à un objet blob tant que vous n'avez pas mis à jour l’objet blob sur lequel ils sont basés. Une fois que vous avez mis à jour l’objet blob de base, il diffère de ses instantanés. Dans ce cas, vous êtes facturé pour les blocs ou pages uniques de chaque objet blob ou instantané.
- Quand vous remplacez un bloc au sein d’un objet blob de bloc, ce bloc est ensuite facturé comme un bloc unique. Cela est vrai même si le bloc a le même ID de bloc et les mêmes données que dans l'instantané. Une fois le bloc revalidé, il diffère de son homologue dans tous les instantanés et vous serez facturé pour ses données. Il en va de même pour une page dans un objet blob de pages qui est mise à jour avec des données identiques.
- Le remplacement d'un objet blob de blocs en appelant la méthode [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] ou [UploadFromByteArray][dotnet_UploadFromByteArray] remplace tous les blocs de l’objet blob. Si vous avez une capture instantanée associée à cet objet blob, tous les blocs dans l’objet blob de base et la capture instantanée seront désormais différents et vous serez facturé pour tous les blocs des deux objets blob. Cela est vrai même si les données de l’objet blob de base et l’instantané restent identiques.
- Le service blob Azure ne dispose d'aucun moyen pour déterminer si deux blocs contiennent des données identiques. Chaque bloc qui est téléchargé et validé est traité comme étant unique, même s’il a les mêmes données et le même ID de bloc. Comme des frais sont applicables aux blocs uniques, il est important de savoir que la mise à jour d’un objet blob qui a une capture instantanée entraîne la création de blocs uniques supplémentaires et donc des frais supplémentaires.

### <a name="minimize-cost-with-snapshot-management"></a>Réduire les coûts grâce à la gestion des captures instantanées

Nous vous recommandons de gérer vos captures instantanées avec soin pour éviter des frais supplémentaires. Vous pouvez suivre ces meilleures pratiques pour réduire les coûts liés au stockage de vos captures instantanées :

- Supprimez et recréez les instantanés associés à un objet blob chaque fois que vous mettez à jour l'objet blob, même si vous le mettez à jour avec des données identiques, sauf si la conception de votre application exige de tenir à jour des instantanés. En supprimant et en recréant les captures instantanées de l’objet blob, vous pouvez vous assurer que l’objet blob et les captures instantanées ne diffèrent pas.
- Si vous tenez à jour les captures instantanées d'un objet blob, évitez d’appeler [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] ou [UploadFromByteArray][dotnet_UploadFromByteArray] pour mettre à jour l’objet blob. Ces méthodes remplacent tous les blocs dans l’objet blob. Ainsi, votre objet blob de base et vos captures instantanées diffèrent de façon significative. Mettez plutôt à jour le moins de blocs possible en utilisant les méthodes [PutBlock][dotnet_PutBlock] et [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scénarios de facturation d’instantanés

Les scénarios suivants illustrent l’accumulation des coûts pour un objet blob de blocs et ses instantanés.

#### <a name="scenario-1"></a>Scénario 1

Dans le scénario 1, l'objet blob de base n'a pas été mis à jour depuis la capture de l'instantané. Des frais sont donc applicables uniquement pour les blocs uniques 1, 2 et 3.

![Ressources Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénario 2

Dans le scénario 2, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été mis à jour, et bien qu’il contienne les mêmes données et le même ID, il est différent du bloc 3 de l’instantané. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénario 3

Dans le scénario 3, l'objet blob de base a été mis à jour, mais pas l'instantané. Le bloc 3 a été remplacé par le bloc 4 dans l’objet blob de base, mais l’instantané reflète toujours le bloc 3. Par conséquent, des frais pour quatre blocs sont facturés au compte.

![Ressources Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénario 4

Dans le scénario 4, l'objet blob de base a été complètement mis à jour et ne contient aucun de ses blocs d'origine. Par conséquent, des frais pour les huit blocs uniques sont facturés au compte. Ce scénario peut se produire si vous utilisez une méthode de mise à jour telle que [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] ou [UploadFromByteArray][dotnet_UploadFromByteArray], car ces méthodes remplacent tout le contenu d’un objet blob.

![Ressources Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer un instantané blob dans .NET](snapshots-manage-dotnet.md)
- [Sauvegarder les disques de machines virtuelles Azure non gérés avec des captures instantanées incrémentielles](../../virtual-machines/windows/incremental-snapshots.md)
