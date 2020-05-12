---
title: Répertorier, mettre à jour et supprimer des ressources d’image à l’aide d’Azure CLI
description: Répertoriez, mettez à jour et supprimez des ressources d’image dans votre galerie d’images partagées à l’aide d’Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 4126b7e0b48b455d9e5fcf65d6707f7e04de13ef
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793993"
---
# <a name="list-update-and-delete-image-resources"></a>Répertorier, mettre à jour et supprimer des ressources d’image 

Vous pouvez gérer vos ressources de galerie d’images partagées à l’aide d’Azure CLI.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Étapes suivantes

[Générateur d’image Azure (préversion)](./linux/image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](./linux/image-builder-gallery-update-image-version.md). 