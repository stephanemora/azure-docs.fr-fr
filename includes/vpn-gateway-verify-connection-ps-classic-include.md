---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03dc6d8bb95a952a77be31f79df36a2c1ddc8ffc
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458633"
---
Vous pouvez vérifier que votre connexion a réussi à l’aide de l’applet de commande « Get-AzureVNetConnection ».

1. Utilisez l’exemple d’applet de commande suivant, en configurant les valeurs sur les vôtres. Le nom du réseau virtuel doit être placé entre guillemets s’il contient des espaces.

  ```azurepowershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Une fois l’applet de commande exécutée, affichez les valeurs. Dans l’exemple ci-dessous, la zone État de la connectivité indique « Connecté » et vous pouvez voir les octets d’entrée et de sortie.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal