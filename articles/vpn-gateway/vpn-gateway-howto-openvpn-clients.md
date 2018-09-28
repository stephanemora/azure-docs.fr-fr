---
title: Configurer des clients OpenVPN sur la passerelle VPN Azure | Microsoft Docs
description: Étapes de configuration de clients OpenVPN pour la passerelle VPN Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977840"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Configurer des clients OpenVPN sur la passerelle VPN Azure (préversion)

Cet article vous permet de configurer des clients OpenVPN.

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez terminé les étapes de configuration de OpenVPN pour votre passerelle VPN. Pour plus d’informations, consultez [Configurer OpenVPN pour la passerelle VPN Azure](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Clients Windows

1. Téléchargez et installez le client OpenVPN depuis le [site web OpenVPN](https://openvpn.net/index.php/open-source/downloads.html) officiel.
2. Téléchargez le profil VPN pour la passerelle. Pour y parvenir, accédez à l’onglet Configurations point à site dans le Portail Azure, ou saisissez « New-AzureRmVpnClientConfiguration » dans PowerShell.
3. Décompressez le profil. Utilisez ensuite e bloc-notes pour ouvrir le fichier de configuration vpnconfig.ovpn à partir du dossier OpenVPN.
4. Renseignez la section du certificat client P2S avec la clé publique du certificat client P2S en base64. Dans un certificat au format PEM, vous pouvez simplement ouvrir le fichier .cer et copier la clé base64 entre les en-têtes de certificat. Découvrez ici comment exporter un certificat pour obtenir la clé publique chiffrée.
5. Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Pour plus d’informations sur l’extraction de la clé privée, consultez [Exporter la clé](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Ne modifiez aucun autre champ. Utilisez la configuration complétée dans l’entrée client pour vous connecter au VPN.
7. Copiez le fichier vpnconfig.ovpn dans C:\Program Files\OpenVPN\config folder.
8. Cliquez avec le bouton droit de la souris sur l’icône OpenVPN de la barre d’état du système et cliquez sur l’option de connexion.

## <a name="mac"></a>Clients Mac

1. Télécharger et installer un client OpenVPN, comme [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Téléchargez le profil VPN pour la passerelle. Pour y parvenir, accédez à l’onglet Configurations point à site dans le Portail Azure, ou saisissez « New-AzureRmVpnClientConfiguration » dans PowerShell.
3. Décompressez le profil. Utilisez le bloc-notes pour ouvrir le fichier de configuration vpnconfig.ovpn à partir du dossier OpenVPN.
4. Renseignez la section du certificat client P2S avec la clé publique du certificat client P2S en base64. Dans un certificat au format PEM, vous pouvez simplement ouvrir le fichier .cer et copier la clé base64 entre les en-têtes de certificat. Consultez [Exporter la clé publique](vpn-gateway-certificates-point-to-site.md#cer) pour plus d’informations sur la façon d’exporter un certificat pour obtenir la clé publique codée.
5. Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Consultez [Exporter votre clé privée](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) pour plus d’informations sur l’extraction de la clé privée.
6. Ne modifiez aucun autre champ. Utilisez la configuration complétée dans l’entrée client pour vous connecter au VPN.
7. Double-cliquez sur le fichier de profil pour créer le profil dans Tunnelblik.
8. Lancez Tunnelblik à partir du dossier d’applications.
9. Cliquez sur l’icône Tunneblik dans la barre d’état du système et choisissez l’option de connexion.

## <a name="linux"></a>Clients Linux

1. Ouvrez une nouvelle session de terminal. Vous pouvez ouvrir une nouvelle session en appuyant sur « Ctrl + Alt + t » en même temps
2. Saisissez la commande suivante pour installer les composants nécessaires :

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Téléchargez le profil VPN pour la passerelle. Pour y parvenir, accédez à l’onglet Configurations point à site dans le Portail Azure, ou saisissez « New-AzureRmVpnClientConfiguration » dans PowerShell.
4. Renseignez la section de la clé privée avec la clé privée du certificat client P2S en base64. Consultez [Exporter votre clé privée](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) pour plus d’informations sur l’extraction de la clé privée.
5. Pour vous connecter en utilisant une ligne de commande, tapez la commande suivante :
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Pour vous connecter à l’aide de l’interface utilisateur graphique, accédez aux paramètres du système.
6. Cliquez sur **+** pour ajouter une nouvelle connexion VPN.
7. Sous **Ajouter un VPN**, choisissez **Importer à partir d’un fichier...**
8. Naviguez jusqu’au fichier de profil et double-cliquez dessus ou bien choisissez **Ouvrir**
9. Cliquez sur **Ajouter** dans la fenêtre **Ajouter un VPN**.
  
  ![Importer à partir d'un fichier](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. Vous pouvez vous connecter en mettant le VPN sur **ON** à partir de la page **Paramètres réseau**, ou sous l’icône de réseau dans la barre d’état système.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez que les clients VPN puissent accéder aux ressources d’un autre réseau virtuel (production), suivez les instructions de l’article [Réseau virtuel à réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) qui explique comment créer une connexion entre des réseaux virtuels. Veillez à activer BGP sur les passerelles et les connexions, sinon le trafic ne passe pas.
