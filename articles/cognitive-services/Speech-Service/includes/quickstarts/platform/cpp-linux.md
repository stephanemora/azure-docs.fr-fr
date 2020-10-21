---
title: 'Démarrage rapide : Configuration de la plateforme du kit SDK Speech pour C++ (Linux) – Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide pour configurer votre plateforme pour C++ sur Linux avec le kit SDK du service Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 1befc26d6c6d0a12af57c8a0d3d0e6d32f363f19
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096893"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous devez disposer des éléments suivants :

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Les plateformes Linux prises en charge nécessitent l’installation de certaines bibliothèques (`libssl` pour la prise en charge du protocole SSL et `libasound2` pour la prise en charge du son). Reportez-vous à votre distribution ci-dessous pour connaître les commandes nécessaires à l’installation des versions appropriées de ces bibliothèques.

   * Sur Ubuntu :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Sur Debian 9 :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * Sur RHEL/CentOS :

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - Sur RHEL/CentOS 7, suivez les instructions décrivant [comment configurer RHEL/CentOS 7 pour le Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list.md)]
