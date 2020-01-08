---
title: Fichier Include
description: Fichier Include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392393"
---
| Ressource | De base | standard | Premium |
|---|---|---|---|
| Stockage<sup>1</sup> | 10 Go | 100 Gio| 500 Go |
| Taille maximale du calque d’image | 200 Gio | 200 Gio | 200 Gio |
| ReadOps par minute<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps par minute<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Bande passante de téléchargement en Mbits/s<sup>2</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>2</sup> | 10 | 20 | 50 |
| webhooks | 2 | 10 | 500 |
| Géoréplication | N/A | N/A | [Pris en charge][geo-replication] |
| Approbation du contenu | N/A | N/A | [Pris en charge][content-trust] |
| Accès au réseau virtuel | N/A | N/A | [Préversion][vnet] |
| Autorisations délimitées par le référentiel | N/A | N/A | [Préversion][token]|
| &bull; jetons | N/A | N/A | 20 000 |
| &bull; mappages d’étendue | N/A | N/A | 20 000 |
| &bull; référentiels par mappage d’étendue | N/A | N/A | 500 |


<sup>1</sup>Les limites de stockage spécifiées correspondent à la quantité de stockage *inclus* pour chaque niveau. Vous êtes facturé un tarif journalier supplémentaire pour chaque Gio de stockage d’images utilisé au-dessus de ces limites. Pour obtenir des informations tarifaires, consultez [Tarification d’Azure Container Registry][pricing].

<sup>2</sup>Les valeurs *ReadOps*, *WriteOps* et *Bandwidth* sont des estimations minimales. Azure Container Registry s’efforce d’améliorer les performances en fonction de l’utilisation requise.

<sup>3</sup>[docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, plus la récupération du manifeste.

<sup>4</sup>[docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md