---
title: Résoudre les problèmes de l’agent Linux Azure
description: Résolvez les problèmes liés à l’agent Linux Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 247324c30bbe0edaef78c0b0d5e6a6d593e8cac9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586395"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Résoudre les problèmes de l’agent Linux Azure

L’[agent Linux Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) permet à une machine virtuelle de communiquer avec le contrôleur de structure (serveur physique sous-jacent sur lequel la machine virtuelle est hébergée) avec l’adresse IP 168.63.129.16.

>[!NOTE]
>Cette adresse IP est une adresse IP publique virtuelle qui facilite la communication et qui ne doit pas être bloquée. Pour plus d’informations, consultez [Qu’est-ce que l’adresse IP 168.63.129.16 ?](../../virtual-network/what-is-ip-address-168-63-129-16.md)

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez l’état et la version de l’agent pour garantir qu’il est toujours pris en charge. Consultez [Prise en charge de version minimale pour les agents de machine virtuelle dans Azure](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version) pour vérifier la prise en charge des versions, ou consultez les [questions fréquentes (FAQ) sur WALinuxAgent](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) pour connaître les étapes permettant de rechercher l’état et la version.

## <a name="troubleshoot-a-not-ready-status"></a>Résoudre les problèmes liés à un état Non prêt

1. Vérifiez l’état du service de l’agent Linux Azure pour garantir qu’il est en cours d’exécution. Le nom du service peut être **walinuxagent** ou **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Si le service est en cours d’exécution, redémarrez-le pour résoudre le problème. Si le service est arrêté, démarrez-le, patientez quelques minutes, puis revérifiez l’état.

1. Vérifier que la mise à jour automatique est activée. Vérifiez le paramètre de mise à jour automatique dans **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Pour plus d’informations sur la façon de mettre à jour l’agent Linux Azure, consultez [Guide pratique pour mettre à jour l’agent Linux Azure sur une machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent).

1. Vérifiez que la machine virtuelle peut se connecter au contrôleur de structure. À l’aide d’un outil tel que curl, effectuez un test pour déterminer si la machine virtuelle peut se connecter à l’adresse IP 168.63.129.16 sur les ports 80, 443 et 32526. Si la machine virtuelle ne se connecte pas comme prévu, vérifiez si la communication sortante sur les ports 80, 443 et 32526 est ouverte dans votre pare-feu local sur la machine virtuelle. Si cette adresse IP est bloquée, l’agent de machine virtuelle peut présenter un comportement inattendu.

## <a name="advanced-troubleshooting"></a>Dépannage avancé

Les événements concernant la résolution des problèmes liés à l’agent Linux Azure sont enregistrés dans le fichier **/var/log/waagent.log**.

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Impossible de se connecter à l’adresse IP WireServer (adresse IP de l’hôte)

L’erreur suivante apparaît dans le fichier **/var/log/waagent.log** quand la machine virtuelle ne peut pas atteindre l’adresse IP WireServer sur le serveur hôte.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Pour résoudre ce problème :

* Connectez-vous à la machine virtuelle à l’aide de SSH, puis essayez d’accéder à l’URL suivante à partir de curl : http://168.63.129.16/?comp=versions.
* Vérifiez si un pare-feu, un proxy ou une autre source pourraient occasionner des problèmes susceptibles de bloquer l’accès à l’adresse IP 168.63.129.16.
* Vérifiez si Linux IPTables ou un pare-feu tiers bloque l’accès aux ports 80, 443 et 32526.

## <a name="next-steps"></a>Étapes suivantes

Pour une résolution plus avancée des problèmes liés à l’agent Linux Azure, [contactez le support technique Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
