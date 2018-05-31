---
title: Connexion à une machine virtuelle Windows Server | Microsoft Docs
description: Découvrez comment vous connecter à une machine virtuelle Windows à l’aide du portail Azure et du modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012639"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Connexion à une machine virtuelle Azure exécutant Windows
Vous utilisez le bouton **Connecter** dans le portail Azure pour démarrer une session Bureau à distance (RDP) depuis un bureau Windows. Tout d’abord, connectez-vous à la machine virtuelle, puis ouvrez une session.

Si vous essayez de vous connecter à une machine virtuelle Windows à partir d’un Mac, vous devez installer un client RDP pour Mac du type [Bureau à distance Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Sur la partie supérieure de la page de la machine virtuelle, cliquez sur ![l’image du bouton de connexion.](./media/connect-logon/connect.png) .
2. Sur la page **Se connecter à la machine virtuelle**, sélectionnez les options appropriées, puis cliquez sur **Télécharger le fichier RDP**.
2. Ouvrez le fichier RDP téléchargé et, à l’invite, cliquez sur **Se connecter**. 
2. Un message vous avertit que le fichier `.rdp` provient d’un éditeur inconnu. C’est normal. Dans la fenêtre Bureau à distance, cliquez sur **Connecter** pour continuer.
   
    ![Capture d’écran d’avertissement relatif à un éditeur inconnu.](./media/connect-logon/rdp-warn.png)
3. Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Tapez les informations d’identification d’un compte sur la machine virtuelle, puis cliquez sur **OK**.
   
     **Compte local** : il s’agit généralement du nom d’utilisateur et du mot de passe du compte local que vous avez spécifiés quand vous avez créé la machine virtuelle. Le domaine correspond alors au nom de la machine virtuelle et vous devez l’entrer sous la forme *nom_machine_virtuelle*&#92;*nom_utilisateur*.  
   
    **Machine virtuelle jointe à un domaine** : si la machine virtuelle appartient à un domaine, entrez le nom d’utilisateur au format *Domaine*&amp;#92;*Nom d’utilisateur*. Le compte doit également être membre du groupe Administrateurs ou bénéficier de privilèges d’accès à distance à la machine virtuelle.
   
    **Contrôleur de domaine** : si la machine virtuelle est un contrôleur de domaine, tapez le nom d’utilisateur et le mot de passe d’un compte d’administrateur de domaine pour ce domaine.
4. Cliquez sur **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.
   
   ![Capture d'écran montrant un message relatif à la vérification de l'identité de la machine virtuelle.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Si le bouton **Connecter** du portail est grisé et si vous n’êtes pas connecté à Azure avec une connexion [Express Route](../../expressroute/expressroute-introduction.md) ou [réseau privé virtuel de site à site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), vous devez créer votre machine virtuelle et lui attribuer une adresse IP publique pour pouvoir utiliser le protocole RDP. Pour en savoir plus sur les adresses IP publiques dans Azure, consultez [cet article](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Étapes suivantes
En cas de problème de connexion, consultez [Résolution des problèmes de connexion Bureau à distance](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cet article vous guide tout au long des opérations de diagnostic et de résolution des problèmes courants.

