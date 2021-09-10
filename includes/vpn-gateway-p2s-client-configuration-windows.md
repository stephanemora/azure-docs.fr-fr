---
title: Fichier Include
description: inclure fichier
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/12/2021
ms.author: cherylmc
ms.openlocfilehash: b676e4c801b447291288fdd07ff64177f1201e6c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732651"
---
Vous pouvez utiliser le même package de configuration du client VPN sur chaque ordinateur client Windows, tant que la version correspond à l’architecture du client. Pour obtenir la liste des systèmes d’exploitation clients pris en charge, consultez la section Point à site de la [FAQ sur la passerelle VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Vous devez disposer de droits d’administrateur sur l’ordinateur client Windows à partir duquel vous vous connectez.
>

### <a name="install-the-configuration-files"></a>Installer les fichiers de configuration

1. Sélectionnez les fichiers de configuration du client VPN qui correspondent à l’architecture de l’ordinateur Windows. Pour une architecture de processeur 64 bits, choisissez le package du programme d’installation « VpnClientSetupAmd64 ». Pour une architecture de processeur 32 bits, choisissez le package du programme d’installation « VpnClientSetupX86 ». 
1. Double-cliquez sur le package pour lancer l’installation. Si une fenêtre contextuelle SmartScreen s’affiche, cliquez sur **Plus d’infos**, puis sur **Exécuter quand même**.

### <a name="verify-and-connect"></a>Vérifier et se connecter

1. Vérifiez que vous avez installé un certificat client sur l’ordinateur client. Un certificat client est requis pour l’authentification lors de l’utilisation du type d’authentification de certificat Azure natif. Pour afficher le certificat client, ouvrez **Gérer les certificats utilisateur**. Le certificat client est installé dans **Current User\Personal\Certificates**.
1. Pour vous connecter, accédez à **Paramètres réseau**, puis cliquez sur **VPN**. La connexion VPN indique le nom du réseau virtuel auquel elle se connecte.
