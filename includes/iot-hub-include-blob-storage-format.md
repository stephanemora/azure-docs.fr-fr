---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162744"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Les données peuvent être écrites dans un espace de stockage d’objets blob au format [Apache Avro](https://avro.apache.org/), qui est la valeur par défaut, ou JSON (préversion). 
>    
> La fonctionnalité permettant d’encoder au format JSON est en préversion dans toutes les régions où IoT Hub est disponible, à l’exception des régions USA Est, USA Ouest et Europe Ouest. Le format d’encodage peut être défini uniquement au moment de configurer le point de terminaison du stockage d’objets blob. Le format ne peut pas être modifié pour un point de terminaison déjà configuré. Quand vous utilisez l’encodage JSON, vous devez définir contentType sur JSON et contentEncoding sur UTF-8 dans les propriétés système de message. 
>
> Pour plus d’informations sur l’utilisation d’un point de terminaison de stockage d’objets blob, consultez les [recommandations en matière de routage vers un espace de stockage d’objets blob](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>