---
title: Fichier include
description: Fichier include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ecd7e070c353d706865b245427758c11a7bbfa5d
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111896327"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associer un compte Azure Storage à IoT Hub

Vous devez disposer d’un compte Stockage Azure associé à votre IoT Hub. Pour plus d’informations sur la création d’un compte, consultez [Créer un compte de stockage](../articles/storage/common/storage-account-create.md). Lorsque vous associez un compte de stockage Azure avec un IoT Hub, celui-ci génère un URI SAS. Un appareil peut utiliser cet URI SAS pour charger en toute sécurité un fichier vers un conteneur d’objets blob. Le service IoT Hub et les Kits de développement logiciel (SDK) d’appareil coordonnent le processus qui génère l’URI SAS et le rend disponible pour un appareil à utiliser pour charger un fichier.

## <a name="create-a-container"></a>Créer un conteneur

Effectuez les étapes suivantes pour créer un conteneur de blobs pour votre compte de stockage :

1. Dans le volet gauche de votre compte de stockage, sous **Stockage des données**, sélectionnez **Conteneurs**.
1. Dans le panneau Conteneur, sélectionnez **+ Conteneur**.
1. Dans le volet **Nouveau conteneur** qui s’ouvre, donnez un nom à votre conteneur, puis sélectionnez **Créer**.

Après avoir créé un conteneur, suivez les instructions disponibles dans [Configurer les téléchargements de fichiers à l’aide du portail Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Assurez-vous qu’un conteneur de blobs est associé à votre hub IoT et que les notifications de fichier sont activées.

![Activer les notifications de fichier dans le portail](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
