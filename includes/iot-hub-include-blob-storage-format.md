---
title: Fichier include
description: Fichier include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b566fc5c8d490d9fbad8ba64217d9ab3110cd709
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313542"
---
> [!NOTE]
> Les données peuvent être écrites dans un espace de stockage d’objets blob au format [Apache Avro](https://avro.apache.org/), qui est la valeur par défaut, ou JSON. 
>    
> Le format d’encodage peut être défini uniquement au moment de configurer le point de terminaison du stockage d’objets blob. Le format ne peut pas être modifié pour un point de terminaison déjà configuré. Quand vous utilisez l’encodage JSON, vous devez définir contentType sur JSON et contentEncoding sur UTF-8 dans les propriétés système de message. 
>
> Pour plus d’informations sur l’utilisation d’un point de terminaison de stockage d’objets blob, voir les [recommandations en matière de routage vers un espace de stockage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>