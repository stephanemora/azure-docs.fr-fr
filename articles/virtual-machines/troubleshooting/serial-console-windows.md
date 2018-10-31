---
title: Console série de machine virtuelle Azure | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: harijay
ms.openlocfilehash: facd9be037894932e516e8294e36b6b0e55374c8
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024411"
---
# <a name="virtual-machine-serial-console"></a>Console série de machine virtuelle


La console série de machine virtuelle Azure permet aux machines virtuelles Windows d’accéder à une console texte. Cette connexion série est effectuée via le port série COM1 de la machine virtuelle. Elle fournit l’accès à la machine virtuelle et n’est pas liée au réseau de la machine virtuelle ni à l’état du système d’exploitation. Pour une machine virtuelle, l’accès à la console série n’est possible que via le portail Azure. De plus, seuls les utilisateurs disposant d’un rôle de Contributeur (ou supérieur) pour cette machine virtuelle sont autorisés à accéder à la console. 

Pour obtenir la documentation sur la console série pour les machines virtuelles Linux, [cliquez ici](serial-console-linux.md).

> [!NOTE] 
> La console série pour les machines virtuelles est généralement disponible dans les régions Azure globales. À ce jour, la console série n’est pas encore disponible dans les clouds Azure Government, ni dans les clouds Azure - Chine.

 

## <a name="prerequisites"></a>Prérequis 

* Vous devez utiliser le modèle de déploiement de gestion des ressources. Les déploiements classiques ne sont pas pris en charge. 
* L’option [Diagnostics de démarrage](boot-diagnostics.md) doit être activée dans la machine virtuelle. 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Le compte qui utilise la console série doit disposer du [rôle Contributeur](../../role-based-access-control/built-in-roles.md) pour la machine virtuelle et pour le compte de stockage avec [diagnostics de démarrage](boot-diagnostics.md). 
* La machine virtuelle pour laquelle vous accédez à la console série doit également avoir un compte avec mot de passe. Vous pouvez en créer un avec la fonctionnalité [Réinitialiser le mot de passe](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de l’extension d’accès aux machines virtuelles (voir la capture d’écran ci-dessous).

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Bien démarrer avec la console série
Pour les machines virtuelles, la console série est accessible uniquement via le [portail Azure](https://portal.azure.com). Voici les étapes permettant aux machines virtuelles d’accéder à la console série via le portail :

  1. Ouvrez le portail Azure
  2. Dans le menu de gauche, sélectionnez Machines virtuelles.
  3. Cliquez sur la machine virtuelle de votre choix dans la liste. La page de présentation de la machine virtuelle s’ouvre.
  4. Faites défiler jusqu’à la section Support + dépannage, puis cliquez sur l’option « Console série ». Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

## <a name="enable-serial-console-in-custom-or-older-images"></a>Activer la console série dans les images personnalisées ou anciennes
La [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) est activée par défaut dans les nouvelles images Windows Server sur Azure. La console SAC est prise en charge sur les versions serveur de Windows, mais elle n’est pas disponible sur les versions client (par exemple Windows 10, Windows 8 ou Windows 7). Pour activer la console série dans les machines virtuelles Windows créées avant février 2018, effectuez les étapes suivantes : 

1. Connectez-vous à la machine virtuelle Windows via le Bureau à distance
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Redémarrez le système pour activer la console SAC

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si nécessaire, la console SAC peut aussi être activée hors connexion :

1. Sélectionnez le disque Windows pour lequel vous souhaitez configurer la console SAC et attachez-le en tant que disque de données à la machine virtuelle existante. 
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>Comment savoir si la console SAC est activée ?

Si la [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) n’est pas activée, la console série n’affiche pas l’invite pour la console SAC. Parfois, les informations d’intégrité de la machine virtuelle s’affichent, et d’autres fois non. Si vous utilisez une image Windows Server créée avant février 2018, la console SAC n’est probablement pas activée.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Activer le menu de démarrage Windows dans la console série 

Si vous souhaitez que les invites de commandes du chargeur de démarrage Windows s’affichent dans la console série, vous pouvez ajouter les options supplémentaires suivantes à vos données de configuration de démarrage. Pour plus d’informations, consultez [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Connectez-vous à la machine virtuelle Windows via le Bureau à distance
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes : 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 10`
* `bcdedit /set {bootmgr} bootems yes`
3. Redémarrer le système pour activer le menu de démarrage

> [!NOTE] 
> Le délai d’expiration que vous avez défini pour le menu du gestionnaire de démarrage à afficher aura un impact sur le temps de démarrage de votre système d’exploitation. Tandis que certains jugent acceptable d’ajouter un délai d’expiration de 10 secondes pour s’assurer que le gestionnaire de démarrage est visible via la console série, d’autres préféreront un délai d’expiration plus court ou plus long. Définissez la valeur du délai d’expiration qui vous convient le mieux.

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Utiliser la console série pour les appels NMI dans les machines virtuelles Windows
Une interruption non masquable (NMI) est conçue pour créer un signal que les logiciels sur une machine virtuelle n’ignoreront pas. À l’origine, les NMI ont été utilisées pour surveiller les problèmes matériels sur les systèmes nécessitant des temps de réponse spécifiques.  Aujourd’hui, les programmeurs et les administrateurs système utilisent souvent les NMI comme mécanisme de débogage ou de dépannage des systèmes qui ne réponde pas.

La console série peut être utilisée pour envoyer une NMI à une machine virtuelle Azure à l’aide de l’icône de clavier dans la barre de commandes ci-dessous. Une fois que la NMI est remise, la configuration de machine virtuelle contrôle le mode de réponse du système. Windows peut être configuré pour se bloquer et créer un vidage mémoire lors de la réception d’une NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Pour plus d’informations sur la configuration de Windows pour créer un vidage sur incident lorsqu’il reçoit une NMI, consultez la section [Comment générer un fichier complet de vidage sur incident ou un fichier complet de vidage sur incident du noyau à l’aide d’une NMI sur un système Windows](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="open-cmd-or-powershell-in-serial-console"></a>Ouvrir CMD ou Powershell dans la console série

1. Connectez-vous à la console série. Si vous êtes connecté à la console série, vous pouvez voir **SAC>** comme le montre la capture d’écran suivante :

    ![Se connecter à la console SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

3.  Tapez `cmd` pour créer un canal qui a une instance CMD. 
4.  Tapez `ch -si 1` pour basculer vers le canal qui exécute l’instance CMD. 
5.  Appuyez sur Entrée et saisissez vos informations d’identification ayant une autorisation d’administration.
6.  Après avoir entré des informations d’identification valides, l’instance CMD s’ouvre.
7.  Pour démarrer une instance PowerShell, tapez `PowerShell` dans l’instance CMD, puis appuyez sur Entrée. 

    ![Ouvrir l’instance PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)


## <a name="disable-serial-console"></a>Désactiver la console série
Par défaut, tous les abonnements ont accès à la console série pour toutes les machines virtuelles. Vous pouvez désactiver la console série au niveau de l’abonnement ou au niveau de la machine virtuelle.

> [!NOTE]       
> Pour activer ou désactiver la console série pour un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Cela inclut, mais sans s’y limiter, les rôles d’administrateur ou de propriétaire. Les rôles personnalisés peuvent également avoir des autorisations en écriture.

### <a name="subscription-level-disable"></a>Désactiver au niveau de l’abonnement
La console série peut être désactivée pour un abonnement entier par le biais de [l’appel à l’API REST Disable Console](https://aka.ms/disableserialconsoleapi). Vous pouvez utiliser la fonctionnalité « Essayez » disponible sur la page de documentation de l’API afin de désactiver et activer la console série pour un abonnement. Entrez votre `subscriptionId`, « valeur par défaut » dans le champ `default`, puis cliquez sur Exécuter. Les commandes Azure CLI seront disponibles à une date ultérieure. [Essayez l’appel à l’API REST ici](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Vous pouvez également utiliser le jeu de commandes ci-dessous dans Cloud Shell (commandes bash indiquées) pour désactiver, activer et afficher l’état désactivé de la console série pour un abonnement. 

* Pour obtenir l’état désactivé de la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Pour désactiver la console série pour un abonnement :
    ```azurecli-interactive 
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Pour activer la console série pour un abonnement :
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Désactiver au niveau de la machine virtuelle
La console série peut être désactivée pour certaines machines virtuelles, en désactivant le paramètre de diagnostics de démarrage. Désactivez simplement les diagnostics de démarrage à partir du portail Azure et la console série sera désactivée pour la machine virtuelle.

## <a name="serial-console-security"></a>Sécurité de la console série 

### <a name="access-security"></a>Sécurité des accès 
L’accès à la console série est limité aux utilisateurs qui disposent du rôle [Contributeur](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) (ou supérieur) pour la machine virtuelle. Si votre locataire AAD nécessite l’authentification MFA, l’accès à la console série nécessite également l’authentification MFA, puisque son accès s’effectue via le [portail Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sécurité des canaux
Toutes les données envoyées sur les canaux sont chiffrées.

### <a name="audit-logs"></a>Journaux d’audit
Tous les accès à la console série sont journalisés dans les journaux [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la machine virtuelle. L’accès à ces journaux est détenu et contrôlé par l’administrateur de la machine virtuelle Azure.  

>[!CAUTION] 
Même si les mots de passe d’accès à la console ne sont pas journalisés, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ces derniers seront écrits dans les journaux Diagnostics de démarrage de la machine virtuelle, avec tout autre texte visible, dans le cadre de l’implémentation de la fonctionnalité de défilement de la console série. Ces journaux sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation du Bureau à distance pour tout élément pouvant impliquer des secrets et/ou des informations d’identification personnelle. 

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le deuxième utilisateur est connecté. Le premier utilisateur quitte la console pendant que le nouvel utilisateur s’installe, en quelque sorte.

>[!CAUTION] 
Cela signifie donc que l’utilisateur qui laisse sa place n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (via SIGHUP ou autre mécanisme similaire) est actuellement étudiée. Pour Windows, un délai d’expiration automatique est activé dans la console SAC. Mais pour Linux, vous pouvez configurer un délai d’expiration terminal. 

## <a name="common-scenarios-for-accessing-serial-console"></a>Scénarios courants pour l’accès à la console série 
Scénario          | Actions à effectuer dans la console série                
:------------------|:-----------------------------------------
Règles de pare-feu incorrectes | Accès à la console série et correction des règles de pare-feu Windows. 
Contrôle de la corruption du système de fichiers | Accès à la console série et récupération du système de fichiers. 
Problèmes de configuration RDP | Accès à la console série et modification des paramètres. Pour démarrer, accédez à la [documentation RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Système de verrouillage du réseau| Accès à la console série via le portail de gestion du système. Certaines commandes réseau sont listées dans la [documentation de CMD et PowerShell dans la console série](serial-console-cmd-ps-commands.md). 
Interaction avec le chargeur de démarrage | Accès à BCD via la console série. Pour démarrer, consultez [Activation du menu de démarrage à afficher dans la console série](#enabling-boot-menu-to-show-in-the-serial-console). 

## <a name="accessibility"></a>Accessibilité
L’accessibilité est un point central de la console série Azure. Nous avons donc fait en sorte que la console série soit accessible aux personnes présentant des déficiences visuelles ou auditives, ainsi qu’aux personnes qui ne peuvent pas utiliser une souris.

### <a name="keyboard-navigation"></a>Navigation au clavier
Utilisez la touche `tab` de votre clavier pour naviguer dans l’interface de la console série, à l’intérieur du portail Azure. Votre emplacement est mis en surbrillance à l’écran. Pour quitter le panneau de la console série, appuyez sur `Ctrl + F6` sur votre clavier.

### <a name="use-serial-console-with-a-screen-reader"></a>Utiliser la console série avec un lecteur d’écran
La console série comprend une prise en charge intégrée des lecteurs d’écran. Quand le lecteur d’écran est activé, le texte de remplacement du bouton sélectionné est lu à voix haute.

## <a name="errors"></a>Errors
La plupart des erreurs sont de nature temporaire et peuvent être corrigées par une nouvelle tentative de connexion. Le tableau ci-dessous présente une liste d’erreurs accompagnées de mesures de prévention.

Error                            |   Atténuation 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>'. To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle. 
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM serial console. Ensure you have at least VM Contributor role permissions (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer du rôle Contributeur pour accéder à la console sur cette machine virtuelle)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
Une réponse « Interdit » s’est produite lors de l’accès au compte de stockage des diagnostics de démarrage de cette machine virtuelle. | Assurez-vous que les diagnostics de démarrage n’ont pas un pare-feu de compte. Un compte de stockage des diagnostics de démarrage accessible est nécessaire au fonctionnement de la console série.
WebSocket est fermé ou n’a pas pu être ouvert. | Vous devrez peut-être autoriser `*.console.azure.com`. Une approche plus détaillée, mais plus longue, consiste à autoriser les [plages IP du centre de données Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653), qui changent régulièrement.
Seules les informations d’intégrité sont affichées lors de la connexion à une machine virtuelle Windows| Ce message s’affiche si la console d’administration spéciale n’a pas été activée pour votre image Windows. Consultez l’article [Accéder à la console série pour Windows](#access-serial-console-for-windows) afin d’obtenir des instructions sur la procédure d’activation manuelle de la console SAC sur votre machine virtuelle Windows. Vous trouverez plus de détails dans l’article [Signaux d’intégrité de Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problèmes connus 
Nous sommes conscients de certains problèmes avec la console série. Voici une liste de ces problèmes et la procédure d’atténuation.

Problème                             |   Atténuation 
:---------------------------------|:--------------------------------------------|
L’invite de connexion ne s’affiche pas lorsque vous appuyez sur la touche Entrée après l’affichage de la bannière de connexion | consultez cette page : [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Cela peut se produire si vous exécutez une machine virtuelle personnalisée, une appliance à sécurité renforcée ou une configuration de démarrage qui fait que Windows ne parvient pas à se connecter correctement au port série. Ce phénomène peut également avoir lieu si vous exécutez une machine virtuelle cliente Windows 10, étant donné que seules les machines virtuelles Windows Server sont configurées pour avoir EMS activé.
Impossible de saisir des données sur l’invite de commandes de la console SAC si le débogage du noyau est activé | Établissez une connexion RDP vers la machine virtuelle et exécutez `bcdedit /debug {current} off` à partir d’une invite de commandes avec élévation de privilèges. Si vous ne pouvez pas établir de connexion RDP, vous pouvez joindre le disque du système d’exploitation à une autre machine virtuelle Azure et le modifier lorsqu’il est joint en tant que disque de données à l’aide de `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, puis rebasculer le disque.
Coller du contenu dans PowerShell sous la console SAC génère un troisième caractère si le contenu d’origine contenait un caractère répété | Une solution de contournement consiste à décharger le module PSReadLine à partir de la session active. Exécutez `Remove-Module PSReadLine` pour décharger le module PSReadLine à partir de la session active : cette action ne va pas supprimer ni désinstaller le module.
Certaines entrées de clavier produisent une sortie étrange de la console SAC (par exemple, `[A`, `[3~`) | Les séquences d’échappement [VT100](https://aka.ms/vtsequences) ne sont pas prises en charge par l’invite de la console SAC.
Le collage de chaînes très longues ne fonctionne pas | La console série limite la longueur des chaînes collées dans le terminal à 2 048 caractères. Cela vise à empêcher une surcharge de la bande passante du port série.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

**Q. Comment puis-je envoyer des commentaires ?**

R. Pour nous contacter au sujet d’un problème, accédez à la page https://aka.ms/serialconsolefeedback (méthode conseillée). Vous pouvez également envoyer des commentaires via azserialhelp@microsoft.com ou dans la catégorie Machine virtuelle de la page http://feedback.azure.com

**Q. Est-ce que la console série prend en charge l’opération Copier/Coller ?**

R. Oui, c’est le cas. Utilisez Ctrl+Maj+C et Ctrl+Maj+V pour copier et coller du texte dans le terminal.

**Q. Qui peut activer ou désactiver la console série pour mon abonnement ?**

R. Pour activer ou désactiver la console série au niveau d’un abonnement, vous devez disposer des autorisations en écriture sur l’abonnement. Les rôles ayant une autorisation en écriture incluent, mais de façon non limitative, les rôles d’administrateur ou de propriétaire. Les rôles personnalisés peuvent également avoir des autorisations en écriture.

**Q. Qui peut accéder à la console série pour ma machine virtuelle ?**

R. Vous devez avoir un accès de niveau contributeur ou supérieur à une machine virtuelle afin d’accéder à sa console série. 

**Q. Ma console série n’affiche rien, que dois-je faire ?**

R. Votre image est probablement mal configurée pour l’accès à la console série. Consultez la section [Activer la console série dans les images personnalisées ou anciennes](#enable-serial-console-in-custom-or-older-images) pour plus d’informations sur la configuration de votre image pour activer la console série.

**Q. La console série est-elle disponible pour Virtual Machine Scale Sets ?**

R. À ce stade, l’accès à la console série n’est pas pris en charge pour les instances de groupe de machines virtuelles identiques.

## <a name="next-steps"></a>Étapes suivantes
* Pour des instructions détaillées sur les commandes CMD et PowerShell que vous pouvez utiliser dans la console SAC Windows, cliquez [ici](serial-console-cmd-ps-commands.md).
* La console série est également disponible pour les machines virtuelles [Linux](serial-console-linux.md).
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md).