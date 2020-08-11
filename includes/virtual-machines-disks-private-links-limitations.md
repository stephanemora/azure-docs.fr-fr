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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420281"
---
- Un seul réseau virtuel peut être lié à un objet d’accès au disque.
- Pour que vous puissiez les lier, votre réseau virtuel et votre objet d’accès au disque doivent être dans le même abonnement.
- Jusqu’à 10 disques ou instantanés peuvent être importés ou exportés simultanément avec le même objet d’accès au disque.
- Vous ne pouvez pas demander une approbation manuelle pour lier un réseau virtuel à un objet d’accès au disque.
- La fonctionnalité différentielle n’est pas prise en charge pour les instantanés incrémentiels associés à un objet d’accès au disque.
- Vous ne pouvez pas utiliser AzCopy pour télécharger le disque dur virtuel d’un disque ou d’un instantané sécurisé via des liens privés vers un compte de stockage. Vous pouvez cependant utiliser AzCopy pour télécharger un disque dur virtuel sur vos machines virtuelles.
