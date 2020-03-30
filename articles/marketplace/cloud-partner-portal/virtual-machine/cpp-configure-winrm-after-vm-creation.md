---
title: Configurer WinRM après la création d’une machine virtuelle Azure | Place de marché Azure
description: Explique comment configurer Windows Remote Management (WinRM) après la création d’une machine virtuelle hébergée sur Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 673fe1f31f6a8602225e7cde3bf1eb4c3b28b8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278142"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configurer WinRM après la création d’une machine virtuelle

Cet article explique comment configurer une machine virtuelle hébergée sur Azure pour activer WinRM sur HTTPS.  Cette configuration s’applique uniquement aux machines virtuelles Windows et requiert le processus en deux étapes suivant :

1. Activer le trafic sur le port pour le protocole WinRM sur HTTPS.  Vous allez configurer ce paramètre pour votre machine virtuelle dans le portail Azure.
2. Configurer la machine virtuelle pour activer WinRM en exécutant les scripts PowerShell fournis.


## <a name="enabling-port-traffic"></a>Activer le trafic sur le port

Le protocole WinRM sur HTTPS utilise le port 5986, qui n’est pas activé par défaut sur les machines virtuelles Windows préconfigurées proposées sur la Place de marché Azure. Pour activer ce protocole, procédez comme suit pour ajouter une nouvelle règle au groupe de sécurité réseau (NSG) dans le [portail Azure](https://portal.azure.com).  Pour plus d’informations sur les groupes de sécurité réseau, voir [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Accédez au panneau **Machines virtuelles >**   <*nom de la machine virtuelle*>   **> Paramètres/Mise en réseau**.
2.  Cliquez sur le nom du groupe de sécurité réseau (dans cet exemple, **testvm11002**) pour afficher ses propriétés :

    ![Propriétés du groupe de sécurité réseau](./media/nsg-properties.png)
 
3. Sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**, pour afficher ce panneau.
4. Cliquez sur **+Ajouter** pour créer une nouvelle règle appelée `WinRM_HTTPS` pour le port TCP 5986.

    ![Ajouter une règle de sécurité réseau de trafic entrant](./media/nsg-new-rule.png)

5. Cliquez sur **OK** lorsque vous avez fini de fournir les valeurs.  La liste des règles de sécurité de trafic entrant doit contenir les nouvelles entrées suivantes.

    ![Liste des règles de sécurité réseau de trafic entrant](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configurer une machine virtuelle pour activer WinRM 

Utilisez les étapes suivantes pour activer et configurer la fonctionnalité Windows Remote Management sur votre machine virtuelle Windows.   

1. Établissez une connexion Bureau à distance avec votre machine virtuelle hébergée sur Azure.  Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Les étapes restantes seront exécutées sur votre machine virtuelle.
2. Téléchargez les fichiers suivants et enregistrez-les dans un dossier sur votre machine virtuelle :
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Ouvrez la **console PowerShell** avec des privilèges élevés (**Exécuter en tant qu’administrateur**). 
4. Exécutez la commande suivante en fournissant le paramètre obligatoire : le nom de domaine complet (FQDN) pour votre machine virtuelle : <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Powershell configuration script 1](./media/powershell-file1.png)

    Ce script varie selon les deux autres fichiers présents dans le même dossier.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez configuré WinRM, vous êtes prêt à [déployer votre machine virtuelle à partir des disques durs virtuels qui la composent](./cpp-deploy-vm-vhd.md).
