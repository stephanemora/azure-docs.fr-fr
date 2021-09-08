---
title: Fichier include
description: Fichier include
services: app-service
author: jasonfreeberg
ms.service: app-service
ms.topic: include
ms.date: 08/27/2021
ms.author: jafreebe
ms.custom: include file
ms.openlocfilehash: a3030eedcc6b00457338f4a71c27965261280a80
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225468"
---
En fonction de la configuration de mise en réseau de vos applications web, l’accès direct au site à partir de votre environnement local peut être bloqué. Pour déployer votre code dans ce scénario, vous pouvez publier votre fichier ZIP sur un système de stockage accessible depuis l’application web et déclencher l’application pour *extraire* le ZIP de l’emplacement de stockage, au lieu de *pousser* le ZIP vers l’application web. Consultez [cet article sur le déploiement d’applications web sécurisées en réseau](https://azure.github.io/AppService/2021/03/01/deploying-to-network-secured-sites-2.html) pour plus d'informations. 
