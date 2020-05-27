---
title: Fichier include
description: Fichier include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 19d0be6a032868c6683cd5b6bbfa7f07306171fb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683411"
---
| Ressource | De base | Standard | Premium |
|---|---|---|---|
| Stockage<sup>1</sup> | 10 Gio | 100 Gio| 500 Gio |
| Taille maximale du calque d’image | 200 Gio | 200 Gio | 200 Gio |
| ReadOps par minute<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps par minute<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Bande passante de téléchargement en Mbits/s<sup>2</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Géoréplication | N/A | N/A | [Pris en charge][geo-replication] |
| Approbation du contenu | N/A | N/A | [Pris en charge][content-trust] |
| Liaison privée avec points de terminaison privés | N/A | N/A | [Pris en charge][plink] |
| Accès au réseau virtuel du point de terminaison de service | N/A | N/A | [Préversion][vnet] |
| Clés gérées par le client | N/A | N/A | [Pris en charge][cmk] |
| Autorisations délimitées par le référentiel | N/A | N/A | [Préversion][token]|
| &bull; Jetons | N/A | N/A | 20 000 |
| &bull; Mappages d’étendue | N/A | N/A | 20 000 |
| &bull; Référentiels par mappage d’étendue | N/A | N/A | 500 |


<sup>1</sup>Les limites de stockage spécifiées correspondent à la quantité de stockage *inclus* pour chaque niveau. Un tarif journalier supplémentaire vous est facturé pour chaque Gio de stockage d’images utilisé au-dessus de ces limites. Pour obtenir des informations sur les tarifs, consultez [Tarification d’Azure Container Registry][pricing].

<sup>2</sup>Les valeurs *ReadOps*, *WriteOps* et *Bandwidth* sont des estimations minimales. Azure Container Registry s’efforce d’améliorer les performances en fonction de l’utilisation requise.

<sup>3</sup>[docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, plus la récupération du manifeste.

<sup>4</sup>[docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md