---
title: Créer une application Cordova sur Azure App Service Mobile Apps | Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser des backends d’applications mobiles Azure pour le développement Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
tags: ''
keywords: cordova, javascript, mobile, client
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: ac6c2b0f93c56de6e0a2b559645884b60d761ba8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240238"
---
# <a name="create-an-apache-cordova-app"></a>Créer une application Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel montre comment ajouter un backend cloud à une application mobile Apache Cordova à l’aide d’un backend d’application mobile Azure.  Vous créez un backend d’application mobile et une simple application Apache Cordova *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Apache Cordova sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un PC équipé de [Visual Studio Community 2017] ou d’une version plus récente.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

Vous pouvez également contourner Visual Studio et utiliser directement la ligne de commande Apache Cordova.  L’utilisation de la ligne de commande est utile lorsque vous terminez le didacticiel sur un ordinateur Mac.  La compilation d’applications client Apache Cordova à l'aide de la ligne de commande n'est pas couverte par ce didacticiel.

## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et de configurer le projet client et serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Télécharger et exécuter l'application Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Portail azure] : https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx