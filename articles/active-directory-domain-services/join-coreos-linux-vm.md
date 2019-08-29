---
title: 'Azure Active Directory Domain Services : Joindre une machine virtuelle Linux CoreOS | Microsoft Docs'
description: Joindre une machine virtuelle CoreOS Linux à Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c1f3d1ec7bb9e9f449cea3f9aa36ca8f80348c6e
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612814"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Joindre une machine virtuelle CoreOS Linux à un domaine managé
Cet article indique comment joindre une machine virtuelle CoreOS Linux dans Azure à un domaine managé Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :
1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](tutorial-create-instance.md).
4. Veillez à configurer les adresses IP du domaine géré en tant que serveurs DNS pour le réseau virtuel. Pour plus d’informations, consultez [Comment mettre à jour les paramètres DNS pour le réseau virtuel Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Effectuez les étapes requises pour [synchroniser les mots de passe à votre domaine géré Azure Active Directory Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Aprovisionner une machine virtuelle CoreOS Linux
Approvisionnez une machine virtuelle CoreOS dans Azure, en utilisant l’une des méthodes suivantes :
* [Portail Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interface de ligne de commande Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Cet article utilise l’image de machine virtuelle **CoreOS Linux (Stable)** dans Azure.

> [!IMPORTANT]
> * Déployez la machine virtuelle dans le **réseau virtuel au sein duquel vous avez activé Azure Active Directory Domain Services**.
> * Choisissez un **sous-réseau** autre que celui dans lequel vous avez activé Azure Active Directory Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connexion à distance à la machine virtuelle Linux qui vient d’être configurée
La machine virtuelle CoreOS a été approvisionnée dans Azure. La tâche suivante consiste à se connecter à distance à la machine virtuelle à l’aide du compte administrateur local créé lors de l’approvisionnement de la machine virtuelle.

Suivez les instructions de l’article [Comment se connecter à une machine virtuelle exécutant Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurer le fichier hosts sur la machine virtuelle Linux
Dans votre terminal SSH, modifiez le fichier/etc/hosts et mettez à jour l’adresse IP et le nom d’hôte de votre machine.

```console
sudo vi /etc/hosts
```

Dans le fichier hosts, saisissez la valeur suivante :

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

Ici, « contoso.com » est le nom de domaine DNS de votre domaine managé. « contoso-coreos » est le nom d’hôte de la machine virtuelle CoreOS que vous joignez au domaine managé.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configurer le service SSSD sur la machine virtuelle Linux
Ensuite, mettez à jour votre fichier de configuration SSSD dans ('/etc/sssd/sssd.conf') pour le faire correspondre à l’exemple suivant :

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = contoso.COM

[domain/contoso.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = contoso.COM
```

Remplacez 'contoso.COM' par le nom de domaine DNS de votre domaine managé. Vérifiez que vous spécifiez le nom de domaine en lettres majuscules dans le fichier de configuration.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Joindre la machine virtuelle Linux au domaine géré
Maintenant que les packages requis sont installés sur la machine virtuelle Linux, la tâche suivante consiste à joindre cette machine virtuelle au domaine géré.

```console
sudo adcli join -D contoso.COM -U bob@contoso.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **Résolution des problèmes :** Si *adcli* ne parvient pas à trouver votre domaine managé :
>   * Vérifiez que le domaine est accessible à partir de la machine virtuelle (effectuez un test ping).
>   * Vérifiez également que la machine virtuelle a bien été déployée dans le réseau virtuel au sein duquel le domaine managé est disponible.
>   * Vérifiez si vous avez mis à jour les paramètres du serveur DNS pour le réseau virtuel afin de pointer vers les contrôleurs de domaine du domaine managé.

Démarrez le service SSSD. Sur votre terminal SSH, saisissez la commande suivante :
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Vérifier la jonction de domaine
Vérifiez rapidement si la machine a bien été jointe au domaine géré. Connectez-vous à la machine virtuelle CoreOS jointe au domaine à l’aide d’une autre connexion SSH. Utilisez un compte d’utilisateur de domaine, puis vérifiez que le compte d’utilisateur est correctement résolu.

1. Sur votre terminal SSH, tapez la commande suivante pour vous connecter à la machine virtuelle CoreOS jointe au domaine à l’aide de SSH. Utilisez un compte de domaine appartenant au domaine géré (par exemple dans ce cas, « bob@contoso.COM »).
    
    ```console
    ssh -l bob@contoso.COM contoso-coreos.contoso.com
    ```

2. Sur votre terminal SSH, tapez la commande suivante pour voir si le répertoire de base a été initialisé correctement.
    
    ```console
    pwd
    ```

3. Sur votre terminal SSH, tapez la commande suivante pour voir si les membres du groupe ont été correctement résolus.
   
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
Reportez-vous à l’article relatif à la [résolution des problèmes de jonction de domaine](join-windows-vm.md#troubleshoot-domain-join-issues) .

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de mise en route](tutorial-create-instance.md)
* [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Comment se connecter à une machine virtuelle exécutant Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
