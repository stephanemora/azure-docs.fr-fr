---
title: La machine virtuelle ne répond pas pendant l’attente du Gestionnaire de session locale
description: Cet article explique comment résoudre les problèmes liés au fait que le système d’exploitation invité est bloqué en attendant que le Gestionnaire de session locale termine le traitement lors du démarrage d’une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: ae3d35bea1c75c797447100316ceab9a27fcbe48
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423667"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>La machine virtuelle ne répond pas pendant l’attente du Gestionnaire de session locale

Cet article explique comment résoudre les problèmes liés au fait que le système d’exploitation invité est bloqué en attendant que le Gestionnaire de session locale termine le traitement lors du démarrage d’une machine virtuelle Azure.

## <a name="symptoms"></a>Symptômes

Quand vous utilisez les [Diagnostics de démarrage](./boot-diagnostics.md) pour visualiser la capture d’écran de la machine virtuelle, vous constatez que cette capture affiche une invite avec le message suivant : « *Attendez le Gestionnaire de session locale* »

![La capture d’écran montre le système d’exploitation invité bloqué avec le message « Attendez le Gestionnaire de session locale » dans Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Cause

Il peut y avoir plusieurs raisons pour lesquelles une machine virtuelle est bloquée en attente du Gestionnaire de session locale. Si l’attente du Gestionnaire de session locale est un problème persistant, vous devez collecter une image mémoire pour l’analyse.

## <a name="solution"></a>Solution

Dans certains cas, il suffit d’attendre suffisamment longtemps pour que le processus se termine. Si votre machine virtuelle est bloquée sur l’écran d’attente pendant plus d’une heure, vous devez collecter une image mémoire, puis contacter le Support Microsoft.

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
> [Résoudre les erreurs de démarrage de machine virtuelle Azure](boot-error-troubleshoot.md)