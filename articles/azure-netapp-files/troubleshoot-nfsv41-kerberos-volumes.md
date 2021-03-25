---
title: Résoudre les problèmes de volume Kerberos NFSv 4.1 pour Azure NetApp Files | Microsoft Docs
description: Décrit les messages d’erreur et les résolutions qui peuvent vous aider à résoudre les problèmes de volume Kerberos NFSv4.1 pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134311"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Résoudre les problèmes de volume Kerberos NFSv4.1 

Cet article décrit les résolutions des conditions d’erreur que vous pouvez rencontrer lors de la création ou de la gestion de volumes Kerberos NFSv4.1. 

## <a name="error-conditions-and-resolutions"></a>Conditions d’erreur et résolutions

|     Conditions d’erreur    |     Résolutions    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files ne prend pas en charge Kerberos pour les volumes NFSv3. Kerberos est uniquement pris en charge pour le protocole NFSv4.1.  |
|`This NetApp account has no configured Active Directory   connections`  |  Configurez Active Directory pour le compte NetApp avec des champs **IP KDC** et **Nom du serveur AD**. Pour obtenir des instructions, consultez [Configurer le portail Azure](configure-kerberos-encryption.md#configure-the-azure-portal). |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files ne prend pas en charge la conversion d’un volume brut NFSv 4.1 en volume Kerberos NFSv 4.1, et inversement. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Exemple : `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Assurez-vous que les enregistrements A/PTR sont correctement configurés et présents dans Active Directory pour le nom du serveur `smb-test-64d9.contoso.com`. <br> Dans le client NFS, si `nslookup` pour `smb-test-64d9.contoso.com` correspond à l’adresse IP IP1 (à savoir, `10.1.1.68`), `nslookup` pour IP1 doit correspondre à un seul enregistrement (à savoir, `smb-test-64d9.contoso.com`). `nslookup` pour IP1 ne *doit* pas correspondre à plusieurs noms. </li>  <li>Définissez AES-256 pour le compte d’ordinateur NFS de type `NFS-<Smb NETBIOS NAME>-<few random characters>` sur AD à l’aide de PowerShell ou de l’interface utilisateur. <br> Exemples de commandes : <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Veillez à ce que l’heure du client NFS, d’AD et du logiciel de stockage Azure NetApp Files soit synchronisée et se situe dans un intervalle de cinq minutes. </li>  <li>Récupérez le ticket Kerberos sur le client NFS à l’aide de la commande `kinit <administrator>`.</li> <li>Réduisez le nom d’hôte du client NFS à moins de 15 caractères et effectuez une nouvelle jonction de domaine. </li><li>Redémarrez le client NFS et le service `rpcgssd` comme suit. La commande peut varier selon le système d’exploitation.<br> RHEL 7 : <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8 : <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu : <br> (Redémarrez le service `rpc-gssd`.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Le problème peut être lié au problème du client NFS. Redémarrez le client NFS.    |
|`Hostname lookup failed`   | Vous devez créer une zone de recherche inversée sur le serveur DNS, puis ajouter un enregistrement PTR de l’ordinateur hôte AD dans cette zone de recherche inversée. <br> Par exemple, supposons que l’adresse IP de l’ordinateur AD est `10.1.1.4`, le nom d’hôte de l’ordinateur AD (trouvé à l’aide de la commande hostname) est `AD1` et le nom de domaine `contoso.com`. L’enregistrement PTR ajouté à la zone de recherche inversée doit être `10.1.1.4 -> AD1.contoso.com`. |
|`Volume creation fails due to unreachable DNS server`  | Deux solutions sont disponibles : <br> <ul><li> Cette erreur indique que DNS n’est pas accessible. Il peut s’agir d’une adresse IP DNS incorrecte ou d’un problème de réseau. Vérifiez l’adresse IP DNS entrée dans la connexion AD et assurez-vous que l’adresse IP est correcte. </li> <li> Assurez-vous que l’instance AD et le volume se trouvent dans la même région et dans le même réseau virtuel. S’ils se trouvent dans des réseaux virtuels différents, assurez-vous que le peering de réseaux virtuels est établi entre les deux réseaux virtuels. </li></ul> |
|La création du volume Kerberos NFSv 4.1 échoue avec une erreur similaire à l’exemple suivant : <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |L’adresse IP du KDC est incorrecte et le volume Kerberos a été créé. Mettez à jour l’adresse IP du KDC avec une adresse correcte. <br> Une fois l’adresse IP du KDC mise à jour, l’erreur ne disparaît pas. Vous devez recréer le volume. |

## <a name="next-steps"></a>Étapes suivantes  

* [Configurer le chiffrement Kerberos NFSv4.1 pour Azure NetApp Files](configure-kerberos-encryption.md)
