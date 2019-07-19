---
title: Créer une application Cordova sur Azure App Service Mobile Apps | Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser des backends d’applications mobiles Azure pour le développement Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: elamalani
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
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a1634cfa7b701401927859a9fbd3fb203b61a77
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445647"
---
# <a name="create-an-apache-cordova-app"></a>Créer une application Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investit dans des services nouveaux et intégrés, essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent surveiller l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics** et interagir avec des utilisateurs à l’aide du service **Push**. Les développeurs peuvent également exploiter **Auth** pour authentifier leurs utilisateurs et le service **Data** pour conserver et synchroniser les données d’application dans le cloud. Découvrez [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started) dès aujourd’hui.
>

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel montre comment ajouter un backend cloud à une application mobile Apache Cordova à l’aide d’un backend d’application mobile Azure.  Vous créez un backend d’application mobile et une simple application Apache Cordova *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Apache Cordova sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Prérequis
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un PC équipé de [Visual Studio Community 2017] ou d’une version plus récente.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

Vous pouvez également contourner Visual Studio et utiliser directement la ligne de commande Apache Cordova.  L’utilisation de la ligne de commande est utile lorsque vous terminez le didacticiel sur un ordinateur Mac.  La compilation d’applications client Apache Cordova à l'aide de la ligne de commande n'est pas couverte par ce didacticiel.

## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et configurer le projet client et de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Télécharger et exécuter l'application Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Portail Azure] : https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx