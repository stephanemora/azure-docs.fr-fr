---
title: Vérification du système de fichiers lors du démarrage d’une machine virtuelle Azure | Microsoft Docs
description: Découvrez comment résoudre le problème que la machine virtuelle affiche le message Vérification du système de fichiers au démarrage | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 91285fd3bf42158068040833146377cb6c48e4e8
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47411703"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows affiche le message « Vérification du système de fichiers » lors du démarrage d’une machine virtuelle Azure

Cet article décrit l’erreur « Vérification du système de fichiers » que vous pouvez rencontrer lorsque vous démarrez une machine virtuelle Windows dans Microsoft Azure.

> [!NOTE] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que nous recommandons pour les nouveaux déploiements à la place du modèle de déploiement classique.

## <a name="symptom"></a>Symptôme 

Une machine virtuelle Windows ne démarre pas. Lorsque vous vérifiez les captures d’écran de démarrage dans [Diagnostics de démarrage](boot-diagnostics.md), vous voyez que le processus de vérification du disque (chkdsk.exe) s’exécute et génère l’un des messages suivants :

- Analyse et réparation du lecteur (C:)
- Vérification du système de fichiers sur C:

## <a name="cause"></a>Cause :

Si une erreur NTFS est détectée dans le système de fichiers, Windows vérifie et répare la cohérence du disque lors du redémarrage suivant. Cela se produit généralement si la machine virtuelle a redémarré de façon inattendue ou si le processus d’arrêt de la machine virtuelle a été brusquement interrompu.

## <a name="solution"></a>Solution 

Windows démarre normalement une fois le processus de vérification du disque terminé. Si la machine virtuelle est bloquée dans le processus de vérification du disque, essayez d’exécuter la vérification du disque sur la machine virtuelle hors connexion :
1.  Prenez un instantané du disque du système d’exploitation de la machine virtuelle affectée en guise de sauvegarde. Pour plus d’informations, voir [Prendre un instantané d’un disque](../windows/snapshot-copy-managed-disk.md).
2.  [Attachez le disque du système d’exploitation à une machine virtuelle de récupération](troubleshoot-recovery-disks-portal-windows.md).  
3.  Sur la machine virtuelle de récupération, effectuez la vérification du disque du système d’exploitation attaché. Dans l’exemple suivant, la lettre de lecteur du disque du système d’exploitation attaché est E:. 
        
        chkdsk E: /f
4.  Une fois la vérification du disque terminée, détachez le disque de la machine virtuelle de récupération, puis rattachez-le à la machine virtuelle affectée en tant que disque du système d’exploitation. Pour en savoir plus, voir [Résoudre les problèmes d’une machine virtuelle Windows en connectant le disque du système d’exploitation à une machine virtuelle de récupération](troubleshoot-recovery-disks-portal-windows.md).
