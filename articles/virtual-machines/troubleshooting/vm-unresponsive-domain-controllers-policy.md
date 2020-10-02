---
title: La machine virtuelle ne répond pas lors de l’application de la stratégie des contrôleurs de domaine par défaut
titlesuffix: Azure Virtual Machines
description: Cet article explique comment résoudre les problèmes liés à la stratégie des contrôleurs de domaine par défaut qui empêche le démarrage d'une machine virtuelle Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 53e1daca47a2917a19cbc30db5348e4fcc06b325
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90038900"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>La machine virtuelle ne répond pas lors de l’application de la stratégie des contrôleurs de domaine par défaut

Cet article explique comment résoudre les problèmes liés à la stratégie des contrôleurs de domaine par défaut qui empêche le démarrage d'une machine virtuelle Azure.

## <a name="symptom"></a>Symptôme

Lorsque vous utilisez [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) pour afficher la capture d'écran de la machine virtuelle, vous constatez sur la capture d'écran que le système d'exploitation ne répond plus au moment du démarrage et que le message **Stratégie des contrôleurs de domaine par défaut** s'affiche.

  ![La figure 1 montre le système d'exploitation bloqué, avec le message « Stratégie des contrôleurs de domaine par défaut »](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Cause

Ce problème peut être dû à des modifications récemment apportées à la stratégie des contrôleurs de domaine par défaut. Sinon, une analyse du fichier de vidage de la mémoire devra être effectuée pour déterminer la cause racine.

## <a name="solution"></a>Solution

Si vous avez récemment apporté des modifications à la stratégie des contrôleurs de domaine par défaut, vous pouvez annuler ces modifications pour résoudre le problème. Si vous ignorez la cause du problème, collectez un fichier de vidage de la mémoire, puis soumettez un ticket de support.

### <a name="collect-the-memory-dump-file"></a>Collecter le fichier de vidage de la mémoire

Pour résoudre ce problème, vous devez d'abord collecter le fichier de l'image mémoire de l'incident, puis contacter le support muni de ce fichier. Pour collecter le fichier d’image, effectuez les étapes suivantes :

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Attacher le disque du système d’exploitation à une machine virtuelle de récupération

1. Effectuez les étapes 1 à 3 des [commandes de réparation de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) pour préparer une machine virtuelle de réparation.

1. Connectez-vous à la machine virtuelle de réparation à l’aide de la connexion Bureau à distance.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Rechercher le fichier de l’image mémoire et envoyer un ticket de support

1. Sur la machine virtuelle de réparation, accédez au dossier Windows sur le disque du système d'exploitation attaché. Si la lettre de lecteur qui est affectée au disque du système d’exploitation attaché est `F`, vous devez accéder à `F:\Windows`.

1. Recherchez le fichier memory.dmp, puis [envoyez un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) avec le fichier de l’image mémoire.

1. Si vous rencontrez des difficultés pour localiser le fichier memory.dmp, vous pouvez utiliser à la place des [appels d’interruption non masquable (NMI) dans la console série](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls). Suivez le guide pour [générer un fichier d'image mémoire sur incident à l'aide d'appels NMI](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).
