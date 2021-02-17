---
title: 'Passerelle VPN Azure : À propos des profils clients VPN P2S'
description: Cet article vous permet de rechercher les informations dont vous avez besoin pour un profil client VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979066"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Utilisation de fichiers de profil client VPN P2S

Le fichier de profil contient les informations nécessaires à la configuration d’une connexion VPN. Cet article vous aide à obtenir et à comprendre les informations nécessaires pour un profil client VPN.

## <a name="generate-and-download-profile"></a>Générer et télécharger un profil

Vous pouvez générer des fichiers de configuration client à l’aide de PowerShell, ou en utilisant le portail Azure. L’une ou l’autre des méthodes retourne le même fichier zip.

### <a name="portal"></a>Portail

1. Dans le portail Azure, accédez à la passerelle du réseau virtuel auquel vous souhaitez vous connecter.
1. Dans la page de passerelle de réseau virtuel, sélectionnez **Configuration de point à site**.
1. En haut de la page Configuration de point à site, sélectionnez **Télécharger le client VPN**. La génération du package de configuration du client prend quelques minutes.
1. Votre navigateur indique qu’un fichier zip de configuration du client est disponible. Il porte le même nom que votre passerelle. Décompressez le fichier pour afficher les dossiers.

### <a name="powershell"></a>PowerShell

Pour générer à l’aide de PowerShell, vous pouvez utiliser l’exemple suivant :

1. Lorsque vous générez les fichiers de configuration du client VPN, la valeur de la méthode « -AuthenticationMethod » est « EapTls ». Générez les fichiers de configuration du client VPN à l’aide de la commande suivante :

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copiez l’URL dans votre navigateur pour télécharger le fichier zip, puis décompressez le fichier pour afficher les dossiers.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Le dossier **OpenVPN** contient le profil *ovpn* qui doit être modifié pour inclure la clé et le certificat. Pour plus d’informations, consultez [Configurer des clients OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn-clients.md#windows). Si vous sélectionnez l’authentification Azure AD sur la passerelle VPN, ce dossier sera absent du fichier zip. Au lieu de cela, accédez au dossier AzureVPN et recherchez azurevpnconfig.xml.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la fonctionnalité point à site, consultez l’article [À propos de la fonctionnalité point à site](point-to-site-about.md).
