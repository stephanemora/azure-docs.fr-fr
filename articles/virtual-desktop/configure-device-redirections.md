---
title: Configurer des redirections d’appareil - Azure
description: Comment configurer des redirections d’appareil pour Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 570983229c16fa9c01d14bd7575d0720a7197191
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756542"
---
# <a name="configure-device-redirections"></a>Configurer des redirections d’appareil

La configuration des redirections d’appareils pour votre environnement Azure Virtual Desktop vous permet d’utiliser des imprimantes, des périphériques USB, des microphones et d’autres périphériques dans la session à distance. Certaines redirections d’appareil requièrent des modifications des propriétés du protocole RDP (Remote Desktop Protocol) et des paramètres de stratégie de groupe.

## <a name="supported-device-redirections"></a>Redirections d’appareil prises en charge

Chaque client prend en charge différentes redirections d’appareil. Consultez [Comparer les clients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) pour obtenir la liste complète des redirections d’appareil prises en charge pour chaque client.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Personnalisation des propriétés RDP pour un pool d’hôtes

Pour en savoir plus sur la personnalisation des propriétés RDP d’un pool d’hôtes à l’aide de PowerShell ou du portail Azure, consultez les [propriétés RDP](customize-rdp-properties.md). Pour obtenir la liste complète des propriétés RDP prises en charge, consultez les [paramètres de fichier RDP pris en charge](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Configurer des redirections d’appareil

Vous pouvez utiliser les propriétés RDP et les paramètres de stratégie de groupe suivants pour configurer des redirections d’appareil.

### <a name="audio-input-microphone-redirection"></a>Redirection de l’entrée audio (microphone)

Définissez la propriété RDP suivante pour configurer la redirection de l’entrée audio :

- `audiocapturemode:i:1` active la redirection de l’entrée audio.
- `audiocapturemode:i:0` désactive la redirection de l’entrée audio.

### <a name="audio-output-speaker-redirection"></a>Redirection de la sortie audio (haut-parleur)

Définissez la propriété RDP suivante pour configurer la redirection de la sortie audio :

- `audiomode:i:0` active la redirection de la sortie audio.
- `audiomode:i:1` ou `audiomode:i:2` désactive la redirection de la sortie audio.

### <a name="camera-redirection"></a>Redirection de la caméra

Définissez la propriété RDP suivante pour configurer la redirection de la caméra :

- `camerastoredirect:s:*` redirige toutes les caméras.
- `camerastoredirect:s:` désactive la redirection de la caméra.

>[!NOTE]
>Même si la propriété `camerastoredirect:s:` est désactivée, les caméras locales peuvent être redirigées via la propriété `devicestoredirect:s:`. Pour désactiver complètement la redirection de caméra, définissez `camerastoredirect:s:`, ainsi que `devicestoredirect:s:` ou un sous-ensemble d’appareils Plug-and-Play n’incluant pas de caméra.

Vous pouvez également rediriger des caméras spécifiques à l’aide d’une liste séparée par des points-virgules d’interfaces KSCATEGORY_VIDEO_CAMERA, telles que `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi`.

### <a name="clipboard-redirection"></a>Redirection du Presse-papiers

Définissez la propriété RDP suivante pour configurer la redirection du Presse-papiers :

- `redirectclipboard:i:1` active la redirection du Presse-papiers.
- `redirectclipboard:i:0` désactive la redirection du Presse-papiers.

### <a name="com-port-redirections"></a>Redirections de port COM

Définissez la propriété RDP suivante pour configurer la redirection de port COM :

- `redirectcomports:i:1` active la redirection de port COM.
- `redirectcomports:i:0` désactive la redirection de port COM.

### <a name="usb-redirection"></a>Redirection USB

Tout d’abord, définissez la propriété RDP suivante pour activer la redirection de périphérique USB :

- `usbdevicestoredirect:s:*` active la redirection de périphérique USB.
- `usbdevicestoredirect:s:` désactive la redirection de périphérique USB.

Ensuite, définissez les stratégie de groupe suivante sur l’appareil local de l’utilisateur :

- Accédez à **Configuration ordinateur** > **Stratégies**> **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Client Connexion Bureau à distance** > **Redirection des périphériques USB RemoteFX**.
- Sélectionnez **Autoriser la redirection RDP d’autres périphériques USB RemoteFX pris en charge à partir de cet ordinateur**.
- Sélectionnez l’option **Activé**, puis cochez la case **Administrateurs et utilisateurs dans les droits d’accès à la redirection USB RemoteFX**.
- Sélectionnez **OK**.

### <a name="plug-and-play-device-redirection"></a>Redirection d’appareils Plug-and-Play

Définissez la propriété RDP suivante pour configurer la redirection d’appareils Plug-and-Play :

- `devicestoredirect:s:*` active la redirection de tous les appareils Plug-and-Play.
- `devicestoredirect:s:` désactive la redirection d’appareils Plug-and-Play.

Vous pouvez également sélectionner des appareils Plug-and-Play spécifiques à l’aide d’une liste séparée par des points-virgules, par exemple `devicestoredirect:s:root\*PNP0F08`.

### <a name="local-drive-redirection"></a>Redirection de lecteur local

Définissez la propriété RDP suivante pour configurer la redirection de lecteur local :

- `drivestoredirect:s:*` active la redirection de tous les lecteurs de disques.
- `drivestoredirect:s:` désactive la redirection de lecteur local.

Vous pouvez également sélectionner des lecteurs spécifiques à l’aide d’une liste séparée par des points-virgules, par exemple `drivestoredirect:s:C:;E:;`.

Pour activer la fonctionnalité de transfert de fichiers du client web, définissez `drivestoredirect:s:*`. Si vous définissez une autre valeur pour cette propriété RDP, la fonctionnalité de transfert de fichiers du client web sera désactivée.

### <a name="printer-redirection"></a>Redirection d’imprimantes

Définissez la propriété RDP suivante pour configurer la redirection d’imprimantes :

- `redirectprinters:i:1` active la redirection d’imprimantes.
- `redirectprinters:i:0` désactive la redirection d’imprimantes.

### <a name="smart-card-redirection"></a>Redirection de carte à puce

Définissez la propriété RDP suivante pour configurer la redirection de carte à puce :

- `redirectsmartcards:i:1` active la redirection de carte à puce.
- `redirectsmartcards:i:0` désactive la redirection de carte à puce.
