---
title: Considérations pour le provisionnement de partages de fichiers Azure
description: Provisionnez des partages de fichiers Azure en vue d’une utilisation avec Azure File Sync. Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209280"
---
Un partage de fichiers Azure est stocké dans le cloud dans un compte de stockage Azure.
Les performances doivent être considérées à un autre niveau ici.

Si vous avez des partages très actifs (utilisés par de nombreux utilisateurs et/ou applications), la limite de performances d’un compte de stockage peut être atteinte avec deux partages de fichiers Azure.

Une bonne pratique consiste à déployer les comptes de stockage avec un partage de fichiers pour chaque.
Vous pouvez regrouper plusieurs partages de fichiers Azure dans le même compte de stockage s’ils sont destinés à l’archivage ou si vous pensez qu’ils feront l’objet d’une activité quotidienne réduite.

Ces considérations s’appliquent davantage à l’accès direct au cloud (par le biais d’une machine virtuelle Azure) qu’à Azure File Sync. Si vous envisagez d’utiliser Azure File Sync sur ces partages uniquement, le regroupement de plusieurs partages dans le même compte de stockage Azure est une bonne idée.

Si vous avez établi la liste de vos partages, vous devez mapper chaque partage au compte de stockage dans lequel il résidera.

Durant la phase précédente, vous avez déterminé le nombre de partages approprié. Au cours de cette étape, vous avez créé un mappage des comptes de stockage aux partages de fichiers. Déployez le nombre de comptes de stockage Azure (à présent approprié) avec le nombre approprié de partages de fichiers Azure pour chaque.

Vérifiez que la région est la même pour tous vos comptes de stockage et qu’elle correspond à la région de la ressource de service de synchronisation de stockage que vous avez déjà déployée.

> [!CAUTION]
> Si vous créez un partage de fichiers Azure limité à 100 Tio, il peut utiliser deux options de redondance seulement : un stockage localement redondant ou un stockage redondant interzone. Tenez compte de vos besoins en termes de redondance du stockage avant d’utiliser des partages de fichiers de 100 Tio.

Les partages de fichiers Azure sont toujours créés avec une limite de 5 Tio par défaut. Étant donné que vous créez de nouveaux comptes de stockage, veillez à suivre les [instructions sur la création de comptes de stockage autorisant des partages de fichiers Azure limités à 100 Tio](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Quand vous déployez un compte de stockage, vous devez également tenir compte de la redondance de votre stockage Azure. Consultez l'article : [Options de redondance de stockage Microsoft Azure](../articles/storage/common/storage-redundancy.md).

Le nom des ressources est également important. Par exemple, si vous regroupez plusieurs partages pour le service RH dans un compte de stockage Azure, vous devez nommer le compte de stockage de manière appropriée. De même, quand vous nommez vos partages de fichiers Azure, vous devez utiliser des noms similaires à ceux de leurs équivalents locaux.