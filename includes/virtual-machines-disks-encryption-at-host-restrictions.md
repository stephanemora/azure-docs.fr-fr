---
title: Fichier Include
description: inclure fichier
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/22/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 528b3243a7c7d8ca1f96d9b4a311393820e19685
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802982"
---
- Ne prend pas en charge les disques Ultra.
- Ne peut pas être activé si Azure Disk Encryption (le chiffrement de machine virtuelle invité à l’aide de bitlocker/DM-Crypt) est activé sur vos machines virtuelles ou groupes de machines virtuelles identiques.
- Azure Disk Encryption ne peut pas être activé sur les disques sur lesquels le chiffrement est activé sur l’hôte.
- Le chiffrement peut être activé sur un groupe de machines virtuelles identiques existant. Toutefois, seules les nouvelles machines virtuelles créées après l’activation du chiffrement sont automatiquement chiffrées.
- Les machines virtuelles existantes doivent être libérées et réallouées afin d’être chiffrées.
- Prend en charge les disques de système d’exploitation éphémères, mais uniquement avec les clés gérées par la plateforme.
