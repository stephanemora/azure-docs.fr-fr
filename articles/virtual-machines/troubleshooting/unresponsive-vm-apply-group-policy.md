---
title: La machine virtuelle Azure ne répond pas lors de l’application de la stratégie
description: Cet article décrit les étapes à suivre pour résoudre les problèmes de blocage de l’écran de chargement lors de l’application d’une stratégie pendant le démarrage sur une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748728"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>La machine virtuelle ne répond plus lors de l’application de la stratégie « Utilisateurs locaux et groupes de stratégie de groupe »

Cet article décrit les étapes à suivre pour résoudre les problèmes de blocage de l’écran de chargement lors de l’application d’une stratégie pendant le démarrage sur une machine virtuelle Azure.

## <a name="symptoms"></a>Symptômes

Quand vous utilisez [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) pour visualiser une capture d’écran de la machine virtuelle, l’écran est bloqué et affiche le message : « *Application de la stratégie Utilisateurs locaux et groupes de stratégie de groupe* ».

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="Capture d’écran du chargement de l’application d’une stratégie Utilisateurs locaux et groupes de stratégie de groupe (Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="Capture d’écran du chargement de l’application d’une stratégie Utilisateurs locaux et groupes de stratégie de groupe (Windows Server 2012).":::

## <a name="cause"></a>Cause

Des verrous sont en conflit lorsque la stratégie tente de nettoyer les anciens profils utilisateur.

> [!NOTE]
> Cela concerne uniquement Windows Server 2012 et Windows Server 2012 R2.

Voici la stratégie problématique :

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>Résolution

### <a name="process-overview"></a>Vue d’ensemble du processus

1. [Créer une machine virtuelle de réparation et y accéder](#step-1-create-and-access-a-repair-vm)
2. [Désactiver la stratégie](#step-2-disable-the-policy)
3. [Activer la console série et la collecte d’image mémoire](#step-3-enable-serial-console-and-memory-dump-collection)
4. [Régénérer la machine virtuelle](#step-4-rebuild-the-vm)

> [!NOTE]
> Si vous rencontrez cette erreur de démarrage, le système d’exploitation invité n’est pas opérationnel. Vous devez résoudre ce problème en mode hors connexion.

### <a name="step-1-create-and-access-a-repair-vm"></a>Étape 1 : Créer une machine virtuelle de réparation et y accéder

1. Suivez les [étapes 1 à 3 des commandes de réparation de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) pour préparer une machine virtuelle de réparation.
2. Connectez-vous à la machine virtuelle de réparation à l’aide de la connexion Bureau à distance.

### <a name="step-2-disable-the-policy"></a>Étape 2 : Désactiver la stratégie

1. Sur la machine virtuelle de réparation, ouvrir l’éditeur du Registre.
2. Localiser la clé **HKEY_LOCAL_MACHINE**, puis sélectionner **Fichier** > **Charger Hive...** dans le menu.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="La capture d’écran montre en surbrillance HKEY_LOCAL_MACHINE et le menu qui contient l’option « Charger Hive ».":::

    - Charger Hive vous permet de charger des clés de Registre à partir d’un système hors connexion, ici, le disque défectueux rattaché à la machine virtuelle de réparation.
    - Les paramètres à l’ensemble du système sont stockés sur `HKEY_LOCAL_MACHINE` et peuvent être abrégés sous la forme « HKLM ».
3. Dans le disque attaché, accédez au fichier `\windows\system32\config\SOFTWARE` et ouvrez-le.

    1. Vous êtes invité à entrer un nom. Sélectionnez BROKENSOFTWARE.<br/>
    2. Pour vérifier que BROKENSOFTWARE a été chargé, développez **HKEY_LOCAL_MACHINE** et recherchez la clé BROKENSOFTWARE ajoutée.
4. Accédez à BROKENSOFTWARE et vérifiez si la clé CleanupProfile existe dans la ruche chargée.

    1. Si la clé existe, cela signifie que la stratégie CleanupProfile est définie. Sa valeur représente la stratégie de rétention en jours. Poursuivez la suppression de la clé.<br/>
    2. Si la clé n’existe pas, la stratégie CleanupProfile n’est pas définie. [Soumettez un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), incluant le fichier memory.dmp situé dans le répertoire Windows du disque du système d’exploitation attaché.

5. Supprimez la clé CleanupProfiles à l’aide de cette commande :

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  Déchargez la ruche BROKENSOFTWARE à l’aide de cette commande :

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>Étape 3 : Activez la console série et la collecte d’image mémoire

Pour activer la collecte d’image mémoire et la console série, exécutez le script suivant :

1. Ouvrez une session d’invite de commandes avec élévation de privilèges (Exécuter en tant qu’administrateur).
2. Exécutez ces commandes :

    **Activer la console série** : 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. Assurez-vous que l’espace libre sur le disque du système d’exploitation est au moins égal à la taille de la mémoire (RAM) de la machine virtuelle.

    Si l’espace est insuffisant sur le disque du système d’exploitation, modifiez l’emplacement de l’image mémoire et rattaché-le à un disque de données attaché avec suffisamment d’espace libre. Pour modifier l’emplacement, remplacez « %SystemRoot% » par la lettre du lecteur (par exemple, « F: ») du disque de données dans les commandes ci-dessous.

    **Configuration suggérée pour activer l’image mémoire du système d’exploitation** :

    Chargez le disque de système d’exploitation corrompu :

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Activez sur ControlSet001 :
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Activez sur ControlSet002 :
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Déchargez le disque de système d’exploitation corrompu :
    
    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>Étape 4 : Régénérez la machine virtuelle.

Suivez l’[étape 5 des commandes de réparation de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) pour réassembler la machine virtuelle.

Si le problème est résolu, cela signifie que la stratégie a été désactivée localement. Pour une solution permanente, n’utilisez pas la stratégie CleanupProfiles sur les machines virtuelles. Utilisez une autre méthode pour effectuer les nettoyages des profils.

N’utilisez pas cette stratégie :

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes lorsque vous appliquez une mise à jour Windows, consultez [La machine virtuelle ne répond pas (erreur « C01A001D ») lors de l’application de la mise à jour Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update).