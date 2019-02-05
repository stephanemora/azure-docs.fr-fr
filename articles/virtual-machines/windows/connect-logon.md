---
title: Connexion à une machine virtuelle Windows Server | Microsoft Docs
description: Apprenez à vous connecter à une machine virtuelle Windows à l'aide du portail Azure et du modèle de déploiement Resource Manager.
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
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 4bfb17a7c50e97ae71908f052f7f38110cf376df
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296883"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Connexion à une machine virtuelle Azure exécutant Windows
Vous utilisez le bouton **Connecter** dans le portail Azure pour démarrer une session Bureau à distance (RDP) depuis un bureau Windows. Tout d'abord, connectez-vous à la machine virtuelle, puis ouvrez une session.

Pour vous connecter à une machine virtuelle Windows à partir d’un Mac, vous devez installer un client RDP pour Mac tel que [Bureau à distance Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Dans la partie supérieure de la page de la machine virtuelle, sélectionnez **Connecter**.
2. Sur la page **Connexion à la machine virtuelle**, sélectionnez l'adresse IP et le port appropriés. Dans la plupart des cas, l'adresse IP et le port par défaut doivent être utilisés. Sélectionnez **Télécharger le fichier RDP**Télécharger le fichier RDP**. Si la machine virtuelle dispose d'un ensemble de stratégies juste-à-temps, vous devez d'abord sélectionner le bouton **Demander l'accès** avant de pouvoir télécharger le fichier RDP. Pour plus d'informations sur la stratégie juste-à-temps, consultez [Gérer l'accès à la machine virtuelle à l'aide de la stratégie juste-à-temps](../../security-center/security-center-just-in-time.md).
2. Ouvrez le fichier RDP téléchargé et, à l’invite, sélectionnez **Connecter**. 
2. Un message vous avertit que le fichier `.rdp` provient d'un éditeur inconnu. Ceci est normal. Sélectionnez **Connecter** dans la fenêtre **Connexion Bureau à distance** pour continuer.
   
    ![Capture d’écran d’avertissement relatif à un éditeur inconnu.](./media/connect-logon/rdp-warn.png)
3. Dans la fenêtre **Sécurité Windows**, sélectionnez **Plus de choix**, puis **Utiliser un autre compte**. Entrez les informations d’identification d’un compte sur la machine virtuelle, puis cliquez sur **OK**.
   
     **Compte local** : il s’agit généralement du nom d’utilisateur et du mot de passe du compte local que vous avez spécifiés quand vous avez créé la machine virtuelle. Le domaine correspond alors au nom de la machine virtuelle et vous devez l’entrer sous la forme *nom_machine_virtuelle*&#92;*nom_utilisateur*.  
   
    **Machine virtuelle jointe à un domaine** : si la machine virtuelle appartient à un domaine, entrez le nom d’utilisateur au format *Domaine*&#92;*Nom d’utilisateur*. Le compte doit également être membre du groupe Administrateurs ou bénéficier de privilèges d’accès à distance à la machine virtuelle.
   
    **Contrôleur de domaine** : si la machine virtuelle est un contrôleur de domaine, entrez le nom d’utilisateur et le mot de passe d’un compte d’administrateur de domaine pour ce domaine.
4. Sélectionnez **Oui** pour vérifier l’identité de la machine virtuelle et terminer la connexion.
   
   ![Capture d'écran montrant un message relatif à la vérification de l'identité de la machine virtuelle.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Si le bouton **Connecter** du portail est grisé et que vous n’êtes pas connecté à Azure via une connexion [Express Route](../../expressroute/expressroute-introduction.md) ou [VPN de site à site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), vous devez créer une adresse IP publique et l’attribuer à votre machine virtuelle pour pouvoir utiliser le protocole RDP. Pour plus d’informations, consultez [Adresses IP publiques dans Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Se connecter à la machine virtuelle à l’aide de PowerShell

Si vous utilisez PowerShell et si vous avez installé le module AzureRM, vous pouvez également vous connecter à l’aide de la cmdlet `Get-AzureRmRemoteDesktopFile`, comme indiqué ci-dessous.

Cet exemple lance immédiatement la connexion RDP : vous êtes alors redirigé par le biais d’invites semblables à celles présentées ci-dessus.

```powershell
Get-AzureRmRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Vous pouvez également enregistrer le fichier RDP pour une utilisation ultérieure.

```powershell
Get-AzureRmRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Étapes suivantes
Si vous avez des difficultés à vous connecter, consultez [Résoudre des problèmes de connexion Bureau à distance](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

