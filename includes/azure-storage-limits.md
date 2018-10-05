---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396121"
---
Le tableau suivant décrit les limites par défaut du Stockage Azure. La limite *d’entrée* désigne toutes les données (demandes) envoyées à un compte de stockage. La limite de *sortie* désigne toutes les données (réponses) reçues d’un compte de stockage.

| Ressource | Limite par défaut |
| --- | --- |
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes standard et premium | 200 |
| Capacité maximale du compte de stockage<sup>1</sup> | 500 Tio |
| Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage | Aucune limite |
| Taux de demande maximal<sup>1</sup> par compte de stockage | 20 000 demandes par seconde |
| Entrée max.<sup>1</sup> par compte de stockage (régions des États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 20 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Sortie max.<sup>1</sup> par compte de stockage (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS |
| Max. d’entrées<sup>1</sup> par compte de stockage (régions hors États-Unis) | 5 Gbit/s si RA-GRS/GRS est activé, 10 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Max. de sorties<sup>1</sup> par compte de stockage (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS |

<sup>1</sup> Les comptes de stockage Azure prennent en charge des limites plus élevées pour la capacité, le taux de demande, l’entrée et la sortie par demande. Pour plus d’informations sur l’augmentation des limites, consultez [Annonce de comptes de stockage plus grands et à plus grande échelle](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). Pour demander une augmentation des limites de compte, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>Les options de [réplication du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) sont notamment :
* **RA-GRS**: stockage géo-redondant avec accès en lecture. Si RA-GRS est activé, les cibles de sortie pour l’emplacement secondaire sont identiques à celles de l’emplacement principal.
* **GRS**: stockage géo-redondant. 
* **ZRS**: stockage redondant dans une zone.
* **LRS**: stockage localement redondant. 

