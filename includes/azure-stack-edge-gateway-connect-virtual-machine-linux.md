---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 92688051cf591470246094dd0d8ea1d436a8efb1
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500210"
---
Connectez-vous à la machine virtuelle à l’aide de l’adresse IP privée que vous avez transmise lors de sa création.

Ouvrez une session SSH pour vous connecter avec l’adresse IP.

`ssh -l <username> <ip address>`

À l’invite, indiquez le mot de passe que vous avez utilisé lors de la création de la machine virtuelle.

Si vous avez besoin de fournir la clé SSH, utilisez cette commande.

`ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236`

Voici un exemple de sortie lors de la connexion à une machine virtuelle :

```powershell
PS C:\07-30-2020\linux> ssh -l Administrator 10.126.68.186
Administrator@10.126.68.186's password:
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.0.0-1027-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Jul 30 22:56:11 UTC 2020

  System load:  0.0               Processes:           105
  Usage of /:   5.6% of 28.90GB   Users logged in:     0
  Memory usage: 12%               IP address for eth0: 10.126.68.186
  Swap usage:   0%

 * Are you ready for Kubernetes 1.19? It's nearly here! Try RC3 with
   sudo snap install microk8s --channel=1.19/candidate --classic

   https://www.microk8s.io/ has docs and details.

68 packages can be updated.
0 updates are security updates.


*** System restart required ***

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Administrator@mylinuxvm:
```

Si vous avez utilisé une adresse IP publique lors de la création de la machine virtuelle, vous pouvez l’utiliser pour vous connecter à la machine virtuelle. Pour obtenir l’adresse IP publique : 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
Dans cet exemple, l’adresse IP publique sera la même que l’adresse IP privée que vous avez transmise lors de la création de l’interface réseau virtuelle.