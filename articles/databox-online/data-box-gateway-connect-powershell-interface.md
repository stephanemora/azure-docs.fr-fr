---
title: Se connecter à et gérer le périphérique de passerelle de zone de données Microsoft Azure via l’interface Windows PowerShell | Microsoft Docs
description: Décrit comment se connecter à et à gérer la passerelle de zone de données via l’interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403491"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gérer un appareil de passerelle de zone de données Azure via Windows PowerShell

Solution de passerelle de zone de données Azure vous permet d’envoyer des données sur le réseau vers Azure. Cet article décrit certaines des tâches de configuration et de gestion pour votre périphérique de passerelle de zone de données. Vous pouvez utiliser le portail Azure, interface utilisateur web locale ou l’interface Windows PowerShell pour gérer votre appareil.

Cet article se concentre sur les tâches que vous effectuez à l’aide de l’interface de PowerShell.

Cet article inclut les procédures suivantes :

- Se connecter à l’interface de PowerShell
- Démarrer une session de support
- Création d’un package de prise en charge
- Téléchargement d’un certificat
- Démarrer dans un environnement non-DHCP
- Afficher les informations de périphérique

## <a name="connect-to-the-powershell-interface"></a>Se connecter à l’interface de PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Création d’un package de prise en charge

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Téléchargement d’un certificat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Démarrer dans un environnement non-DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Afficher les informations de périphérique

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Étapes suivantes

- Déployer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) sur le portail Azure.
