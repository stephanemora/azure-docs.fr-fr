---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372711"
---
### <a name="premium-performance-block-blob-storage"></a>Stockage blob par blocs performances Premium

Un compte stockage blob de bloc de performances premium est optimisé pour les applications qui utilisent plus petits en kilo-octets, plage d’objets. Elle est idéale pour les applications nécessitant des taux de transaction très élevée ou faible latence constante du stockage. Stockage blob par blocs performances Premium est conçu pour évoluer avec vos applications. Si vous envisagez de déployer des applications qui nécessitent des centaines de milliers de demandes par seconde ou de plusieurs pétaoctets de capacité de stockage, veuillez nous contacter en envoyant une demande de prise en charge dans les [Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Stockage blob de page de performances Premium

Performances Premium, comptes de stockage à usage général v1 ou v2 ont les objectifs d’évolutivité suivants :

| Capacité totale des comptes                            | Bande passante totale pour un compte de stockage localement redondant                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacité du disque : 35 To <br>Capacité d’instantané : 10 To | Jusqu’à 50 Go par seconde pour les données entrantes <sup>1</sup> et sortantes <sup>2</sup> |

<sup>1</sup> Toutes les données (demandes) envoyées à un compte de stockage

<sup>2</sup> Toutes les données (réponses) reçues d’un compte de stockage

Si vous utilisez des comptes de stockage de performances premium pour disques non gérés et que votre application dépasse les objectifs d’évolutivité d’un compte de stockage unique, vous pouvez choisir de migrer vers des disques gérés. Si vous ne souhaitez pas migrer vers des disques gérés, générez votre application pour utiliser plusieurs comptes de stockage. Ensuite, partitionnez vos données sur ces comptes de stockage. Par exemple, si vous souhaitez attacher des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage. La limite pour un compte de stockage Premium unique est de 35 To. Assurez-vous qu’un compte de stockage de performances premium n’a jamais plus de 35 To de disques alloués.