---
title: Connecter et gérer un appareil Microsoft Azure Data Box Gateway via l’interface Windows PowerShell | Microsoft Docs
description: Décrit comment connecter et gérer Data Box Gateway via l’interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64415160"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gérer un appareil Azure Data Box Gateway via Windows PowerShell

La solution Azure Data Box Gateway vous permet d’envoyer des données via le réseau vers Azure. Cet article décrit certaines des tâches de gestion et de configuration pour votre appareil Data Box Gateway. Vous pouvez utiliser le portail Azure, l'interface utilisateur locale ou l'interface Windows PowerShell pour gérer votre appareil.

Cet article traite des tâches que vous effectuez à l’aide de l’interface PowerShell.

Cet article inclut les procédures suivantes :

- Connexion à l’interface PowerShell
- Démarrer une session de support
- Création d’un package de prise en charge
- Téléchargement d’un certificat
- Démarrage dans un environnement non DHCP
- Affichage des informations sur l’appareil

## <a name="connect-to-the-powershell-interface"></a>Connexion à l’interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Création d’un package de prise en charge

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Téléchargement d’un certificat

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Démarrage dans un environnement non DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Affichage des informations sur l’appareil

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Étapes suivantes

- Déployer [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) sur le portail Azure.
