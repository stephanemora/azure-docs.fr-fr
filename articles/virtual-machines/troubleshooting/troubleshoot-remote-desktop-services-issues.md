---
title: Les Services Bureau à distance ne démarrent pas sur une machine virtuelle Azure | Microsoft Docs
description: Découvrez comment résoudre les problèmes concernant les Services Bureau à distance lors de la connexion à une Machine virtuelle | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988978"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Les Services Bureau à distance ne démarrent pas sur une machine virtuelle Azure

Cet article décrit comment résoudre les problèmes de connexion à une Machine virtuelle (VM) Azure lorsque les Services Bureau à distance (TermService) ne démarrent pas ou ne parviennent pas à démarrer.

>[!NOTE]
>Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement de Resource Manager. Nous vous recommandons d’utiliser ce modèle pour les nouveaux déploiements au lieu du modèle de déploiement classique.

## <a name="symptoms"></a>Symptômes

Lorsque vous essayez de vous connecter à une machine virtuelle, vous rencontrez les situations suivantes :

- La capture d’écran de la VM montre que le système d’exploitation est entièrement chargé et attend des informations d’identification.
- Toutes les applications sur la VM fonctionnent comme prévu et sont accessibles.
- La VM répond à la connectivité TCP sur le port du protocole RDP (Remote Desktop) Microsoft (par défaut, 3389).
- Vous n’êtes pas invité à entrer les informations d’identification lorsque vous essayez d’établir une connexion RDP.

## <a name="cause"></a>Cause :

Ce problème se produit car les Services Bureau à distance ne s’exécutent pas sur la machine virtuelle. La cause peut varier selon les scénarios suivants :

- Le service TermService a été défini sur **désactivé**.
- Le service TermService est en incident ou suspendu.

## <a name="solution"></a>Solution

Pour résoudre ce problème, utilisez l’une des solutions suivantes ou essayez les solutions une par une :

### <a name="solution-1-using-the-serial-console"></a>Solution 1 : Utiliser la console série

1. Accédez à la [Console série](serial-console-windows.md) en sélectionnant **Support & Troubleshooting (Support et dépannage)** > **Console série (préversion)**. Si la fonctionnalité est activée sur la machine virtuelle, vous pouvez connecter la machine.

2. Créez un canal pour une instance CMD. Tapez **CMD** pour démarrer le canal et en obtenir le nom.

3. Basculez vers le canal qui exécute l’instance CMD, en l’occurrence le canal 1.

   ```
   ch -si 1
   ```

4. Appuyez sur **Entrée** à nouveau et tapez un nom d’utilisateur et un mot de passe valides (ID local ou de domaine) pour la machine virtuelle.

5. Interrogez l’état du service TermService.

   ```
   sc query TermService
   ```

6. Si l’état du service indique **Arrêté**, essayez de démarrer le service.

   ```
   sc start TermService
   ```

7. Interrogez à nouveau le service pour vous assurer qu’il a réussi à démarrer.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Solution 2 : utiliser une machine virtuelle de récupération pour activer le service

[Sauvegardez le disque du système d’exploitation](../windows/snapshot-copy-managed-disk.md) et [attachez le disque du système d’exploitation à une machine virtuelle de secours](../windows/troubleshoot-recovery-disks-portal.md). Ensuite, ouvrez une instance CMD avec élévation de privilèges et exécutez les scripts suivants sur la machine virtuelle de secours :

>[!NOTE]
>Nous partons du principe que la lettre de lecteur qui est affectée au disque du système d’exploitation attaché est F. Remplacez-la par la valeur appropriée dans votre machine virtuelle. Une fois cette opération effectuée, détachez le disque de la machine virtuelle de récupération et [recréez votre machine virtuelle](../windows/create-vm-specialized.md). Pour un dépannage plus poussé, vous pouvez utiliser la **Solution 1** car la Console série aura été activée.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
