---
title: Configurer un client NFS pour Azure NetApp Files | Microsoft Docs
description: Explique comment configurer des clients NFS à utiliser avec Azure NetApp Files.
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
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449648"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurer un client NFS pour Azure NetApp Files

La configuration du client NFS décrite dans cet article fait partie de l’installation lorsque vous [configurez le chiffrement NFSv4.1 Kerberos](configure-kerberos-encryption.md) ou [créez un volume à double protocole](create-volumes-dual-protocol.md). De nombreuses distributions Linux peuvent être utilisées avec Azure NetApp Files. Cet article décrit les configurations pour deux des environnements les plus couramment utilisés : RHEL 8 et Ubuntu 18.04. 

Quelle que soit la version de Linux que vous utilisez, les configurations suivantes sont nécessaires :
* Configurez un client NTP pour éviter tout problème d’asymétrie temporelle.
* Configurez les entrées DNS du client Linux pour la résolution de noms.  
    Cette configuration inclut l’enregistrement « A » (direct) et l’enregistrement PTR (inversé). 
* Pour la jonction de domaine, créez un compte d’ordinateur dans le domaine Active Directory cible (créé avec la commande de jonction de domaine). 
    > [!NOTE] 
    > La variable `$SERVICEACCOUNT` utilisée dans les commandes ci-dessous doit être un compte d’utilisateur disposant d’autorisations ou d’une délégation pour créer un compte d’ordinateur dans l’unité d’organisation ciblée.
* Autorisez le client à monter des volumes NFS et d’autres outils de supervision pertinents.

## <a name="rhel-8-configuration"></a>Configuration pour RHEL 8 

1. Installez les packages :   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Configurez le client NTP :  
    RHEL 8 utilise `chrony` par défaut.  Suivez les instructions de configuration de la page [Using the Chrony suite to configure NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp) (Utilisation de la suite Chrony pour configurer NTP).

3. Joignez le domaine Active Directory :  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Configuration pour Ubuntu 
Cette section décrit la configuration d'Ubuntu pour les clients NFS.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Si vous utilisez le chiffrement Kerberos NFSv4.1 

1. Installez les packages :  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configurez le client NTP.  
    Ubuntu 18.04 utilise `chrony` par défaut.  Suivez les instructions de configuration de la page [Ubuntu Bionic : Using chrony to configure NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp) (Utilisation de Chrony pour configurer NTP).

3. Joignez le domaine Active Directory :  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Si vous utilisez le double protocole  

1. Pour mettre à niveau les packages installés, exécutez la commande suivante :  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Exemple :   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Pour redémarrer et activer le service, exécutez la commande suivante :   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

L'exemple suivant interroge le serveur LDAP AD à partir du client LDAP Ubuntu pour un utilisateur LDAP `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md)

