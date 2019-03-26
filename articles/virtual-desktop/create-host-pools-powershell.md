---
title: Créer un pool d’hôte de Windows Virtual Desktop Preview avec PowerShell - Azure
description: Comment créer un pool d’hôte dans la version préliminaire de bureau virtuel Windows avec les applets de commande PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: bd46e5f7428bab58508521b2c7d4d7cca25d689b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439071"
---
# <a name="create-a-host-pool-with-powershell"></a>Créer un pool d’hôtes avec PowerShell

Les pools d’hôte sont une collection d’un ou plusieurs machines virtuelles identiques au sein d’environnements de client Windows Virtual Desktop Preview. Chaque pool de l’hôte peut contenir un groupe d’application que les utilisateurs peuvent interagir avec comme ils le feraient sur un ordinateur de bureau physique.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Utilisez votre client PowerShell pour créer un pool de l’hôte

Tout d’abord, [télécharger et importer le module PowerShell de bureau virtuel Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell si vous n’avez pas déjà.

Exécutez l’applet de commande suivante pour vous connecter à l’environnement de bureau virtuel Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Après cela, exécutez l’applet de commande suivante pour définir le contexte à votre groupe de client. Si vous n’avez pas le nom du groupe de client, votre client étant probablement dans la valeur par défaut locataire « groupe », vous pouvez ignorer cette applet de commande.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Ensuite, exécutez cette applet de commande pour créer un nouveau pool d’hôte dans votre client de bureau virtuel Windows :

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Exécutez l’applet de commande suivante pour créer un jeton d’inscription pour autoriser un hôte de session rejoignent le pool de l’hôte et l’enregistrer dans un nouveau fichier sur votre ordinateur local. Vous pouvez spécifier la durée pendant laquelle le jeton d’inscription n’est valide en utilisant le paramètre - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Après cela, exécutez la commande suivante pour ajouter des utilisateurs Azure Active Directory au groupe d’application de bureau par défaut pour le pool de l’hôte.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Le **Add-RdsAppGroupUser** applet de commande ne prend pas en charge l’ajout de groupes de sécurité et ajoute uniquement un seul utilisateur à la fois pour le groupe de l’application. Si vous souhaitez ajouter plusieurs utilisateurs à l’app group, réexécutez l’applet de commande avec les noms de principal d’utilisateur approprié.

Exécutez l’applet de commande suivante pour exporter le jeton d’inscription à une variable, vous utiliserez plus tard dans [inscrire les machines virtuelles au pool hôte bureau virtuel Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Créer des machines virtuelles pour le pool de l’hôte

Vous pouvez maintenant créer une machine virtuelle Azure qui peut être jointe à votre pool d’hôte de bureau virtuel Windows.

Vous pouvez créer une machine virtuelle de plusieurs façons :

- [Créer une machine virtuelle à partir d’une image de galerie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Créer une machine virtuelle à partir d’une image managée](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Créer une machine virtuelle à partir d’une image non managée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Préparer les ordinateurs virtuels pour les installations d’agent Windows Virtual Desktop Preview

Vous devez effectuer les opérations suivantes pour préparer vos machines virtuelles avant de pouvoir installer les agents de bureau virtuel Windows et inscrire les machines virtuelles à votre pool d’hôte de bureau virtuel Windows :

- Vous devez la machine de jonction de domaine. Ainsi, les utilisateurs entrants de bureau virtuel Windows à être mappées à partir de leur compte Azure Active Directory à leur compte Active Directory et a été autorisé à accéder à la machine virtuelle.
- Vous devez installer le rôle hôte de Session de bureau à distance (RDSH) si la machine virtuelle exécute un système d’exploitation de Windows Server. Le rôle RDSH permet aux agents bureau virtuel Windows à l’installation.

Pour correctement jonction de domaine, procédez comme suit sur chaque machine virtuelle :

1. [Se connecter à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez fourni lors de la création de la machine virtuelle.
2. Sur la machine virtuelle, lancez **le panneau de configuration** et sélectionnez **système**.
3. Sélectionnez **nom de l’ordinateur**, sélectionnez **modifier les paramètres**, puis sélectionnez **modification...**
4. Sélectionnez **domaine** , puis entrez le domaine Active Directory sur le réseau virtuel.
5. S’authentifier avec un compte de domaine qui dispose de privilèges pour les machines de jonction de domaine.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Inscrire les machines virtuelles au pool hôte Windows Virtual Desktop Preview

Enregistrer les ordinateurs virtuels à un pool d’hôte de bureau virtuel Windows est aussi simple que l’installation des agents de bureau virtuel Windows.

Pour inscrire les agents de bureau virtuel de Windows, procédez comme suit sur chaque machine virtuelle :

1. [Se connecter à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez fourni lors de la création de la machine virtuelle.
2. Téléchargez et installez l’Agent de bureau virtuel Windows.
   - Téléchargez le [Agent bureau virtuel Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Cliquez sur le programme d’installation téléchargé, sélectionnez **propriétés**, sélectionnez **Unblock**, puis sélectionnez **OK**. Cela permettra à votre système d’approuver le programme d’installation.
   - Exécutez le programme d’installation. Lorsque le programme d’installation vous demande le jeton d’inscription, entrez la valeur que vous avez obtenu à partir de la **RdsRegistrationInfo d’exportation** applet de commande.
3. Téléchargez et installez le Windows Virtual Desktop Agent du chargeur de démarrage.
   - Téléchargez le [chargeur de démarrage de l’Agent de bureau virtuel Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Cliquez sur le programme d’installation téléchargé, sélectionnez **propriétés**, sélectionnez **Unblock**, puis sélectionnez **OK**. Cela permettra à votre système d’approuver le programme d’installation.
   - Exécutez le programme d’installation.
4. Installer ou activer la pile de côte à côte de bureau virtuel Windows. Les étapes seront différentes selon la version du système d’exploitation utilise la machine virtuelle.
   - Si le système d’exploitation de votre machine virtuelle est Windows Server 2016 :
     - Téléchargez le [pile côte à côte de bureau virtuel Windows](https://go.microsoft.com/fwlink/?linkid=2084270).
     - Cliquez sur le programme d’installation téléchargé, sélectionnez **propriétés**, sélectionnez **Unblock**, puis sélectionnez **OK**. Cela permettra à votre système d’approuver le programme d’installation.
     - Exécutez le programme d’installation.
   - Si le système d’exploitation de votre machine virtuelle est Windows 10 1809 ou version ultérieure ou Windows Server 2019 ou version ultérieure :
     - Téléchargez le [script](https://go.microsoft.com/fwlink/?linkid=2084268) pour activer la pile côte à côte.
     - Cliquez sur le script téléchargé, sélectionnez **propriétés**, sélectionnez **Unblock**, puis sélectionnez **OK**. Cela permettra à votre système d’approuver le script.
     - À partir de la **Démarrer** menu, recherchez Windows PowerShell ISE, faites un clic droit, puis sélectionnez **exécuter en tant qu’administrateur**.
     - Sélectionnez **fichier**, puis **ouvrir...** , puis recherchez le script PowerShell à partir de fichiers téléchargés et ouvrez-le.
     - Sélectionnez le bouton de lecture vert pour exécuter le script.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un pool de l’hôte, vous pouvez le remplir avec RemoteApps. Pour en savoir plus sur la gestion des applications de bureau virtuel de Windows, consultez le didacticiel de groupes d’application gérer.

> [!div class="nextstepaction"]
> [Gérer le didacticiel de groupes d’applications](./manage-app-groups.md)
