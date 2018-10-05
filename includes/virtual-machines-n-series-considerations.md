---
title: Fichier Include
description: Fichier Include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1a9c4dc5a4d21f8837bde171283cd8a070297674
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043850"
---
## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

* Pour connaître la disponibilité des machines virtuelles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

* Les machines virtuelles de série N ne peuvent être déployées que dans le modèle de déploiement Resource Manager.

* Les machines virtuelles de série N diffèrent en fonction du type de stockage Azure qu’elles prennent en charge pour les disques. Les machines virtuelles NC et NV ne prennent en charge que les disques de machines virtuelles sauvegardés par le Stockage sur disque Standard (HDD). Les machines virtuelles NCv2, NCv3, ND et NVv2 prennent en charge uniquement les disques de machine virtuelle s’appuyant sur un stockage sur disque (SSD) Premium.

* Si vous voulez déployer plus de quelques machines virtuelles de série N, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

* Vous devez peut-être augmenter le quota de cœurs (par région) dans votre abonnement Azure ainsi que le quota de chaque cœur NC, NCv2, NCv3, ND, NV ou NVv2. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../articles/azure-supportability/how-to-create-azure-support-request.md) gratuitement. Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.




