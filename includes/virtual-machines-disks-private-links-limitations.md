---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376703"
---
- Un seul réseau virtuel peut être lié à un objet d’accès au disque.
- Pour que vous puissiez les lier, votre réseau virtuel et votre objet d’accès au disque doivent être dans le même abonnement.
- Jusqu’à 10 disques ou instantanés peuvent être importés ou exportés simultanément avec le même objet d’accès au disque.
- Vous ne pouvez pas demander une approbation manuelle pour lier un réseau virtuel à un objet d’accès au disque.
- Il est impossible d’exporter des instantanés incrémentiels lorsqu’ils sont associés à un objet d’accès au disque.
- Vous ne pouvez pas utiliser AzCopy pour télécharger le disque dur virtuel d’un disque ou d’un instantané sécurisé via des liens privés vers un compte de stockage. Vous pouvez cependant utiliser AzCopy pour télécharger un disque dur virtuel sur vos machines virtuelles.
