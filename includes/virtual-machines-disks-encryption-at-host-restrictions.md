---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4435784592954e4df44d5ea8dc05b33a0023fd43
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313389"
---
- Ne prend pas en charge les disques Ultra.
- Ne peut pas être activé si Azure Disk Encryption (le chiffrement de machine virtuelle invité à l’aide de BitLocker/VM-decryption) est activé sur vos machines virtuelles ou groupes de machines virtuelles identiques.
- Azure Disk Encryption ne peut pas être activé sur les disques sur lesquels le chiffrement est activé sur l’hôte.
- Le chiffrement peut être activé sur un groupe de machines virtuelles identiques existant. Toutefois, seules les nouvelles machines virtuelles créées après l’activation du chiffrement sont automatiquement chiffrées.
- Les machines virtuelles existantes doivent être libérées et réallouées afin d’être chiffrées.
- Prend en charge les disques de système d’exploitation éphémères, mais uniquement avec les clés gérées par la plateforme.