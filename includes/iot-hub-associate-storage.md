---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 892dd050829dce242035e2b875ea43ed13910d4a
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246072"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associer un compte Azure Storage à IoT Hub

Étant donné que l’application d’appareil simulé charge un fichier dans un objet Blob, vous devez disposer d’un compte [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) associé à IoT Hub. Lorsque vous associez un compte de stockage Azure avec un IoT Hub, celui-ci génère un URI SAS. Un appareil peut utiliser cet URI SAS pour charger en toute sécurité un fichier vers un conteneur d’objets blob. Le service IoT Hub et les Kits de développement logiciel (SDK) d’appareil coordonnent le processus qui génère l’URI SAS et le rend disponible pour un appareil à utiliser pour charger un fichier.

Pour associer un compte de stockage Azure à votre IoT Hub, suivez les instructions de [Configurer les chargements de fichiers IoT Hub à l’aide du portail Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Assurez-vous qu’un conteneur de blobs est associé à votre IoT Hub et que les notifications de fichier sont activées.

![Activer les notifications de fichier dans le portail](media/iot-hub-associate-storage/enable-file-notifications.png)