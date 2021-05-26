---
title: Comment évaluer des serveurs avec Azure Arc avec une machine virtuelle Azure
description: Découvrez comment évaluer des serveurs avec Azure Arc à l’aide d’une machine virtuelle Azure.
ms.date: 05/06/2021
ms.topic: conceptual
ms.openlocfilehash: 1e49b2d29b21f6ded72d1b22e946743f27e7d160
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109837832"
---
# <a name="evaluate-arc-enabled-servers-on-an-azure-virtual-machine"></a>Évaluer les serveurs avec Arc sur une machine virtuelle Azure

Les serveurs avec Azure Arc sont conçus pour vous aider à connecter des serveurs qui s’exécutent localement ou dans d’autres clouds à Azure. Normalement, vous n’utilisez pas de serveurs avec Azure Arc sur une machine virtuelle Azure, car les mêmes fonctionnalités sont disponibles en mode natif pour ces machines virtuelles, y compris une représentation de la machine virtuelle dans Azure Resource Manager, des extensions de machine virtuelle, des identités managées et Azure Policy. Si vous tentez d’installer des serveurs avec Azure Arc sur une machine virtuelle Azure, vous recevrez un message d’erreur indiquant que cela n’est pas pris en charge et l’installation de l’agent sera annulée.

Bien que vous ne soyez pas en mesure d’installer des serveurs avec Azure Arc sur une machine virtuelle Azure pour des scénarios de production, il est possible de configurer des serveurs avec Azure Arc pour qu’ils s’exécutent sur une machine virtuelle Azure *à des fins d’évaluation et de test uniquement*. Cet article vous aidera à configurer une machine virtuelle Azure avant de pouvoir activer des serveurs avec Azure Arc dessus.

## <a name="prerequisites"></a>Prérequis

* Votre compte est affecté au rôle [Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
* La machine virtuelle Azure exécute un [système d’exploitation pris en charge par les serveurs avec Arc](agent-overview.md#supported-operating-systems). Si vous n’avez pas de machine virtuelle Azure, vous pouvez déployer une [machine virtuelle Windows simple](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) ou une [machine virtuelle Ubuntu Linux 18.04 LTS simple](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json).
* Votre machine virtuelle Azure peut communiquer en sortie pour télécharger le package d’agent Azure Connected Machine pour Windows à partir du [Centre de téléchargement Microsoft](https://aka.ms/AzureConnectedMachineAgent), et pour Linux à partir du [dépôt de packages](https://packages.microsoft.com/) Microsoft. Si la connectivité sortante à Internet est limitée à la suite de votre stratégie de sécurité informatique, vous devez télécharger le package de l’agent manuellement et le copier dans un dossier sur la machine virtuelle Azure.
* Un compte avec des privilèges élevés (c’est-à-dire un administrateur ou root) sur la machine virtuelle, et un accès RDP ou SSH à la machine virtuelle.
* Pour inscrire et gérer la machine virtuelle Azure avec des serveurs avec Arc, vous êtes membre du rôle [Administrateur de ressources Azure Connected Machine](../../role-based-access-control/built-in-roles.md#azure-connected-machine-resource-administrator) ou [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) dans le groupe de ressources.

## <a name="plan"></a>Planifier

Pour commencer à gérer votre machine virtuelle Azure en tant que serveur avec Arc, vous devez apporter les modifications suivantes à la machine virtuelle Azure avant de pouvoir installer et configurer des serveurs avec Arc.

1. Supprimez toutes les extensions de machine virtuelle déployées sur la machine virtuelle Azure, telles que l’agent Log Analytics. Bien que les serveurs avec Arc prennent en charge la plupart des extensions des machines virtuelles Azure, l’agent des serveurs avec Arc ne peut pas gérer les extensions de machine virtuelle déjà déployées sur la machine virtuelle.

2. Désactivez l’agent invité Azure Windows ou Linux. L’agent invité de machine virtuelle Azure remplit un rôle similaire à l’agent de machine connectée aux serveurs avec Azure Arc. Pour éviter les conflits entre les deux, l’agent de machine virtuelle Azure doit être désactivé. Une fois désactivé, vous ne pouvez pas utiliser les extensions de machine virtuelle ou certains services Azure.

3. Créez une règle de sécurité pour refuser l’accès à Azure Instance Metadata Service (IMDS). IMDS est une API REST que les applications peuvent appeler pour obtenir des informations sur la représentation des machines virtuelles dans Azure, y compris son ID de ressource et son emplacement. IMDS fournit également l’accès à toutes les identités managées affectées à la machine. Les serveurs avec Azure Arc fournissent leur propre implémentation d’IMDS et retournent des informations sur la représentation Azure Arc de la machine virtuelle. Pour éviter les situations où les deux points de terminaison IMDS sont disponibles et que les applications doivent choisir entre les deux, vous bloquez l’accès à IMDS des machines virtuelles Azure afin que l’implémentation IMDS du serveur avec Azure Arc soit la seule disponible.

Une fois ces modifications effectuées, votre machine virtuelle Azure se comporte comme n’importe quelle machine ou n’importe quel serveur en dehors d’Azure, et se trouve au point de départ nécessaire pour installer et évaluer les serveurs avec Azure Arc.

Lorsque les serveurs avec Arc sont configurés sur la machine virtuelle, vous voyez deux représentations de ceux-ci dans Azure. L’une est la ressource de machine virtuelle Azure, avec un type de ressource `Microsoft.Compute/virtualMachines`, et l’autre est une ressource Azure Arc, avec un type de ressource `Microsoft.HybridCompute/machines`. En raison de la prévention de la gestion du système d’exploitation invité à partir du serveur hôte physique partagé, la meilleure façon de voir les deux ressources est de considérer la ressource de machine virtuelle Azure comme le matériel virtuel de votre machine virtuelle, et vous permet de contrôler l’état de l’alimentation et d’afficher des informations sur ses configurations de référence SKU, de réseau et de stockage. La ressource Azure Arc de son côté gère le système d’exploitation invité de cette machine virtuelle et peut être utilisée pour installer des extensions, afficher les données de conformité pour Azure Policy et effectuer toute autre tâche prise en charge par les serveurs avec Arc.

## <a name="reconfigure-azure-vm"></a>Reconfigurer une machine virtuelle Azure

1. Supprimez toutes les extensions de machine virtuelle sur la machine virtuelle Azure.

   Dans le portail Azure, accédez à votre ressource de machine virtuelle Azure et, dans le volet gauche, sélectionnez **Extensions**. Si des extensions sont installées sur la machine virtuelle, sélectionnez chaque extension individuellement, puis **Désinstaller**. Attendez la fin de la désinstallation de toutes les extensions avant de passer à l’étape 2.

2. Désactivez l’agent invité de machine virtuelle Azure.

   Pour désactiver l’agent invité de machine virtuelle Azure, vous devez vous connecter à votre machine virtuelle à l’aide de Connexion Bureau à distance (Windows) ou SSH (Linux). Une fois connecté, exécutez les commandes suivantes pour désactiver l’agent invité.

   Pour Windows, exécutez les commandes PowerShell suivantes :

   ```powershell
   Set-Service WindowsAzureGuestAgent -StartupType Disabled -Verbose
   Stop-Service WindowsAzureGuestAgent -Force -Verbose
   ```

   Pour Linux, exécutez les commandes ci-dessous :

   ```bash
   sudo service walinuxagent stop
   sudo waagent -deprovision -force
   sudo rm -rf /var/lib/waagent
   ```

3. Bloquer l’accès au point de terminaison Azure IMDS.

   Tout en étant toujours connecté au serveur, exécutez les commandes suivantes pour bloquer l’accès au point de terminaison Azure IMDS. Pour Windows, exécutez la commande PowerShell suivante :

   ```powershell
   New-NetFirewallRule -Name BlockAzureIMDS -DisplayName "Block access to Azure IMDS" -Enabled True -Profile Any -Direction Outbound -Action Block -RemoteAddress 169.254.169.254
   ```

   Pour Linux, consultez la documentation de votre distribution pour la meilleure façon de bloquer l’accès sortant à `169.254.169.254/32` sur le port TCP 80. Normalement, vous bloquez l’accès sortant avec le pare-feu intégré, mais vous pouvez également le bloquer temporairement avec **iptables** ou **nftables**.

   Si votre machine virtuelle Azure exécute Ubuntu, procédez comme suit pour configurer Uncomplicated Firewall (UFW) :

   ```bash
   sudo ufw --force enable
   sudo ufw deny out from any to 169.254.169.254
   sudo ufw default allow incoming
   sudo apt-get update
   ```


   Pour configurer une configuration iptables générique, exécutez la commande suivante :

   ```bash
   iptables -A OUTPUT -d 169.254.169.254 -j DROP
   ```

   > [!NOTE]
   > Cette configuration doit être définie après chaque redémarrage, sauf si une solution iptables persistante est utilisée.

4. Installez et configurez l’agent des serveurs avec Azure Arc.

   La machine virtuelle est maintenant prête pour que vous commenciez à évaluer les serveurs avec Arc. Pour installer et configurer l’agent des serveurs avec Arc, consultez [Connecter des machines hybrides à l’aide du portail Azure](onboard-portal.md) et suivez les étapes pour générer un script d’installation et installer à l’aide de la méthode avec script.

   > [!NOTE]
   > Si la connectivité sortante à Internet est restreinte pour votre machine virtuelle Azure, vous devez télécharger le package de l’agent manuellement. Copiez le package de l’agent sur la machine virtuelle Azure, puis modifiez le script d’installation des serveurs avec Arc pour référencer le dossier source.

Si vous avez manqué l’une des étapes, le script d’installation détecte qu’il s’exécute sur une machine virtuelle Azure et se conclut sur une erreur. Vérifiez que vous avez terminé les étapes 1-3, puis réexécutez le script.

## <a name="verify-the-connection-with-azure-arc"></a>Vérifier la connexion avec Azure Arc

Après avoir installé et configuré l’agent de manière à inscrire les serveurs compatibles avec Azure Arc, accédez au portail Azure pour vérifier que le serveur a déjà été correctement connecté. Affichez vos machines dans le [portail Azure](https://portal.azure.com).

![Connexion au serveur réussie](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment planifier et activer un grand nombre de machines sur des serveurs avec Azure Arc](plan-at-scale-deployment.md) afin de simplifier la configuration de fonctionnalités essentielles de sécurité, de gestion et de surveillance dans Azure.

* Découvrez nos [extensions de machine virtuelle Azure prises en charge](manage-vm-extensions.md) disponibles pour simplifier le déploiement avec d’autres services Azure, tels qu’Automation, KeyVault et d’autres pour votre machine Windows ou Linux.

* Une fois les tests terminés, consultez [Supprimer l’agent des serveurs avec Arc](manage-agent.md#remove-the-agent).
