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
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 20cbc9b33e567ffe306aae694bb835d95c2d861e
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88704975"
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

1. Installez les packages :  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configurez le client NTP.  
    Ubuntu 18.04 utilise `chrony` par défaut.  Suivez les instructions de configuration de la page [Ubuntu Bionic : Using chrony to configure NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp) (Utilisation de Chrony pour configurer NTP).

3. Joignez le domaine Active Directory :  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md)

