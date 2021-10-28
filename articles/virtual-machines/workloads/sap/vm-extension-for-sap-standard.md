---
title: Version standard de l’extension de machine virtuelle Azure pour les solutions SAP | Microsoft Docs
description: Découvrez comment déployer l’extension standard de machine virtuelle pour SAP.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: 8e723e39364207e1075d66eeafcdf12b74e16624
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261383"
---
# <a name="standard-version-of-azure-vm-extension-for-sap-solutions"></a>Version standard de l’extension de machine virtuelle Azure pour les solutions SAP
[new-extension]:vm-extension-for-sap-new.md (Nouvelle version de l’extension de machine virtuelle Azure pour les solutions SAP)
[configure-windows]:vm-extension-for-sap-standard.md#cb095b34-51c9-41f3-aeed-30a16072a1dc (Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell)
[configure-linux]:vm-extension-for-sap-standard.md#c691e304-3524-4bfd-8612-992d5715a689 (Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI)
[configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[troubleshoot-windows]:vm-extension-for-sap-standard.md#f566b77e-5427-451b-b4ca-6b93114d51e8 (Résolution des problèmes pour Windows)
[troubleshoot-linux]:vm-extension-for-sap-standard.md#a4dae567-e7fd-4d4b-8279-510b8e5fae4a (Résolution des problèmes pour Linux)
[healthcheck]:vm-extension-for-sap-standard.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Contrôle d’intégrité de la configuration de l’extension Azure pour SAP)
[deployment-guide-4.1]:vm-extension-for-sap-standard.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Déploiement d’applets de commande Azure PowerShell)
[deploy-cli]:vm-extension-for-sap-standard.md#1ded9453-1330-442a-86ea-e0fd8ae8cab3 (Déploiement de l’interface de ligne de commande Azure)
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[azure-cli-2]:/cli/azure/install-azure-cli
[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[readiness-check]:vm-extension-for-sap-standard.md#6b2561a0-b476-4579-847d-6c68117eb26e (Vérification de la préparation)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Ressources SAP)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[deployment-guide-contact-support]:vm-extension-for-sap-standard.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Résolution des problèmes liés à l’extension Azure pour SAP – Contacter le support)
[deployment-guide-run-the-script]:vm-extension-for-sap-standard.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Résolution des problèmes liés à l’extension Azure pour SAP – Exécuter le script d’installation)
[deployment-guide-redeploy-after-sysprep]:vm-extension-for-sap-standard.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Résolution des problèmes liés à l’extension Azure pour SAP – Redéployer après Sysprep)
[deployment-guide-fix-internet-connection]:vm-extension-for-sap-standard.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 (Résolution des problèmes liés à l’extension Azure pour SAP – Réparer la connexion Internet)



## <a name="prerequisites"></a>Prérequis

> [!NOTE]
> Instruction générale de support : La prise en charge de l’extension Azure pour SAP est assurée par le biais des canaux de support SAP.
> Si vous avez besoin d’aide sur l’extension de machine virtuelle Azure pour solutions SAP, veuillez ouvrir un dossier de support auprès du support SAP
  
> [!NOTE]
> Veillez à désinstaller l’extension de machine virtuelle avant de basculer entre la version standard et la nouvelle version de l’extension Azure pour SAP.

> [!NOTE]
> L’extension de machine virtuelle se présente sous deux versions. Cet article traite de la version **standard** de l’extension de machine virtuelle Azure pour SAP. Pour accéder à l’aide concernant l’installation de la nouvelle version, consultez [Nouvelle version de l’extension de machine virtuelle Azure pour les solutions SAP][new-extension].


### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Déploiement d’applets de commande Azure PowerShell

Suivez la procédure décrite dans l’article [Installation du module Azure PowerShell](/powershell/azure/install-az-ps).

Recherchez régulièrement les mises à jour des applets de commande PowerShell, qui sont généralement publiées une fois par mois. Suivez la procédure décrite dans [cet](/powershell/azure/install-az-ps#update-the-azure-powershell-module) article. Sauf indication contraire dans les notes SAP [1928533] ou [2015553], nous vous recommandons d’utiliser la dernière version des applets de commande Azure PowerShell.

Pour vérifier la version des applets de commande Azure PowerShell installées sur votre ordinateur, exécutez cette commande PowerShell :

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Déploiement de l’interface de ligne de commande Azure

Suivez la procédure décrite dans l’article [Installation d’Azure CLI](/cli/azure/install-azure-cli).

Recherchez régulièrement les mises à jour de l’interface de ligne de commande Azure, qui sont généralement publiées une fois par mois.

Pour vérifier la version de l’interface de ligne de commande Azure installée sur votre ordinateur, exécutez cette commande :

```console
az --version
```


## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="cb095b34-51c9-41f3-aeed-30a16072a1dc"></a>Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell 
 
Pour installer l’extension Azure pour SAP à l’aide de PowerShell :

1. Vérifiez que vous avez installé la dernière version de l’applet de commande Azure PowerShell. Pour plus d’informations, consultez [Déploiement de cmdlets Azure PowerShell][deployment-guide-4.1].
1. Exécutez l’applet de commande PowerShell suivant. Pour afficher la liste des environnements disponibles, exécutez la cmdlet `Get-AzEnvironment` . Si vous voulez utiliser la version globale d’Azure, votre environnement est **AzureCloud**. Pour Azure China 21Vianet, sélectionnez **AzureChinaCloud**. 
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   
   Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> 
   ``` 
Une fois que vous avez entré les données de votre compte, le script déploie les extensions requises et active les fonctionnalités nécessaires. Ceci peut prendre plusieurs minutes.
Pour plus d’informations sur `Set-AzVMAEMExtension`, consultez [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Exécution réussie de la cmdlet Azure spécifique à SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

La configuration `Set-AzVMAEMExtension` effectue toutes les étapes de configuration de collecte de données d’hôte pour SAP.

La sortie du script comprend les informations suivantes :

* La confirmation que la collecte de données a été configurée pour le disque du système d’exploitation et tous les autres disques de données.
* Les deux messages suivants confirment la configuration des métriques de stockage pour un compte de stockage spécifique.
* Une des lignes de sortie fournit l’état de la mise à jour réelle de la configuration de l’extension de machine virtuelle pour SAP.
* Une autre ligne de sortie confirme que la configuration a été déployée ou mise à jour.
* La dernière ligne de sortie est informative. Elle répertorie les options permettant de tester la configuration de l’extension de machine virtuelle pour SAP.
* Pour vérifier que toutes les étapes de configuration de l’extension de machine virtuelle Azure pour SAP ont été exécutées correctement et que l’infrastructure Azure fournit les données nécessaires, vérifiez la préparation de l’extension Azure pour SAP, comme décrit dans [Vérification de la préparation][readiness-check].
* Attendez 15 à 30 minutes pour que les diagnostics Azure collectent les données pertinentes.

## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="c691e304-3524-4bfd-8612-992d5715a689"></a>Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI
 
Pour installer l’extension de machine virtuelle Azure pour SAP à l’aide d’Azure CLI :

1. Vérifiez que vous avez installé la version la plus récente d’Azure CLI. Pour plus d’informations, consultez [Déploiement de l’interface de ligne de commande Azure][deploy-cli]. 

1. Connectez-vous à votre compte Azure :
   ```azurecli
   az login
   ```

1. Installez l’extension AEM Azure CLI. Veillez à utiliser la version 0.2.2 ou une version ultérieure.
   ```azurecli
   az extension add --name aem
   ```

1. Activez l’extension : 
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> 
   ```

1. Vérifiez que l’extension Azure pour SAP est active sur la machine virtuelle Azure Linux. Vérifiez si le fichier /var/lib/AzureEnhancedMonitor/PerfCounters existe. S’il existe, à l’invite de commandes, exécutez cette commande pour afficher les informations collectées par l’extension Azure pour SAP :

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Le résultat se présente ainsi :

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Mettre à jour la configuration de l’extension Azure pour SAP

Mettez à jour la configuration de l’extension Azure pour SAP dans les scénarios suivants :
* L’équipe Microsoft/SAP a étendu les capacités de l’extension de machine virtuelle et a décidé d’ajouter ou de supprimer des compteurs.
* Microsoft introduit une nouvelle version de l’infrastructure Azure sous-jacente qui génère les données, et l’extension Azure pour SAP doit s’adapter à ces modifications.
* Vous montez des disques de données supplémentaires sur votre machine virtuelle Azure ou vous supprimez un disque de données. Dans ce scénario, mettez à jour la collection de données liées au stockage. L’ajout ou la suppression de points de terminaison, ou l’affectation d’adresses IP à une machine virtuelle n’affecte pas la configuration de l’extension.
* Vous modifiez la taille de votre machine virtuelle Azure, par exemple en passant de la taille A5 à une autre taille de machine virtuelle.
* Vous ajoutez de nouvelles interfaces de réseau à votre machine virtuelle Azure.

Pour mettre à jour les paramètres, mettez à jour la configuration de l’extension Azure pour SAP en suivant les étapes décrites dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows].

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Vérifications et résolution des problèmes

Une fois que vous avez déployé votre machine virtuelle Azure et configuré l’extension Azure pour SAP appropriée, vérifiez si tous les composants de l’extension fonctionnent correctement.

Exécutez la vérification de la préparation de l’extension Azure pour SAP, comme décrit dans [Vérification de la préparation][readiness-check]. Si tous les résultats de la vérification de disponibilité sont positifs et que tous les compteurs de performances ont le statut OK, l’extension Azure pour SAP a été correctement configurée. Vous pouvez poursuivre l’installation de l’agent hôte SAP, comme décrit dans les notes SAP sous [Ressources SAP][deployment-guide-2.2]. Si la vérification de la préparation indique que des compteurs sont manquants, exécutez le contrôle d’intégrité de l’extension Azure pour SAP, comme décrit dans [Contrôle d’intégrité de la configuration de l’extension Azure pour SAP][healthcheck]. Pour obtenir davantage d’options de résolution des problèmes, consultez [Résolution des problèmes pour Windows][troubleshoot-windows] ou [Résolution des problèmes pour Linux][troubleshoot-linux].

### <a name="readiness-check"></a><a name="6b2561a0-b476-4579-847d-6c68117eb26e"></a>Vérification de la préparation

Cette vérification vous assure que tous les indicateurs de performance affichés au sein de votre application SAP sont fournis par l’extension Azure pour SAP sous-jacente.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Vérifier la disponibilité d’une machine virtuelle Windows
 
1. Connectez-vous à la machine virtuelle Azure (l’utilisation d’un compte Administrateur n’est pas nécessaire).
1. Ouvrez une fenêtre d’invite de commandes.
1. À l’invite de commandes, remplacez le référentiel par le dossier d’installation de l’extension Azure pour SAP : C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop

   La *version* figurant dans le chemin d’accès à l’extension peut varier. Si vous voyez des dossiers pour plusieurs versions de l’extension dans le dossier d’installation, vérifiez la configuration du service Windows AzureEnhancedMonitoring, puis accédez au dossier indiqué en tant que *Chemin d’accès des fichiers exécutables*.

   ![Propriétés du service exécutant l’extension Azure pour SAP][deployment-guide-figure-1000]

1. À l’invite de commandes, exécutez **azperflib.exe** sans aucun paramètre.

   > [!NOTE]
   > Le fichier exécutable azperflib.exe fonctionne en boucle et met à jour les compteurs collectés toutes les 60 secondes. Pour mettre fin à la boucle, fermez la fenêtre d’invite de commandes.
   >
   >

Si l’extension Azure pour SAP n’est pas installée ou que le service AzureEnhancedMonitoring n’est pas en cours d’exécution, cela signifie que l’extension n’a pas été configurée correctement. Pour obtenir des informations détaillées sur la manière de dépanner l’extension, consultez [Résolution des problèmes pour Windows][troubleshoot-windows] ou [Résolution des problèmes pour Linux][troubleshoot-linux].

> [!NOTE]
> Le fichier Azperflib.exe est un composant qui ne peut pas être utilisé pour des besoins propres. Ce composant fournit des données d’infrastructure Azure relatives à la machine virtuelle pour l’agent hôte SAP exclusivement.
> 
##### <a name="check-the-output-of-azperflibexe"></a>Vérifiez la sortie renvoyée par azperflib.exe
 
La sortie de azperflib.exe indique tous les compteurs de performances Azure remplis pour SAP. En bas de la liste des compteurs collectés, vous trouverez un résumé et un indicateur d’intégrité, qui indiquent l’état de l’extension Azure pour SAP.

![Sortie du contrôle d’intégrité effectué avec azperflib.exe indiquant l’absence de problèmes][deployment-guide-figure-1100]
<a name="figure-11"></a>

Vérifiez le résultat renvoyé pour la sortie **Nombre total de compteurs**, qui est indiqué comme étant vide, et pour **État d’intégrité**, comme indiqué à la figure précédente.

Interprétez les valeurs obtenues comme suit :

| Résultats du fichier exécutable azperflib.exe | État d’intégrité de l’extension Azure pour SAP |
| --- | --- |
| **Appels de l’API - non disponibles** | Les compteurs qui ne sont pas disponibles peuvent ne pas être concernés par la configuration de la machine virtuelle ou constituent des erreurs. Voir **État d’intégrité**. |
| **Nombre total de compteurs : vide** |Les deux compteurs suivants de stockage Azure peuvent être vides : <ul><li>Stockage Lecture Op Latence Serveur msec</li><li>Stockage Lecture Op Latence E2E msec</li></ul>Tous les autres compteurs doivent contenir des valeurs. |
| **État d’intégrité** |Uniquement OK si l’état renvoyé est **OK**. |
| **Diagnostics** |Informations détaillées sur l’état d’intégrité. |

Si la valeur **État d’intégrité** n’est pas **OK**, suivez les instructions de [Contrôle d’intégrité de la configuration de l’extension Azure pour SAP][healthcheck].
 
##### <a name="run-the-readiness-check-on-a-linux-vm"></a>Vérifier la disponibilité d’une machine virtuelle Linux

1. Connectez-vous à la machine virtuelle Azure à l’aide de SSH.
1. Vérifiez la sortie de l’extension Azure pour SAP.

   a.  Exécutez `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Résultat attendu** : Renvoie la liste des compteurs de performances. Ce fichier ne doit pas être vide.

   b. Exécutez `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Résultat attendu** : Renvoie une ligne où l’erreur est **none**, par exemple **3;config;Error;;0;0;none;0;1456416792;tst-servercs;**

   c. Exécutez `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Résultat attendu** : Revient vide ou n’existe pas.

Si la vérification précédente a échoué, exécutez ces vérifications supplémentaires :

1. Assurez-vous que waagent est installé et démarré.

   a.  Exécutez `sudo ls -al /var/lib/waagent/`

     **Résultat attendu** : Répertorie le contenu du répertoire waagent.

   b.  Exécutez `ps -ax | grep waagent`

   **Résultat attendu** : Affiche une entrée similaire à : `python /usr/sbin/waagent -daemon`

1. Assurez-vous que l’extension Azure pour SAP est installée et en cours d’exécution.

   a.  Exécutez `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Résultat attendu** : Répertorie le contenu de l’annuaire de l’extension Azure pour SAP.

   b. Exécutez `ps -ax | grep AzureEnhanced`

   **Résultat attendu** : Affiche une entrée similaire à : `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installez l’agent hôte SAP comme décrit dans la note SAP [1031096] et vérifiez le résultat de `saposcol`.

   a.  Exécutez `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Exécutez `dump ccm`

   c.  Vérifiez si la métrique **Virtualization_Configuration\Enhanced Monitoring Access** a la valeur **true**.

Si vous avez déjà installé un serveur d’applications ABAP SAP NetWeaver, ouvrez la transaction ST06 et regardez si l’analyse est activée.

Si l’une de ces vérifications échoue ou pour savoir comment redéployer l’extension, consultez [Résolution des problèmes pour Linux][troubleshoot-linux] ou [Résolution des problèmes pour Windows][troubleshoot-windows].

## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Contrôles d'intégrité

Si certaines des données d’infrastructure ne sont pas fournies correctement, comme indiqué par le test décrit dans [Vérification de la disponibilité][readiness-check], exécutez les contrôles d’intégrité repris dans ce chapitre pour tester si l’infrastructure Azure et l’extension Azure pour SAP sont correctement configurées.

### <a name="health-checks-using-powershell"></a>Contrôles d’intégrité à l’aide de PowerShell

1. Assurez-vous d’avoir installé la dernière version de l’applet de commande Azure PowerShell, comme décrit dans le chapitre [Déploiement d’applets de commande Azure PowerShell][deployment-guide-4.1].
1. Exécutez l’applet de commande PowerShell suivant. Pour afficher la liste des environnements disponibles, exécutez l’applet de commande `Get-AzEnvironment`. Pour utiliser la version globale d’Azure, sélectionnez l’environnement **AzureCloud**. Pour Azure China 21Vianet, sélectionnez **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Le script teste la configuration de la machine virtuelle sélectionnée.

   ![Sortie de test réussi de l’extension Azure pour SAP][deployment-guide-figure-1300]

Assurez-vous que le résultat de chaque vérification d’intégrité est **OK**. Si certaines vérifications n’affichent pas **OK**, exécutez la cmdlet de mise à jour comme décrit dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows]. Attendez 15 minutes, puis répétez les vérifications décrites dans [Vérification de la préparation][readiness-check] et ce chapitre. Si les vérifications indiquent toujours un problème avec certains ou tous les compteurs, consultez [Résolution des problèmes pour Linux][troubleshoot-linux] ou [Résolution des problèmes pour Windows][troubleshoot-windows].

> [!Note]
> Vous pouvez recevoir des avertissements si vous utilisez des disques Azure standard managés. Les tests ne retournent pas « OK » et des avertissements s’affichent. Il s’agit d’un comportement normal et attendu pour ce type de disque. Voir aussi [Résolution des problèmes pour Linux][troubleshoot-linux] ou [Résolution des problèmes pour Windows][troubleshoot-windows].
>

### <a name="health-checks-using-azure-cli"></a>Contrôles d’intégrité à l’aide d’Azure CLI

Pour exécuter le contrôle d’intégrité de l’extension de machine virtuelle Azure pour SAP à l’aide d’Azure CLI :
 
1. Installez [Azure CLI 2.0][azure-cli-2]. Veillez à utiliser au moins la version 2.19.1 ou ultérieure (utilisez la version la plus récente). 
1. Connectez-vous à votre compte Azure :
   ```azurecli
   az login
   ```

1. Installez l’extension AEM Azure CLI. Veillez à utiliser la version 0.2.2 ou une version ultérieure.
   ```azurecli
   az extension add --name aem
   ```

1. Vérifiez l’installation de l’extension : 
   ```azurecliw
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
Le script teste la configuration de la machine virtuelle sélectionnée.

Assurez-vous que le résultat de chaque vérification d’intégrité est **OK**. Si certaines vérifications n’affichent pas **OK**, exécutez la cmdlet de mise à jour comme décrit dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows]. Attendez 15 minutes, puis répétez les vérifications décrites dans [Vérification de la préparation][readiness-check] et ce chapitre. Si les vérifications indiquent toujours un problème avec certains ou tous les compteurs, consultez [Résolution des problèmes pour Linux][troubleshoot-linux] ou [Résolution des problèmes pour Windows][troubleshoot-windows].

## <a name="troubleshooting-for-windows"></a><a name="f566b77e-5427-451b-b4ca-6b93114d51e8"></a>Résolution des problèmes pour Windows
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Les compteurs de performances Azure ne s’affichent pas
Le service Windows AzureEnhancedMonitoring recueille les indicateurs de performance dans Azure. Si le service n’a pas été installé correctement ou s’il n’est pas exécuté sur votre machine virtuelle, aucun indicateur de performance ne peut être collecté.
 
#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Le répertoire d’installation de l’extension Azure pour SAP est vide

##### <a name="issue"></a>Problème
Le répertoire d’installation C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\<version>\drop est vide.

##### <a name="solution"></a>Solution
L’extension n’est pas installée. Déterminez s’il s’agit d’un problème de proxy (comme décrit précédemment). Vous devrez peut-être redémarrer la machine ou réexécuter le script de configuration `Set-AzVMAEMExtension`.

#### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Le service pour l’extension Azure pour SAP n’existe pas

##### <a name="issue"></a>Problème

Le service Windows AzureEnhancedMonitoring n’existe pas.

La sortie de azperflib.exe génère une erreur :

![L’exécution d’azperflib.exe indique que le service de l’extension Azure pour SAP n’est pas en cours d’exécution][deployment-guide-figure-1400]
<a name="figure-14"></a>
 
##### <a name="solution"></a>Solution

Si le service n’existe pas, cela signifie que l’extension Azure pour SAP n’a pas été installée correctement. Redéployez l’extension comme décrit dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows].

Après avoir déployé l’extension, vérifiez à nouveau si les compteurs de performances Azure sont fournis dans la machine virtuelle Azure.

#### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Le service pour l’extension Azure pour SAP existe, mais ne parvient pas à démarrer
##### <a name="issue"></a>Problème
Le service Windows AzureEnhancedMonitoring existe et est activé, mais ne parvient pas à démarrer. Pour plus d’informations, consultez le journal des événements de l’application.
##### <a name="solution"></a>Solution
La configuration est incorrecte. Redémarrez l’extension Azure pour SAP, comme décrit dans Configurer l’extension Azure pour SAP.
 
### <a name="some-azure-performance-counters-are-missing"></a>Certains compteurs de performances Azure sont manquants
Le service Windows AzureEnhancedMonitoring recueille les indicateurs de performance dans Azure. Le service obtient des données provenant de plusieurs sources. Certaines données de configuration sont collectées localement, et certains indicateurs de performance sont lus à partir des diagnostics Azure. Les compteurs de stockage sont utilisés à partir de votre enregistrement au niveau de l’abonnement de stockage.

Si la résolution des problèmes à l’aide de la note SAP [1999351] ne résout pas le problème, réexécutez le script de configuration `Set-AzVMAEMExtension`. Vous devrez peut-être attendre une heure, car il est possible que les compteurs d’analyse ou de diagnostics de stockage ne soient pas créés immédiatement après avoir été activés. Si le problème persiste, ouvrez un message de support client SAP sur le composant BC-OP-NT-AZR pour Windows ou BC-OP-LNX-AZR pour une machine virtuelle Linux.

## <a name="troubleshooting-for-linux"></a><a name="a4dae567-e7fd-4d4b-8279-510b8e5fae4a"></a>Résolution des problèmes pour Linux

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Les compteurs de performances Azure ne s’affichent pas
Les indicateurs de performance dans Azure sont collectés par un démon. Si le démon ne fonctionne pas, aucun indicateur de performance ne peut être collecté.

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Le répertoire d’installation de l’extension Azure pour SAP est vide
##### <a name="issue"></a>Problème
Le répertoire \\var\\lib\\waagent\\ ne contient pas de sous-répertoire pour l’extension Azure pour SAP.
##### <a name="solution"></a>Solution
L’extension n’est pas installée. Déterminez s’il s’agit d’un problème de proxy (comme décrit précédemment). Vous devrez peut-être redémarrer la machine et/ou réexécuter le script de configuration `Set-AzVMAEMExtension`.

#### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>L’exécution de Set-AzVMAEMExtension et Test-AzVMAEMExtension affiche des messages d’avertissement indiquant que les disques managés standard ne sont pas pris en charge
##### <a name="issue"></a>Problème
Lorsque vous exécutez Set-AzVMAEMExtension ou Test-AzVMAEMExtension, des messages comme ceux qui suivent s’affichent :

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

En exécutant azperfli.exe comme décrit plus haut, vous pouvez obtenir un résultat indiquant un état défectueux.

##### <a name="solution"></a>Solution
Les messages s’affichent parce que les disques managés standard ne fournissent pas les API utilisées par l’extension de SAP pour Azure pour vérifier les statistiques des comptes de stockage Azure standard. Vous n’avez pas à vous en soucier. Nous avons introduit la collecte des données des comptes de stockage sur disque standard pour éviter les limitations trop fréquentes des entrées et sorties. Les disques managés évitent ces limitations en réduisant le nombre de disques dans un compte de stockage. Par conséquent, ces données ne sont pas capitales dans ce contexte.

### <a name="some-azure-performance-counters-are-missing"></a>Certains compteurs de performances Azure sont manquants

Les indicateurs de performance sur Azure sont collectés par un démon, qui obtient des données de plusieurs sources. Certaines données de configuration sont collectées localement, et certains indicateurs de performance sont lus à partir des diagnostics Azure. Les compteurs de stockage proviennent des journaux d’activité de votre abonnement de stockage.

Pour obtenir une liste complète et à jour des problèmes connus, consultez la note SAP [1999351], qui contient des informations de dépannage supplémentaires pour l’extension Azure pour SAP.

Si la résolution des problèmes à l’aide de la note SAP [1999351] ne résout pas le problème, réexécutez le script de configuration `Set-AzVMAEMExtension` comme décrit dans [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec Azure CLI][configure-linux] ou [Configurer l’extension de machine virtuelle Azure pour les solutions SAP avec PowerShell][configure-windows]. Vous devrez peut-être attendre une heure, car il est possible que les compteurs d’analyse ou de diagnostics de stockage ne soient pas créés immédiatement après avoir été activés. Si le problème persiste, ouvrez un message de support client SAP sur le composant BC-OP-NT-AZR pour Windows ou BC-OP-LNX-AZR pour une machine virtuelle Linux.

## <a name="azure-extension-error-codes"></a>Codes d’erreur de l’extension Azure

| ID d'erreur | Description de l'erreur | Solution |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | Configuration de l’application manquante. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Aucun ID de déploiement dans la configuration de l’application. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Aucun RoleInstanceId dans la configuration de l’application. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Aucun RoleInstanceId dans la configuration de l’application. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Impossible de lire la configuration Azure. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Fichier de configuration de l’application manquant. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Aucune taille de machine virtuelle dans la configuration de l’application. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Échec du compteur GlobalMemoryStatusEx. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Échec du compteur MaxHwFrequency. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Échec des compteurs NIC. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Échec du compteur de mappages de disque. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Échec du compteur du nom du processeur. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Échec du compteur de mappages de disque. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Indicateur « Type de disque » manquant dans le fichier de configuration de l’extension config.xml. « Type de disque », ainsi que d’autres compteurs, a été introduit dans la version 2.2.0.68 le 16/12/2015. Si vous avez déployé l’extension avant le 16/12/2015, elle utilise l’ancien fichier de configuration. L’infrastructure d’extension Azure met à niveau l’extension automatiquement, mais le fichier config.xml reste inchangé. Pour mettre à jour la configuration, téléchargez et exécutez le dernier script d’installation PowerShell. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Aucune mise en cache du disque. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Aucun débit SLA sur le disque. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Aucune IOPS SLA sur le disque. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Échec du compteur de mappages de disque. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Échec du compteur des dernières modifications matérielles. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Échec des compteurs NIC. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Modification du SID Windows due à Sysprep sur la machine virtuelle. | [Redéployer après Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Échec de l’accès à Storage Analytics. <br /><br />Dans la mesure où le remplissage des données Storage Analytics sur une nouvelle machine virtuelle peut prendre une demi-heure, il est possible que l’erreur disparaisse au bout d’un certain temps. Si elle persiste, réexécutez le script d’installation. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Aucun compteur Storage Analytics. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Échec des journaux Storage Analytics. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Configuration WAD incorrecte. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Format WAD inattendu. | [Contacter le support][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Compteurs WAD introuvables. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Compteurs WAD obsolètes. | [Contacter le support][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | Impossible de lire la table WAD. Il n’y a pas de connexion à la table WAD. Plusieurs causes possibles :<br /><br /> 1) Configuration obsolète <br />2) Aucune connexion réseau à Azure <br />3) Problèmes d’installation WAD | [Exécuter le script d’installation][deployment-guide-run-the-script]<br />[Réparer la connexion Internet][deployment-guide-fix-internet-connection]<br />[Contacter le support][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Échec des métriques NIC Perfmon. | [Contacter le support][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Échec des métriques de disque Perfmon. | [Contacter le support][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | Échec de certaines métriques Perfmon. | [Contacter le support][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Échec de la création de compteur par Perfmon. | [Contacter le support][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Aucun fournisseur de métriques configuré. | [Contacter le support][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Configuration Storage Analytics incorrecte. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Échec des métriques Storage Analytics. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Échec de l’un des fournisseurs de métriques. | [Exécuter le script d’installation][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | Échec du thread du fournisseur. | [Contacter le support][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Instructions détaillées sur les solutions apportées

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Exécuter le script d’installation

Suivez les étapes décrites dans le chapitre [Configuration de l’extension Azure pour SAP][configure-windows] de ce guide pour réinstaller l’extension. Notez que le provisionnement de certains compteurs peut prendre 30 minutes.

Si les erreurs persistent, [contactez le support][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Contacter le support

Il s’est produit une erreur inattendue ou il n’existe aucune solution connue. Collectez le fichier AzureEnhancedMonitoring_service.log situé dans le dossier C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop (Windows) ou /var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) et contactez le support SAP pour obtenir de l’aide.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756">Redéployer après Sysprep</a>

Si vous envisagez de créer une image de système d’exploitation généralisée et préparée avec Sysprep (qui peut inclure des logiciels SAP), nous vous recommandons de ne pas y inclure l’extension Azure pour SAP. Installez l’extension Azure pour SAP après le déploiement de la nouvelle instance de l’image généralisée du système d’exploitation.

Toutefois, si votre image de système d’exploitation généralisée et préparée avec Sysprep contient déjà l’extension Azure pour SAP, vous pouvez appliquer la solution de contournement suivante pour reconfigurer l’extension, sur l’instance de machine virtuelle qui vient d’être déployée :

* Sur l’instance de machine virtuelle qui vient d’être déployée, supprimez le contenu des dossiers suivants :  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\Status

* Suivez les étapes décrites dans le chapitre [Configuration de l’extension Azure pour SAP][configure-windows] de ce guide pour réinstaller l’extension.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Réparer la connexion Internet

La machine virtuelle Microsoft Azure exécutant l’extension Azure pour SAP a besoin d’un accès à Internet. Si elle fait partie d’un réseau virtuel Azure ou d’un domaine local, vérifiez que les paramètres de proxy nécessaires sont définis. Ces paramètres doivent également être valides pour que le compte LocalSystem puisse accéder à Internet. Suivez le chapitre [Configuration du proxy][configure-proxy] de ce guide.

En outre, si vous devez définir une adresse IP statique pour votre machine virtuelle Azure, ne la définissez pas manuellement dans la machine virtuelle Azure, mais avec [Azure PowerShell](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-cli.md) ou le [Portail Azure](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-pportal.md). L’adresse IP statique est propagée via le service DHCP Azure.

Il n’est pas possible de définir manuellement une adresse IP statique à l’intérieur de la machine virtuelle Azure, car cela pourrait poser problème avec l’extension Azure pour SAP.


## <a name="next-steps"></a>Étapes suivantes
* [Déploiement de machines virtuelles Azure pour SAP NetWeaver](./deployment-guide.md)
* [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver](./planning-guide.md)