---
title: Se connecter à la machine virtuelle Microsoft Azure | Place de marché Azure
description: Cet article explique comment se connecter à la machine virtuelle nouvellement créée sur Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4aea624c2127c9b0a61d72b8d14929ce6f47df24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142485"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Se connecter à la machine virtuelle Azure

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machine virtuelle Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions données fournies dans [Créer vos ressources techniques de machine virtuelle Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) pour gérer vos offres migrées.

Cet article explique comment se connecter aux machines virtuelles que vous avez créées sur Azure.  Une fois connecté, vous pouvez utiliser la machine virtuelle comme si vous étiez connecté localement à son serveur hôte.

## <a name="connect-to-a-windows-based-vm"></a>Se connecter à une machine virtuelle Windows

Vous allez utiliser le client Bureau à distance pour vous connecter à la machine virtuelle Windows hébergée sur Azure.  La plupart des versions de Windows incluent en mode natif la prise en charge du protocole RDP (Remote Desktop Protocol).  Pour les autres machines, vous trouverez plus d’informations sur les clients dans l’article [Clients Bureau à distance](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

Pour plus d’informations sur l’utilisation de la prise en charge intégrée de Windows RDP pour vous connecter à votre machine virtuelle, consultez [Connexion à une machine virtuelle Azure exécutant Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Au cours du processus, vous pouvez obtenir certains avertissements de sécurité, vous signalant par exemple que le fichier .rdp provient d’un éditeur inconnu ou que vos informations d’identification d’utilisateur ne sont pas vérifiables.  Vous pouvez ignorer ces avertissements.


## <a name="connect-to-a-linux-based-vm"></a>Se connecter à une machine virtuelle Linux

Pour vous connecter à la machine virtuelle Linux, vous devez disposer d’un client de protocole Secure Shell (SSH).  Dans le cadre de cet article, nous allons utiliser la version gratuite du terminal SSH [PuTTY](https://www.ssh.com/ssh/putty/).

1. Accédez au [portail Azure](https://ms.portal.azure.com). Recherchez et sélectionnez **Machines virtuelles**. 
2. Sélectionnez la machine virtuelle à laquelle vous souhaitez vous connecter.  
3. **Démarrez** la machine virtuelle si elle n’est pas déjà en cours d’exécution.
4. Cliquez sur le nom de la machine virtuelle pour ouvrir sa page **Vue d’ensemble**.
5. Remplacez l’adresse IP publique et le nom de DNS (Domain Name System) de votre machine virtuelle.  (Si ces valeurs ne sont pas définies, vous devez [créer une interface réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).)

   ![Paramètres de la page de vue d’ensemble d’une machine virtuelle](./media/publishvm_019.png)
 
6. Ouvrez l’application PuTTY.  
7. Dans la boîte de dialogue de configuration PuTTY, entrez l’adresse IP ou le nom DNS de votre machine virtuelle. 

   ![Paramètres du terminal PuTTY](./media/publishvm_020.png)
 
8. Cliquez sur **Ouvrir** pour ouvrir un terminal PuTTY.  
9. Lorsque vous y êtes invité, entrez le nom de compte et le mot de passe de votre compte de machine virtuelle Linux. 

Si vous rencontrez des problèmes de connexion, consultez la documentation de votre client SSH, par exemple [Chapitre 10 : Messages d'erreur courants](https://www.ssh.com/ssh/putty/putty-manuals).

Pour plus d’informations, notamment concernant l’ajout d’un Bureau à une machine virtuelle Linux approvisionnée, consultez l’article [Installer et configurer le Bureau à distance pour effectuer une connexion à une machine virtuelle Linux dans Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Arrêter les machines virtuelles inutilisées
Azure facture l’hébergement de toutes les machines virtuelles, que ces dernières soient en cours d’exécution *ou inactives*.  Il est donc recommandé d’arrêter les machines virtuelles qui ne sont pas en cours d’utilisation.  Par exemple, vous pouvez arrêter les machines virtuelles de test, de sauvegarde ou qui ont été mises hors service. Pour arrêter une machine virtuelle, procédez comme suit :

1. Dans le panneau **Machines virtuelles**, sélectionnez la machine virtuelle que vous souhaitez arrêter. 
2. Dans la barre d’outils située dans la partie supérieure de la page, cliquez sur le bouton **Arrêter**.

   ![Arrêter une machine virtuelle](./media/publishvm_018.png)

Azure arrête rapidement la machine virtuelle dans le cadre d’un processus appelé *libération*, qui procède non seulement à l’arrêt du système d’exploitation de la machine virtuelle, mais également à la libération du matériel et des ressources réseau avec lesquels la machine virtuelle a été précédemment approvisionnée.

Par la suite, si vous souhaitez réactiver une machine virtuelle arrêtée, sélectionnez-la, puis cliquez sur le bouton **Démarrer**.


## <a name="next-steps"></a>Étapes suivantes

Une fois connecté à distance, vous voici prêt à [configurer votre machine virtuelle](./cpp-configure-vm.md).
