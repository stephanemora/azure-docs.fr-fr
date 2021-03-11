---
title: Configurer TLS 1.2 sur les clients Windows accédant à l’appareil Azure Stack Edge Pro avec GPU
description: Décrit comment configurer TLS 1.2 sur les clients Windows accédant à l’appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4a159f7fa384a6899fb3cbb4db3bba9e0ed02d52
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438862"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Configurer TLS 1.2 sur les clients Windows accédant à l’appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Si vous utilisez un client Windows pour accéder à votre appareil Azure Stack Edge Pro, vous devez configurer TLS 1.2 sur votre client. Cet article fournit des ressources et des instructions pour configurer TLS 1.2 sur votre client Windows. 

Les instructions fournies ici sont basées sur les tests effectués sur un client exécutant Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Configurer TLS 1.2 pour la session PowerShell actuelle

Procédez comme suit pour configurer TLS 1.2 sur votre client.

1. Démarrez PowerShell en tant qu'administrateur.
2. Pour définir TLS 1.2 pour la session PowerShell en cours, saisissez :
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Configurer TLS 1.2 sur le client

Si vous souhaitez définir le protocole TLS 1.2 sur l’ensemble du système pour votre environnement, suivez les instructions fournies dans les documents suivants :

- [Général : activation de TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12)
- [Guide pratique pour activer TLS 1.2 sur les clients](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Guide pratique pour activer TLS 1.2 sur les serveurs de site et les systèmes de site distants](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protocoles dans TLS/SSL (SSP Schannel)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Suites de chiffrement](/windows-server/security/tls/tls-registry-settings#tls-12) : En particulier, [Configuration de l’ordre des suites de chiffrement TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) Assurez-vous que vous répertoriez vos suites de chiffrement actuelles, et ajoutez les éléments manquants dans la liste suivante :

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Vous pouvez également ajouter ces suites de chiffrement en modifiant directement les paramètres du Registre.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Comment définir des courbes elliptiques

    Assurez-vous de répertorier vos courbes elliptiques actuelles, et ajoutez les éléments manquants dans la liste suivante :

    - P-256 
    - P-384

    Vous pouvez également ajouter ces courbes elliptiques en modifiant directement les paramètres du Registre.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Définissez la taille minimale d’échange de clés RSA sur 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Étapes suivantes

[Se connecter à Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)