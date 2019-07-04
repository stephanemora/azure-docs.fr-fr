---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177684"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Vous vous apprêtez à acheminer les messages vers différentes ressources en fonction des propriétés jointes au message par l’appareil simulé. Les messages dont le routage n’est pas personnalisé sont envoyés au point de terminaison par défaut (messages/événements). Dans le prochain tutoriel, vous allez envoyer des messages à IoT Hub qui seront routés vers différentes destinations.

|value |Résultat|
|------|------|
|level="storage" |Écrire dans Stockage Azure.|
|level="critical" |Écrire dans une file d’attente Service Bus. Une application logique extrait le message de la file d’attente et utilise Office 365 pour envoyer le message par e-mail.|
|default |Afficher ces données à l’aide de Power BI.|

La première étape consiste à configurer le point de terminaison vers lequel les données seront routées. La deuxième étape consiste à configurer la route des messages qui utilise ce point de terminaison. Après avoir configuré le routage, vous pouvez examiner les points de terminaison et les routes des messages sur le portail.