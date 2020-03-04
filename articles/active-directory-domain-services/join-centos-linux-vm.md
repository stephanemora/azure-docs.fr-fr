---
title: Rejoindre une machine virtuelle CentOS à Azure AD Domain Services | Microsoft Docs
description: Découvrez comment configurer une machine virtuelle Linux CentOS et comment la joindre à un domaine managé Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 0f0cf1f066257413f23dd83ffc5ecad0169f7e77
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613825"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Joindre une machine virtuelle Linux CentOS à un domaine managé Azure AD Domain Services

Pour permettre aux utilisateurs de se connecter à des machines virtuelles dans Azure en utilisant un ensemble unique d’informations d’identification, vous pouvez joindre les machines virtuelles à un domaine managé Azure Active Directory Domain Services (AD DS). Quand vous joignez une machine virtuelle à un domaine managé Azure AD DS, les comptes d’utilisateurs et les informations d’identification du domaine peuvent être utilisés pour se connecter aux serveurs et les gérer. Les appartenances aux groupes du domaine managé Azure AD DS sont également appliquées pour vous permettre de contrôler l’accès aux fichiers ou aux services sur la machine virtuelle.

Cet article vous montre comment joindre une machine virtuelle Linux CentOS à un domaine managé Azure AD DS.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce tutoriel, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
    * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
    * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
    * Si nécessaire, le premier tutoriel [crée et configure une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Créer une machine virtuelle Linux CentOS et s’y connecter

Si vous disposez d’une machine virtuelle Linux CentOS dans Azure, connectez-vous-y en utilisant SSH, puis passez à l’étape suivante pour [commencer à configurer la machine virtuelle](#configure-the-hosts-file).

Si vous avez besoin de créer une machine virtuelle Linux CentOS ou si vous souhaitez créer une machine virtuelle de test dans le cadre de cet article, vous pouvez employer l’une des méthodes suivantes :

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Au moment de créer la machine virtuelle, faites attention aux paramètres de réseau virtuel de sorte que la machine virtuelle puisse communiquer avec le domaine managé Azure AD DS :

* Déployez la machine virtuelle dans le réseau virtuel où vous avez activé Azure Active Directory Domain Services ou dans un réseau virtuel appairé.
* Déployez la machine virtuelle dans un sous-réseau différent de celui de votre instance Azure AD Domain Services.

Une fois la machine virtuelle déployée, suivez les étapes pour vous connecter à la machine virtuelle avec SSH.

## <a name="configure-the-hosts-file"></a>Configurer le fichier hosts

Pour que le nom d’hôte de la machine virtuelle soit correctement configuré pour le domaine managé, modifiez le fichier */etc/hosts* et définissez le nom d’hôte :

```console
sudo vi /etc/hosts
```

Dans le fichier *hosts*, mettez à jour l’adresse *localhost*. Dans l’exemple suivant :

* *aaddscontoso.com* est le nom de domaine DNS de votre domaine managé Azure AD DS.
* *centos* est le nom d’hôte de votre machine virtuelle CentOS que vous joignez au domaine managé.

Mettez à jour ces noms avec vos propres valeurs :

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Quand vous avez terminé, enregistrez et quittez le fichier *hosts* à l’aide de la commande `:wq` de l’éditeur.

## <a name="install-required-packages"></a>Installer les packages nécessaires

La machine virtuelle a besoin de packages supplémentaires pour être jointe au domaine managé Azure AD DS. Pour installer et configurer ces packages, mettez à jour et installez les outils de jonction de domaine à l’aide de `yum` :

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Joindre la machine virtuelle au domaine managé

Maintenant que les packages nécessaires sont installés sur la machine virtuelle, joignez celle-ci au domaine managé Azure AD DS.

1. Utilisez la commande `realm discover` pour découvrir le domaine managé Azure AD DS. L’exemple suivant découvre le domaine *AADDSCONTOSO.COM*. Spécifiez votre propre nom de domaine managé Azure AD DS TOUT EN MAJUSCULES :

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Si la commande `realm discover` ne trouve pas votre domaine managé Azure AD DS, examinez les étapes de dépannage suivantes :

    * Vérifiez que le domaine est accessible à partir de la machine virtuelle. Essayez `ping aaddscontoso.com` pour voir si une réponse positive est retournée.
    * Vérifiez que la machine virtuelle est déployée dans le réseau virtuel où le domaine managé Azure AD DS est disponible ou dans un réseau virtuel appairé.
    * Vérifiez que les paramètres de serveur DNS du réseau virtuel ont été mis à jour pour pointer vers les contrôleurs de domaine du domaine managé Azure AD DS.

1. À présent, initialisez Kerberos à l’aide de la commande `kinit`. Spécifiez un utilisateur qui appartient au groupe *Administrateurs AAD DC*. Si nécessaire, [ajoutez un compte d’utilisateur à un groupe dans Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Là encore, le nom de domaine managé Azure AD DS doit être entré TOUT EN MAJUSCULES. Dans l’exemple suivant, le compte nommé `contosoadmin@aaddscontoso.com` est utilisé pour initialiser Kerberos. Entrez votre propre compte d’utilisateur qui est membre du groupe *Administrateurs AAD DC* :

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Enfin, joignez la machine au domaine managé Azure AD DS à l’aide de la commande `realm join`. Utilisez le même compte d’utilisateur membre du groupe *Administrateurs AAD DC* que vous avez spécifié dans la commande précédente `kinit`, à savoir `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

La jonction de la machine virtuelle au domaine managé Azure AD DS prend quelques instants. L’exemple de sortie suivant montre que la machine virtuelle a bien été jointe au domaine managé Azure AD DS :

```output
Successfully enrolled machine in realm
```

Si votre machine virtuelle ne peut pas venir à bout du processus de jonction de domaine, vérifiez que le groupe de sécurité réseau de la machine virtuelle autorise le trafic Kerberos sortant sur le port TCP + UDP 464 vers le sous-réseau du réseau virtuel de votre domaine managé Azure AD DS.

## <a name="allow-password-authentication-for-ssh"></a>Autoriser l’authentification par mot de passe pour SSH

Par défaut, les utilisateurs ne peuvent se connecter à une machine virtuelle qu’avec l’authentification par clé publique SSH. L’authentification par mot de passe échoue. Quand vous joignez la machine virtuelle à un domaine managé Azure AD DS, ces comptes de domaine doivent utiliser l’authentification par mot de passe. Mettez à jour la configuration SSH pour autoriser l’authentification par mot de passe comme suit.

1. Ouvrez le fichier *sshd_conf* avec un éditeur :

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Mettez à jour la ligne pour *PasswordAuthentication* sur *yes* :

    ```console
    PasswordAuthentication yes
    ```

    Quand vous avez terminé, enregistrez et quittez le fichier *sshd_conf* à l’aide de la commande `:wq` de l’éditeur.

1. Pour appliquer les modifications et permettre aux utilisateurs de se connecter à l’aide d’un mot de passe, redémarrez le service SSH :

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Accorder les privilèges sudo au groupe « Administrateurs du contrôleur de domaine AAD »

Pour accorder des privilèges d’administration aux membres du groupe *Administrateurs du contrôleur de domaine AAD* sur la machine virtuelle CentOS, ajoutez une entrée au fichier */etc/sudoers*. Une fois ajoutés, les membres du groupe *Administrateurs du contrôleur de domaine AAD* peuvent utiliser la commande `sudo` sur la machine virtuelle CentOS.

1. Ouvrez le fichier *sudoers* pour le modifier :

    ```console
    sudo visudo
    ```

1. Ajoutez l’entrée suivante à la fin du fichier */etc/sudoers*. Étant donné que le nom de groupe *Administrateurs du contrôleur de domaine AAD* contient un espace, incluez-y une barre oblique inverse en guise de caractère d’échappement. Ajoutez votre propre nom de domaine, par exemple *aaddscontoso.com* :

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quand vous avez terminé, enregistrez et quittez l’éditeur à l’aide de la commande `:wq` de l’éditeur.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Se connecter à la machine virtuelle à l’aide d’un compte de domaine

Pour vérifier que la machine virtuelle a bien été jointe au domaine managé Azure AD DS, démarrez une nouvelle connexion SSH en utilisant un compte d’utilisateur du domaine. Vérifiez qu’un répertoire de base a été créé et que l’appartenance au groupe du domaine est appliquée.

1. Créez une connexion SSH à partir de votre console. Utilisez un compte de domaine qui appartient au domaine managé en utilisant la commande `ssh -l`, comme `contosoadmin@aaddscontoso.com`, puis entrez l’adresse de votre machine virtuelle, par exemple *centos.aaddscontoso.com*. Si vous utilisez Azure Cloud Shell, utilisez l’adresse IP publique de la machine virtuelle plutôt que le nom DNS interne.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. Une fois connecté à la machine virtuelle, vérifiez que le répertoire de base a bien été initialisé :

    ```console
    pwd
    ```

    Vous devez vous trouver dans le répertoire de base */home* et votre propre répertoire doit correspondre au compte d’utilisateur.

1. Vérifiez maintenant que les appartenances aux groupes sont correctement résolues :

    ```console
    id
    ```

    Vos appartenances aux groupes du domaine managé Azure AD DS doivent s’afficher.

1. Si vous vous êtes connecté à la machine virtuelle en tant que membre du groupe *Administrateurs AAD DC*, vérifiez que vous pouvez bien utiliser la commande `sudo` :

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des difficultés à connecter la machine virtuelle au domaine managé Azure AD DS ou à vous connecter avec un compte de domaine, consultez [Résoudre les problèmes de jonction à un domaine](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
