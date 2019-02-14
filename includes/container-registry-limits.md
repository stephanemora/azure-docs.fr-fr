---
title: Fichier Include
description: Fichier Include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: fe1227d91c0d039a94e5b9a3046b879f4f17355a
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246851"
---
| Ressource | De base | standard | Premium |
|---|---|---|---|---|
| Stockage<sup>1</sup> | 10 Go | 100 Go| 500 Go |
| Taille maximale du calque d’image | 20 Gio | 20 Gio | 50 GiB |
| ReadOps par minute<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps par minute<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Bande passante de téléchargement en Mbits/s<sup>2</sup> | 30 | 60 | 100 |
| Bande passante de chargement en Mbits/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Géoréplication | N/A | N/A | [Pris en charge][geo-replication] |
| Approbation du contenu (préversion) | N/A | N/A | [Pris en charge][content-trust] |

<sup>1</sup> Les limites de stockage spécifiées correspondent à la quantité de stockage *inclus* pour chaque niveau. Vous êtes facturé un tarif journalier supplémentaire pour chaque Gio de stockage d’images utilisé au-dessus de ces limites. Pour obtenir des informations tarifaires, consultez [Tarifs de Container Registry][pricing].

<sup>2</sup> Les valeurs *ReadOps*, *WriteOps* et *Bandwidth* sont des estimations minimales. ACR s’efforce d’améliorer les performances en fonction de l’utilisation requise.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduit par plusieurs opérations de lecture en fonction du nombre de couches dans l’image, plus la récupération du manifeste.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduit par plusieurs opérations d’écriture, en fonction du nombre de couches qui doivent être envoyées. Un `docker push` inclut des *ReadOps* pour récupérer un manifeste pour une image existante.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
