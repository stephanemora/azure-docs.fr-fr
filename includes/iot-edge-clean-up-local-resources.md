---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046406"
---
### <a name="delete-local-resources"></a>Supprimer des ressources locales

Si vous souhaitez supprimer le runtime IoT Edge et les ressources associées à partir de votre appareil, utilisez les commandes appropriées pour le système d’exploitation de votre appareil. 

#### <a name="windows"></a>Windows

Désinstallez le runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Lorsque le runtime IoT Edge est supprimé, les conteneurs qu’il a créés sont arrêtés, mais existent toujours sur votre appareil. Affichez tous les conteneurs.

   ```powershell
   docker ps -a
   ```

Supprimez les conteneurs de runtime qui ont été créés sur votre appareil.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Supprimez tous les conteneurs qui ont été répertoriés dans la sortie `docker ps` en faisant référence aux noms de conteneur. 

#### <a name="linux"></a>Linux

Supprimez le runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Lorsque le runtime IoT Edge est supprimé, les conteneurs qu’il a créés sont arrêtés, mais existent toujours sur votre appareil. Affichez tous les conteneurs.

   ```bash
   sudo docker ps -a
   ```

Supprimez les conteneurs de runtime qui ont été créés sur votre appareil.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Supprimez tous les conteneurs qui ont été répertoriés dans la sortie `docker ps` en faisant référence aux noms de conteneur. 

Supprimez le runtime de conteneurs.

   ```bash
   sudo apt-get remove --purge moby
   ```