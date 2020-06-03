---
title: Gestion de l’agent Azure Arc pour serveurs (préversion)
description: Cet article décrit les différentes tâches de gestion à effectuer en règle générale pendant le cycle de vie de l’agent Connected Machine Azure Arc pour serveurs.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.openlocfilehash: 965e59f9c51cc41d4e5a8e8931b5c2f62c260599
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648095"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Gestion et maintenance de l’agent Connected Machine

Après le déploiement initial de l’agent Connected Machine Azure Arc pour serveurs (préversion) pour Windows ou Linux, il se peut que vous deviez le reconfigurer, le mettre à niveau ou le supprimer de la machine s’il a atteint la phase de mise hors service dans son cycle de vie. Vous pouvez facilement effectuer ces tâches de maintenance de routine manuellement ou automatiquement ce qui réduit les erreurs opérationnelles et les coûts.

## <a name="upgrading-agent"></a>Mise à niveau de l’agent

L’agent Azure Connected Machine pour Windows et Linux peut être mis à niveau vers la dernière version de façon manuelle ou automatique selon vos besoins. Le tableau suivant décrit les méthodes prises en charge pour effectuer la mise à niveau de l’agent.

| Système d’exploitation | Méthode de mise à niveau |
|------------------|----------------|
| Windows | Manuellement<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Agent Windows

Le package de mise à jour de l’agent Connected Machine pour Windows est disponible sur :

* Microsoft Update

* [Catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx)

* [Package Windows Installer de l’agent pour Windows](https://aka.ms/AzureConnectedMachineAgent) à partir du centre de téléchargement Microsoft.

L’agent peut être mis à jour à l’aide de différentes méthodes pour prendre en charge votre processus de gestion des mises à jour logicielles. À part l’obtention depuis Microsoft Update, vous pouvez également procéder au téléchargement et à l’exécution manuels à partir de l’invite de commandes, d’un script ou d’une autre solution d’automatisation, ou à partir de l’Assistant interface utilisateur en exécutant `AzureConnectedMachine.msi`.

> [!NOTE]
> * Pour mettre à niveau l’agent, vous devez disposer d’autorisations *Administrateur*.
> * Pour mettre à niveau manuellement, vous devez tout d’abord télécharger et copier le package Installer dans un dossier sur le serveur cible, ou à partir d’un dossier réseau partagé. 

Si vous n’êtes pas familiarisé avec les options de ligne de commande pour les packages Windows Installer, passez en revue [Options de ligne de commande standard Msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) et [Options de ligne de commande Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Pour effectuer la mise à niveau à l’aide de l’Assistant d’installation

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.

2. Exécutez **AzureConnectedMachineAgent.msi** pour démarrer l’Assistant Installation.

L’Assistant Installation détecte si une version précédente existe, puis effectue automatiquement une mise à niveau de l’agent. Une fois la mise à niveau terminée, l’Assistant Installation se ferme automatiquement.

#### <a name="to-upgrade-from-the-command-line"></a>Pour effectuer la mise à niveau à l’aide de la ligne de commande

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.

2. Pour mettre à niveau l’agent en mode silencieux et créer un fichier journal d’installation dans le dossier `C:\Support\Logs`, exécutez la commande suivante.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agent Linux

La mise à jour de l’agent vers la dernière version sur un ordinateur Linux implique deux commandes. Une commande pour mettre à jour l’index de package local avec la liste des derniers packages disponibles dans les référentiels, et une commande pour mettre à niveau le package local.

Vous pouvez télécharger le package d’agent le plus récent à partir du [référentiel de packages](https://packages.microsoft.com/) de Microsoft.

> [!NOTE]
> Pour mettre à niveau l’agent, vous devez disposer d’autorisations d’accès *racine* ou d’un compte avec des droits élevés utilisant Sudo.

#### <a name="upgrade-ubuntu"></a>Mettre à niveau Ubuntu

1. Pour mettre à jour l’index de package local avec les dernières modifications apportées aux référentiels, exécutez la commande suivante :

    ```bash
    apt update
    ```

2. Pour mettre à niveau votre système, exécutez la commande suivante :

    ```bash
    apt upgrade
    ```

Les actions de la commande [apt](https://help.ubuntu.com/lts/serverguide/apt.html), telles que l’installation et la suppression de packages, sont consignées dans le fichier journal `/var/log/dpkg.log`.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Mettre à niveau Red Hat/CentOS/Amazon Linux

1. Pour mettre à jour l’index de package local avec les dernières modifications apportées aux référentiels, exécutez la commande suivante :

    ```bash
    yum check-update
    ```

2. Pour mettre à niveau votre système, exécutez la commande suivante :

    ```bash
    yum update
    ```

Les actions de la commande [yum](https://access.redhat.com/articles/yum-cheat-sheet), telles que l’installation et la suppression de packages, sont consignées dans le fichier journal `/var/log/yum.log`. 

#### <a name="upgrade-suse-linux-enterprise"></a>Mettre à niveau SUSE Linux Enterprise

1. Pour mettre à jour l’index de package local avec les dernières modifications apportées aux référentiels, exécutez la commande suivante :

    ```bash
    zypper refresh
    ```

2. Pour mettre à niveau votre système, exécutez la commande suivante :

    ```bash
    zypper update
    ```

Les actions de la commande [zypper](https://en.opensuse.org/Portal:Zypper), telles que l’installation et la suppression de packages, sont consignées dans le fichier journal `/var/log/zypper.log`. 

## <a name="about-the-azcmagent-tool"></a>À propos de l’outil Azcmagent

L’outil Azcmagent (Azcmagent.exe) sert à configurer l’agent Azure Connected Machine d’Azure Arc pour serveurs (préversion) en cours d’installation, ou à modifier la configuration initiale de l’agent après installation. Azcmagent.exe fournit des paramètres de ligne de commande pour personnaliser l’agent et afficher son état :

* **Connect** : pour connecter la machine à Azure Arc.

* **Disconnect** : pour déconnecter la machine d’Azure Arc.

* **Reconnect** : pour reconnecter une machine déconnectée à Azure Arc.

* **Show** : afficher l’état et les propriétés de configuration de l’agent (nom du groupe de ressources, ID d’abonnement, version, etc.), qui peuvent être utiles lors de la résolution d’un problème avec l’agent.

* **-h ou--Help** : afficher les paramètres de ligne de commande disponibles.

    Par exemple, pour obtenir une aide détaillée sur le paramètre **Reconnect**, tapez `azcmagent reconnect -h`. 

* **-v ou--verbose** : activer la journalisation détaillée.

Vous pouvez effectuer une opération **Connect**, **Disconnect** ou **Reconnect** manuellement quand vous êtes connecté de manière interactive, l’automatiser en utilisant le principal de service utilisé pour intégrer plusieurs agents, ou avec un [jeton d’accès](../../active-directory/develop/access-tokens.md) de plateforme d’identité Microsoft. Si vous n’avez pas utilisé de principal de service pour inscrire la machine auprès d’Azure Arc pour serveurs (préversion), consultez l’[article](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) suivant pour créer un principal de service.

### <a name="connect"></a>Se connecter

Ce paramètre spécifie une ressource dans Azure Resource Manager représentant la machine créée dans Azure. La ressource se trouve dans l’abonnement et le groupe de ressources spécifiés, et les données relatives à la machine sont stockées dans la région Azure spécifiée par le paramètre `--location`. Le nom de ressource par défaut est le nom d’hôte de cette machine s’il n’est pas spécifié.

Un certificat correspondant à l’identité attribuée par le système de cet machine est ensuite téléchargé, puis stocké localement. Après cette étape, l’Instance Metadata Service d’Azure Connected Machine et l’agent de configuration invité commencent la synchronisation avec Azure Arc pour serveurs (préversion).

Pour vous connecter à un principal de service, exécutez la commande suivante :

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Pour vous connecter à l’aide d’un jeton d’accès, exécutez la commande suivante :

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Pour vous connecter à l’aide de vos informations d’identification d’ouverture de session avec privilèges élevés (interactives), exécutez la commande suivante :

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Déconnecter

Ce paramètre spécifie une ressource dans Azure Resource Manager, indiquant que la machine est supprimée dans Azure. Il ne supprime pas l’agent de la machine. Cette opération doit être effectuée en tant qu’étape distincte. Une fois la machine déconnectée, si vous souhaitez la réinscrire auprès d’Azure Arc pour serveurs (préversion), utilisez `azcmagent connect` afin qu’une nouvelle ressource soit créée pour elle dans Azure.

Pour vous déconnecter à l’aide d’un principal du service, exécutez la commande suivante :

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Pour vous déconnecter à l’aide d’un jeton d’accès, exécutez la commande suivante :

`azcmagent disconnect --access-token <accessToken>`

Pour vous déconnecter à l’aide de vos informations d’identification d’ouverture de session avec privilèges élevés (interactives), exécutez la commande suivante :

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Reconnexion

Ce paramètre reconnecte la machine déjà inscrite ou connectée avec Azure Arc pour serveurs (préversion). Cela peut s’avérer nécessaire si la machine a été mise hors tension au moins 45 jours avant que son certificat expire. Cette commande utilise les options d’authentification fournies pour récupérer de nouvelles informations d’identification correspondant à la ressource Azure Resource Manager représentant cet machine.

Cette commande requiert des autorisations plus élevées que le rôle[Intégration d’ordinateurs connectés à Azure](agent-overview.md#required-permissions).

Pour vous reconnecter à un principal de service, exécutez la commande suivante :

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Pour vous reconnecter à l’aide d’un jeton d’accès, exécutez la commande suivante :

`azcmagent reconnect --access-token <accessToken>`

Pour vous reconnecter à l’aide de vos informations d’identification d’ouverture de session avec privilèges élevés (interactives), exécutez la commande suivante :

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Supprimer l’agent

Utilisez l’une des méthodes suivantes pour désinstaller l’agent Connected Machine Windows ou Linux de l’ordinateur. La suppression de l’agent ne désinscrit pas l’ordinateur avec Arc pour serveurs (préversion). Il s’agit d’un processus distinct que vous effectuez quand vous n’avez plus besoin de gérer l’ordinateur dans Azure.

### <a name="windows-agent"></a>Agent Windows

Les deux méthodes suivantes suppriment l’agent, mais pas le dossier *C:\Program Files\AzureConnectedMachineAgent* sur l’ordinateur.

#### <a name="uninstall-from-control-panel"></a>Désinstaller à partir du Panneau de configuration

1. Pour désinstaller l’agent Windows de la machine, procédez comme suit :

    a. Connectez-vous à la machine avec un compte disposant des autorisations d’administration.  
    b. Dans **Panneau de configuration**, sélectionnez **Programmes et fonctionnalités**.  
    c. Dans **Programmes et fonctionnalités**, sélectionnez **Agent Azure Connected Machine Agent**, **Désinstaller**, puis **Oui**.  

    >[!NOTE]
    > Vous pouvez également exécuter l’Assistant Installation de l’agent en double-cliquant sur le package d’installation **AzureConnectedMachineAgent.msi**.

#### <a name="uninstall-from-the-command-line"></a>Désinstaller à partir de la ligne de commande

Pour désinstaller l’agent manuellement à partir de l’invite de commandes ou pour utiliser une méthode automatisée, telle qu’un script, vous pouvez utiliser l’exemple suivant. Tout d’abord, vous devez récupérer le code du produit, c’est-à-dire un GUID qui est l’identificateur principal du package d’application, à partir du système d’exploitation. La désinstallation est effectuée à l’aide de la ligne de commande Msiexec.exe - `msiexec /x {Product Code}`.

1. Ouvrez l’Éditeur du Registre.

2. Sous la clé de Registre `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`, recherchez et copiez le GUID du code du produit.

3. Vous pouvez alors désinstaller l’agent à l’aide de Msiexec en utilisant les exemples suivants :

   * À partir du type de la ligne de commande :

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Vous pouvez effectuer les mêmes étapes en utilisant PowerShell :

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agent Linux

> [!NOTE]
> Pour désinstaller l’agent, vous devez disposer d’autorisations d’accès *racine* ou d’un compte avec des droits élevés utilisant Sudo.

Pour désinstaller l’agent Linux, la commande à utiliser dépend du système d’exploitation Linux.

- Pour Ubuntu, exécutez la commande suivante :

    ```bash
    sudo apt purge azcmagent
    ```

- Pour RHEL, CentOS et Amazon Linux, exécutez la commande suivante :

    ```bash
    sudo yum remove azcmagent
    ```

- Pour SLES, exécutez la commande suivante :

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Désinscrire l’ordinateur

Si vous envisagez d’arrêter la gestion de l’ordinateur avec les services de prise en charge dans Azure, procédez comme suit pour désinscrire l’ordinateur avec Arc pour serveurs (préversion). Vous pouvez suivre ces étapes avant ou après la suppression de l’agent Connected Machine de la machine.

1. Ouvrez Azure Arc pour serveurs (préversion) en accédant au [portail Azure](https://aka.ms/hybridmachineportal).

2. Sélectionnez la machine dans la liste, sélectionnez les points de suspension ( **...** ), puis sélectionnez **Supprimer**.

## <a name="update-or-remove-proxy-settings"></a>Mettre à jour ou supprimer des paramètres de proxy

Pour configurer l’agent afin qu’il communique avec le service via un serveur proxy ou pour supprimer cette configuration après le déploiement, utilisez l’une des méthodes suivantes.

### <a name="windows"></a>Windows

Pour définir la variable d’environnement du serveur proxy, exécutez la commande suivante :

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Pour configurer l’agent afin qu’il cesse de communiquer via un serveur proxy, exécutez la commande suivante pour supprimer la variable d’environnement du serveur proxy et redémarrer le service de l’agent :

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Pour définir le serveur proxy, exécutez la commande suivante à partir du répertoire dans lequel vous avez téléchargé le package d’installation de l’agent :

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Pour configurer l’agent afin qu’il cesse de communiquer via un serveur proxy, exécutez la commande suivante pour supprimer la configuration du proxy :

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md), par exemple pour la [configuration invité](../../governance/policy/concepts/guest-configuration.md) des machines virtuelles, pour vérifier que l’ordinateur crée des rapports sur l’espace de travail Log Analytics prévu, pour activer l’analyse d’[Azure Monitor sur des machines virtuelles](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) et bien plus encore.

- En savoir plus sur [l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez superviser de manière proactive le système d’exploitation et les charges de travail en cours d’exécution sur la machine, gérer le système d’exploitation à l’aide de runbooks Automation ou de fonctionnalités comme Update Management ou utiliser d’autres services Azure tels qu’[Azure Security Center](../../security-center/security-center-intro.md).