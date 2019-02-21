---
title: Fichier Include
description: Fichier Include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331066"
---
Les objectifs d’extensibilité des comptes de stockage Premium sont les suivants :

| Capacité totale des comptes | Bande passante totale pour un compte de stockage localement redondant |
| --- | --- | 
| Capacité du disque : 35 To <br>Capacité d’instantané : 10 To | Jusqu’à 50 Go par seconde pour les données entrantes <sup>1</sup> et sortantes <sup>2</sup> |

<sup>1</sup> Toutes les données (demandes) envoyées à un compte de stockage

<sup>2</sup> Toutes les données (réponses) reçues d’un compte de stockage

Si vous utilisez des comptes de stockage Premium pour des disques non gérés et que votre application dépasse les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez envisager la migration vers des disques gérés. Si vous ne souhaitez pas migrer vers des disques gérés, générez votre application pour utiliser plusieurs comptes de stockage. Ensuite, partitionnez vos données sur ces comptes de stockage. Par exemple, si vous souhaitez attacher des disques de 51 To sur plusieurs machines virtuelles, répartissez-les entre deux comptes de stockage. La limite pour un compte de stockage Premium unique est de 35 To. Vérifiez qu’un compte de stockage Premium n’a jamais plus de 35 To de disques configurés.