---
title: Erreur d’arrêt de Windows – 0x0000007E Exception de thread système non gérée
description: Cet article décrit les étapes à suivre lorsque le système d’exploitation invité rencontre un problème et souhaite redémarrer votre machine virtuelle Azure. Le message indique qu’une exception de thread système n’a pas été gérée.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423665"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Erreur d’arrêt de Windows – 0x0000007E Exception de thread système non gérée

Cet article décrit les étapes à suivre lorsque le système d’exploitation invité rencontre un problème et souhaite redémarrer votre machine virtuelle Azure. Le message indique qu’une exception de thread système n’a pas été gérée.

## <a name="symptoms"></a>Symptômes

Lorsque vous utilisez les [Diagnostics de démarrage](./boot-diagnostics.md) pour afficher la capture d’écran de la machine virtuelle, vous voyez que la capture d’écran indique que Windows a besoin de redémarrer avec le code d’arrêt **EXCEPTION DE THREAD SYSTÈME NON GÉRÉE** ou le code d’erreur **0x0000007E**.

![La capture d’écran montre Windows bloqué au démarrage avec le message suivant : « Votre PC a rencontré un problème et doit redémarrer. Nous allons le redémarrer pour vous. » Code d'arrêt : « EXCEPTION DE THREAD SYSTÈME NON GÉRÉE »](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Cause

Il n’est pas possible de déterminer la cause tant qu’un fichier d’image mémoire n’a pas été analysé. Continuez pour collecter le fichier d’image mémoire.

## <a name="solution"></a>Solution

### <a name="collect-the-memory-dump-file"></a>Collecter le fichier de l’image mémoire

Pour résoudre ce problème, vous devez recueillir le fichier de l’image mémoire de l’incident, puis contacter le support muni de ce fichier. Pour collecter le fichier d’image, effectuez les étapes suivantes :

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Attacher le disque du système d’exploitation à une machine virtuelle de récupération

1. Suivez les [étapes 1 à 3 des commandes de réparation de machine virtuelle](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) pour préparer une machine virtuelle de réparation.
2. Connectez-vous à la machine virtuelle de réparation à l’aide de la connexion Bureau à distance.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Rechercher le fichier de l’image mémoire et envoyer un ticket de support

1. Sur la machine virtuelle de réparation, accédez au dossier Windows sur le disque du système d'exploitation attaché. Si la lettre de lecteur qui est affectée au disque de système d’exploitation attaché est *F*, alors vous devez accéder à `F:\Windows`.
2. Recherchez le fichier **memory.dmp**, puis [envoyez un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) avec le fichier de l’image mémoire.
3. Si vous ne parvenez pas à localiser le fichier **memory.dmp**, suivez le guide pour [générer un fichier d’image mémoire sur incident à l’aide d’appels d’interruption non masquable (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Pour plus d’informations sur les appels NMI, consultez le guide de l’utilisateur relatif aux [appels d’interruption non masquable (NMI) dans une console série](./serial-console-windows.md#use-the-serial-console-for-nmi-calls).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Résoudre les erreurs de démarrage de machine virtuelle Azure](./boot-error-troubleshoot.md)