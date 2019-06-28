---
title: Console série Azure pour Linux | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles et groupes de machines virtuelles identiques Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: a561d29f462d44eb6bc440bb6110430cc5c51688
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735243"
---
# <a name="azure-serial-console-for-linux"></a>Console série Azure pour Linux

La console série du Portail Azure permet aux machines virtuelles et aux instances de groupe de machines virtuelles identiques Linux d’accéder à une console texte. Cette connexion série s’effectue par le biais du port série COM1 de la machine virtuelle ou de l’instance du groupe de machines virtuelles identiques. Elle fournit l’accès à la machine virtuelle ou à l’instance et n’est pas liée au réseau ni à l’état du système d’exploitation. La console série est uniquement accessible à l’aide du Portail Azure et est autorisée uniquement pour les utilisateurs disposant d’un rôle d’accès de contributeur supérieur ou égal à la machine virtuelle ou au groupe de machines virtuelles identiques.

La console série fonctionne de la même manière pour les machines virtuelles et les instances de groupe de machines virtuelles identiques. Dans ce document, toutes les mentions aux machines virtuelles incluent implicitement les instances de groupe de machines virtuelles identiques, sauf indication contraire.

Pour en savoir plus sur la console série Windows, consultez [Console série Windows](../windows/serial-console.md).

> [!NOTE]
> La console série est généralement disponible dans les régions Azure globales. Elle n’est pas encore disponible dans les clouds Azure Government, ni dans les clouds Azure - Chine.


## <a name="prerequisites"></a>Prérequis

- Votre machine virtuelle ou instance de groupe de machines virtuelles identiques doit utiliser le modèle de déploiement Resource Manager. Les déploiements classiques ne sont pas pris en charge.

- Le compte qui utilise une console série doit disposer du [rôle Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pour la machine virtuelle et du compte de stockage de [diagnostics de démarrage](boot-diagnostics.md)

- Votre machine virtuelle ou instance de groupe de machines virtuelles identiques doit avoir une authentification de l’utilisateur par mot de passe. Vous pouvez en créer un avec la fonction [Réinitialiser le mot de passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de l’extension d’accès aux machines virtuelles. Sélectionnez **Réinitialiser le mot de passe** dans la section **Support + dépannage**.

- Les [diagnostics de démarrage](boot-diagnostics.md) de votre machine virtuelle ou instance de groupe de machines virtuelles identiques doivent être activés.

    ![Paramètres de l’option Diagnostics de démarrage](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Pour découvrir les paramètres spécifiques des distributions Linux, consultez [Disponibilité de distributions Linux pour la console série](#serial-console-linux-distribution-availability).

- Votre machine virtuelle ou instance de groupe de machines virtuelles identiques doit être configurée pour une sortie en série sur `ttys0`. Il s’agit de la valeur par défaut pour les images Azure, mais pensez à la vérifier pour les images personnalisées. Vous trouverez plus de détails [ci-dessous](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Bien démarrer avec la console série
Pour les machines virtuelles et les groupes de machines virtuelles identiques, la console série est accessible uniquement sur le Portail Azure :

### <a name="serial-console-for-virtual-machines"></a>Console série pour machines virtuelles
La console série pour machines virtuelles est aussi simple que de cliquer sur **Console série** dans la section **Support + dépannage** dans le Portail Azure.
  1. Ouvrez le [portail Azure](https://portal.azure.com).

  1. Accédez à **Toutes les ressources** et sélectionnez une machine virtuelle. La page de présentation de la machine virtuelle s’ouvre.

  1. Faites défiler l’écran jusqu’à la section **Support + dépannage**, puis sélectionnez **Console série**. Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

     ![Fenêtre de console série Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console série pour les groupes de machines virtuelles identiques
La console série est disponible sur une base par instance pour les groupes de machines virtuelles identiques. Vous devrez accéder à l’instance individuelle d’un groupe de machines virtuelles identiques avant de voir le bouton **Console série**. Si votre groupe de machines virtuelles identiques n’a pas activé les diagnostics de démarrage, assurez-vous de mettre à jour votre modèle de groupe de machines virtuelles identiques pour activer les diagnostics de démarrage, puis mettez à niveau toutes les instances vers le nouveau modèle afin d’accéder à la console série.
  1. Ouvrez le [portail Azure](https://portal.azure.com).

  1. Accédez à **Toutes les ressources** et sélectionnez un groupe de machines virtuelles identiques. La page Vue d’ensemble du groupe de machines virtuelles identiques s’affiche.

  1. Accédez à **Instances**

  1. Sélectionnez une instance de groupe de machines virtuelles identiques

  1. Dans la section **Support + dépannage**, sélectionnez **Console série**. Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

     ![Console série de groupe de machines virtuelles identiques Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> La console série nécessite la configuration d’un utilisateur local avec mot de passe. Les machines virtuelles ou groupes de machines virtuelles identiques configurés uniquement avec une clé publique SSH ne peuvent pas se connecter à la console série. Pour créer un utilisateur local avec mot de passe, utilisez [Extension VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), disponible dans le portail par le biais de l’option **Réinitialiser le mot de passe** du portail Azure, puis créez un utilisateur local avec mot de passe.
> Vous pouvez également réinitialiser le mot de passe administrateur de votre compte en [utilisant le GRUB pour démarrer en mode mono-utilisateur](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilité de distributions Linux pour la console série
Afin de permettre le bon fonctionnement de la console série, le système d’exploitation invité doit être configuré pour la lecture et l’écriture des messages de console sur le port série. La plupart des [distributions Azure Linux approuvées](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) présentent la console série configurée par défaut. Sélectionnez **Console série** dans la section **Support + dépannage** du portail Azure pour accéder à la console série.

> [!NOTE]
> Si vous ne voyez rien dans la console série, vérifiez que les diagnostics de démarrage sont activés sur votre machine virtuelle. Le fait de cliquer sur **Entrée** permet généralement de corriger les problèmes à cause desquels rien ne s’affiche dans la console série.

Distribution      | Accès à la console série
:-----------|:---------------------
Red Hat Enterprise Linux    | Accès à la console série activé par défaut.
CentOS      | Accès à la console série activé par défaut.
Ubuntu      | Accès à la console série activé par défaut.
CoreOS      | Accès à la console série activé par défaut.
SUSE        | Les images SLES les plus récentes disponibles sur Azure disposent de l’accès à la console série activé par défaut. Si vous utilisez des versions antérieures (version 10 ou antérieure) de SLES sur Azure, consultez les instructions de l’[article de la base de connaissances](https://www.novell.com/support/kb/doc.php?id=3456486) pour activer la console série.
Oracle Linux        | Accès à la console série activé par défaut.

### <a name="custom-linux-images"></a>Images Linux personnalisées
Afin d’activer la console série pour votre image Linux personnalisée de machine virtuelle, activez l’accès à la console dans le fichier */etc/inittab* pour exécuter un terminal sur `ttyS0`. Par exemple : `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Vous devez également ajouter ttys0 comme destination de la sortie en série. Pour plus d’informations sur la configuration d’une image personnalisée pour que cette dernière fonctionne avec la console série, consultez les exigences générales du système sous [Création et téléchargement d’un disque dur virtuel Linux dans Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Si vous générez un noyau personnalisé, envisagez d’activer les indicateurs de noyau `CONFIG_SERIAL_8250=y` et `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Le fichier config se trouve généralement sous */boot/* . |

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Scénarios courants d’accès à la console série

Scénario          | Actions à effectuer dans la console série
:------------------|:-----------------------------------------
Fichier *FSTAB* endommagé | Appuyez sur la touche **Entrée** pour continuer, puis utilisez un éditeur de texte pour réparer le fichier *FSTAB*. Vous devrez peut-être activer le mode mono-utilisateur pour ce faire. Pour en savoir plus, consultez les sections [Résoudre les incidents fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) et [Utiliser la console série pour accéder au GRUB et au mode mono-utilisateur](serial-console-grub-single-user-mode.md) de la console série.
Règles de pare-feu incorrectes |  Si vous avez configuré iptables pour bloquer la connectivité SSH, vous pouvez utiliser la console série pour interagir avec votre machine virtuelle sans avoir besoin de SSH. Vous trouverez plus d’informations sur la [page du manuel des tables d’adresses IP](https://linux.die.net/man/8/iptables).<br>De même, si votre pare-feu bloque l’accès SSH, vous pouvez accéder à la machine virtuelle via la console série et reconfigurer le pare-feu. Pour plus d’informations, consultez la [documentation sur le pare-feu](https://firewalld.org/documentation/).
Contrôle de la corruption du système de fichiers | Consultez la section [Azure Linux VM cannot start because of file system errors](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) (La machine virtuelle Azure Linux ne peut pas démarrer en raison d’erreurs du système de fichiers) de la console série pour obtenir plus d’informations sur la résolution des problèmes des systèmes de fichiers endommagés à l’aide de la console série.
Problèmes de configuration SSH | Accédez à la console série et modifiez les paramètres. La console série peut être utilisée quelle que soit la configuration SSH d’une machine virtuelle. La machine virtuelle ne nécessite pas de connectivité réseau pour fonctionner. Un guide de résolution des problèmes est disponible sous [Dépannage d’une connexion SSH à une machine virtuelle Linux Azure défaillante, qui génère une erreur ou qui est refusée](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Pour en savoir plus, consultez la page [Étapes détaillées supplémentaires de résolution des problèmes SSH pour la connexion à une machine virtuelle Linux dans Azure](./detailed-troubleshoot-ssh-connection.md)
Interaction avec le chargeur de démarrage | Redémarrez votre machine virtuelle à partir du panneau de la console série pour accéder au GRUB sur votre machine virtuelle Linux. Pour en savoir plus et obtenir des informations spécifiques sur la distribution, consultez [Utiliser la console série pour accéder au GRUB et au mode mono-utilisateur](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Désactiver la console série
Par défaut, tous les abonnements ont accès à la console série. Vous pouvez désactiver la console série au niveau de l’abonnement ou de la machine virtuelle/du groupe de machines virtuelles identiques. Notez que les diagnostics de démarrage doivent être activés sur une machine virtuelle pour la console série puisse fonctionner.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Désactiver les niveaux de machine virtuelle/groupe de machines virtuelles identiques
La console série peut être désactivée pour une machine virtuelle ou un groupe de machines virtuelles identiques spécifique en désactivant les paramètres de diagnostics de démarrage. Désactivez les diagnostics de démarrage à partir du Portail Azure afin de désactiver la console série pour la machine virtuelle ou le groupe de machines virtuelles identiques. Si vous utilisez la console série sur un groupe de machines virtuelles identiques, assurez-vous de mettre à niveau les instances de groupe de machines virtuelles identiques vers les modèles les plus récents.

> [!NOTE]
> Afin d’activer ou de désactiver la console série pour un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Ces autorisations incluent les rôles administrateur et propriétaire. Des rôles personnalisés peuvent aussi disposer d’autorisations en écriture.

### <a name="subscription-level-disable"></a>Désactiver au niveau de l’abonnement
La console série peut être désactivée pour un abonnement complet par le biais de l’[appel d’API REST Disable Console](/rest/api/serialconsole/console/disableconsole). Cette action nécessite un accès au niveau collaborateur ou supérieur à l’abonnement. Vous pouvez utiliser la fonctionnalité **Essayez** disponible sur la page de documentation de l’API afin de désactiver et d’activer la console série pour un abonnement. Entrez votre ID d’abonnement pour **subscriptionId**, entrez **par défaut** pour **par défaut**, puis sélectionnez **Exécuter**. Les commandes Azure CLI ne sont pas encore disponibles.

Pour réactiver la console série pour un abonnement, utilisez l’[appel à l’API REST Enable Console](/rest/api/serialconsole/console/enableconsole).

![Essayez l’API REST](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Vous pouvez également utiliser le jeu de commandes Bash ci-dessous dans Cloud Shell afin de désactiver, d’activer et d’afficher l’état désactivé de la console série pour un abonnement :

* Afin d’obtenir l’état désactivé de la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Afin de désactiver la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Afin d’activer la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Sécurité de la console série

### <a name="access-security"></a>Sécurité des accès
L’accès à la console série est limité aux utilisateurs disposant d’un rôle d’accès [Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou d’un rôle supérieur vis-à-vis de la machine virtuelle. Si votre tenant Azure Active Directory nécessite une authentification multifacteur (MFA), l’accès à la console série nécessite également l’authentification MFA, car l’accès de la console série s’effectue par le biais du [portail Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sécurité des canaux
Toutes les données envoyées sur les canaux sont chiffrées.

### <a name="audit-logs"></a>Journaux d’audit
Tous les accès à la console série sont journalisés dans les journaux d’activité [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la machine virtuelle. L’accès à ces journaux d’activité est détenu et contrôlé par l’administrateur de la machine virtuelle Azure.

> [!CAUTION]
> Aucun mot de passe d’accès pour la console n’est journalisé. Toutefois, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ces derniers sont écrits dans les journaux d’activité Diagnostics de démarrage de la machine virtuelle. Ils sont accompagnés de tout autre texte visible dans le cadre de l’implémentation de la fonctionnalité de scrollback de la console série. Ces journaux d’activité sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation du Bureau à distance pour tout élément susceptible d’impliquer des secrets et/ou des informations d’identification personnelle.

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le second utilisateur est connecté.

> [!CAUTION]
> Cet utilisateur n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (par le biais de SIGHUP ou d’un mécanisme similaire) est en cours d’étude. Pour Windows, un délai d’expiration automatique est activé dans la console SAC (Special Administrative Console). Mais pour Linux, vous pouvez configurer le délai d’expiration terminal. Pour ce faire, ajoutez `export TMOUT=600` dans votre fichier *.bash_profile* ou *.profile* pour l’utilisateur avec lequel vous vous connectez habituellement à la console. Ce paramètre met fin à la session au bout de 10 minutes.

## <a name="accessibility"></a>Accessibilité
L’accessibilité est un point central de la console série Azure. C’est pourquoi nous avons veillé à ce que la console série soit entièrement accessible.

### <a name="keyboard-navigation"></a>Navigation au clavier
Utilisez la touche **Tabulation** de votre clavier pour naviguer dans l’interface de la console série à partir du portail Azure. Votre emplacement est mis en surbrillance à l’écran. Pour quitter la fenêtre de la console série, appuyez sur les touches **Ctrl**+**F6** de votre clavier.

### <a name="use-serial-console-with-a-screen-reader"></a>Utiliser la console série avec un lecteur d’écran
La console série comprend une prise en charge intégrée des lecteurs d’écran. Quand le lecteur d’écran est activé, le texte de remplacement du bouton sélectionné est lu à voix haute.

## <a name="errors"></a>Errors
Étant donné que la plupart des erreurs sont temporaires, une nouvelle tentative de connexion peut souvent les corriger. Le tableau suivant présente une liste d’erreurs accompagnées de solutions d’atténuation. Ces erreurs et atténuations s’appliquent aux machines virtuelles et aux instances de groupe de machines virtuelles identiques.

Error                            |   Atténuation
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for *&lt;VMNAME&gt;* . To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle.
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM with the serial console. Ensure you have at least Virtual Machine Contributor role permissions. (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer des autorisations du rôle Contributeur de machine virtuelle au minimum.)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez [Prérequis](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;* . Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez [Prérequis](#prerequisites).
WebSocket est fermé ou n’a pas pu être ouvert. | Vous devrez peut-être autoriser `*.console.azure.com`. Une approche plus détaillée, mais plus longue, consiste à autoriser les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui changent régulièrement.
Une réponse « Interdit » s’est produite lors de l’accès au compte de stockage des diagnostics de démarrage de cette machine virtuelle. | Assurez-vous que les diagnostics de démarrage n’ont pas un pare-feu de compte. Un compte de stockage des diagnostics de démarrage accessible est nécessaire au fonctionnement de la console série.

## <a name="known-issues"></a>Problèmes connus
Nous sommes conscients de certains problèmes avec la console série. Voici une liste de ces problèmes et la procédure d’atténuation associée. Ces problèmes et atténuations s’appliquent aux machines virtuelles et aux instances de groupe de machines virtuelles identiques.

Problème                           |   Atténuation
:---------------------------------|:--------------------------------------------|
L’utilisation de la touche **Entrée** après la bannière de connexion n’entraîne pas l’affichage d’une invite de connexion. | Pour plus d’informations, consultez [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Cela peut se produire si vous exécutez une machine virtuelle personnalisée, une appliance à sécurité renforcée ou une configuration de GRUB qui empêche Linux de se connecter au port série.
Le texte de la console série n’occupe l’écran que partiellement (souvent après l’utilisation d’un éditeur de texte). | Les consoles série ne gèrent pas la négociation sur la taille de fenêtre ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), ce qui signifie qu’aucun signal SIGWINCH ne sera envoyé pour mettre à jour la taille de l’écran et la machine virtuelle ne connaîtra pas la taille de votre terminal. Installez xterm ou un utilitaire similaire pour disposer de la commande `resize`, puis exécutez `resize`.
Le collage de chaînes longues ne fonctionne pas. | La console série limite la longueur des chaînes collées dans le terminal à 2 048 caractères afin d’empêcher toute surcharge de la bande passante du port série.
Serial console ne fonctionne pas avec un pare-feu de compte de stockage. | Serial console, de par sa conception, ne peut pas fonctionner avec des pare-feu de compte de stockage activés sur le compte de stockage des diagnostics de démarrage.
La console série ne fonctionne pas avec un compte de stockage utilisant Azure Data Lake Storage Gen2 avec des espaces de noms hiérarchiques. | Il s’agit d’un problème connu avec les espaces de noms hiérarchiques. Pour atténuer ce problème, assurez-vous que le compte de stockage des diagnostics de démarrage de la machine virtuelle n’est pas créé avec Azure Data Lake Storage Gen2. Cette option peut uniquement être définie durant la création du compte de stockage. Vous devrez peut-être créer un compte de stockage des diagnostics de démarrage distinct sans Azure Data Lake Storage Gen2 pour atténuer ce problème.
Entrée de clavier erratique dans les images SLES BYOS. L’entrée de clavier n’est reconnue que de manière sporadique. | Il s’agit d’un problème avec le package Plymouth. Plymouth ne doit pas être exécuté dans Azure tant que vous n’avez pas besoin d’un écran de démarrage. Plymouth interfère avec la capacité de la plateforme à utiliser la console série. Supprimez Plymouth avec `sudo zypper remove plymouth`, puis redémarrez. Vous pouvez également modifier la ligne du noyau de votre configuration GRUB en ajoutant `plymouth.enable=0` à la fin de la ligne. Vous pouvez le faire en [modifiant l’entrée de démarrage au moment du démarrage](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), ou en modifiant la ligne GRUB_CMDLINE_LINUX dans `/etc/default/grub`, en regénérant GRUB avec `grub2-mkconfig -o /boot/grub2/grub.cfg`, puis en redémarrant la machine.


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q. Comment puis-je envoyer des commentaires ?**

R. Envoyez vos commentaires en créant un problème GitHub sur https://aka.ms/serialconsolefeedback. Vous pouvez également envoyer des commentaires par le biais de azserialhelp@microsoft.com ou dans la catégorie Machine virtuelle de la page https://feedback.azure.com.

**Q. Est-ce que la console série prend en charge l’opération Copier/Coller ?**

R. Oui. Utilisez les combinaisons de touches **Ctrl**+**Maj**+**C** et **Ctrl**+**Maj**+**V** pour copier et coller du texte dans le terminal.

**Q. Puis-je utiliser une console série à la place d’une connexion SSH ?**

R. Même si cela est techniquement possible, la console série est principalement utilisée comme outil de résolution des problèmes, lorsque la connectivité par le biais du protocole SSH n’est pas possible. Nous déconseillons l’utilisation de la console série en remplacement du SSH, et ce, pour les raisons suivantes :

- La console série ne dispose pas d’autant de bande passante que le protocole SSH. Étant donné qu’il s’agit d’une connexion qui convient au texte uniquement, les interactions sollicitant beaucoup l’interface graphique utilisateur seront difficiles.
- L’accès à la console série n’est actuellement possible que par l’utilisation d’un nom d’utilisateur et d’un mot de passe. Comme les clés SSH sont beaucoup plus sécurisées que les combinaisons nom d’utilisateur/mot de passe, il est recommandé, pour des raisons de sécurité, d’utiliser le protocole SSH plutôt que la console série.

**Q. Qui peut activer ou désactiver la console série pour mon abonnement ?**

R. Pour activer ou désactiver la console série au niveau d’un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Les rôles ayant une autorisation en écriture incluent notamment les rôles administrateur et propriétaire. Des rôles personnalisés peuvent aussi disposer d’autorisations en écriture.

**Q. Qui peut accéder à la console série pour ma machine virtuelle/mon groupe de machines virtuelles identiques ?**

R. Vous devez disposer du rôle Contributeur de machine virtuelle ou d’un rôle supérieur vis-à-vis d’une machine virtuelle ou d’un groupe de machines virtuelles identiques pour accéder à la console série.

**Q. Ma console série n’affiche rien. Que dois-je faire ?**

R. Votre image est probablement mal configurée pour l’accès à la console série. Pour plus d’informations sur la configuration de votre image afin d’activer la console série, consultez [Disponibilité de distributions Linux pour la console série](#serial-console-linux-distribution-availability).

**Q. La console série est-elle disponible pour les groupes de machines virtuelles identiques ?**

R. Oui, c’est le cas. Consultez [Console série pour les groupes de machines virtuelles identiques](#serial-console-for-virtual-machine-scale-sets)

**Q. Si je configure ma machine virtuelle ou mon groupe de machines virtuelles identiques en utilisant uniquement une authentification par clé SSH, puis-je toujours utiliser la console série pour me connecter à ma machine virtuelle/à mon groupe de machines virtuelles identiques ?**

R. Oui. Comme la console série ne nécessite pas de clés SSH, il vous suffit de configurer une combinaison nom d’utilisateur/mot de passe. Vous pouvez le faire en sélectionnant **Réinitialiser le mot de passe** dans le portail Azure et en utilisant ces informations d’identification pour vous connecter à la console série.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez la console série pour [accéder au GRUB et au mode mono-utilisateur](serial-console-grub-single-user-mode.md).
* Utilisez la console série pour les [appels SysRq et NMI](serial-console-nmi-sysrq.md).
* Découvrez comment utiliser la console série pour [activer le GRUB dans plusieurs distributions](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* La console série est également disponible pour les [machines virtuelles Windows](../windows/serial-console.md).
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md).

