---
title: Résoudre le problème de Windows qui n’a pas pu terminer la configuration du système
titlesuffix: Azure Virtual Machines
description: Cet article explique comment résoudre des problèmes où le processus Sysprep empêche le démarrage d’une machine virtuelle Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629587"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Résoudre le problème de Windows qui n’a pas pu terminer la configuration du système

Cet article explique comment résoudre des problèmes où le processus Sysprep empêche le démarrage d’une machine virtuelle Azure.

## <a name="symptom"></a>Symptôme

Quand vous utilisez les [diagnostics de démarrage](./boot-diagnostics.md) pour visualiser la capture d’écran de la machine virtuelle, vous pouvez voir que cette capture montre une erreur d’installation de Windows alors que la configuration de Windows démarre des services. L’erreur affiche le message :

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![La figure 1 montre une erreur d’installation de Windows avec le message : « Windows n’a pas pu terminer la configuration du système. Pour tenter de reprendre la configuration, redémarrez l’ordinateur. Le programme d’installation démarre des services »](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Cause

Cette erreur se produit quand le système d’exploitation ne parvient pas à effectuer le [processus Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). Cette erreur se produit quand vous tentez d’effectuer le démarrage initial d’une machine virtuelle généralisée. Si vous rencontrez ce problème, recréez l’image généralisée, car l’image est dans un état « non déployable » et ne peut pas être récupérée.

## <a name="solution"></a>Solution

> [!TIP]
> Si vous disposez d’une sauvegarde récente de la machine virtuelle, vous pouvez essayer de [restaurer la machine virtuelle à partir de la sauvegarde](../../backup/backup-azure-arm-restore-vms.md) pour résoudre le problème de démarrage.

Pour résoudre ce problème, suivez les [instructions Azure sur la préparation/capture d’une image](../windows/upload-generalized-managed.md) et préparez une nouvelle image généralisée.