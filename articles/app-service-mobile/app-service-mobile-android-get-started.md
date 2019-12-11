---
title: Créer une application Android
description: Suivez ce tutoriel pour bien démarrer avec l’utilisation des backends d’applications mobiles Azure pour développer des applications Android.
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d63faf5c06e648e4bd886d6e62c35a2b451c8c44
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668587"
---
# <a name="create-an-android-app"></a>Créer une application Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center prend en charge les services intégrés essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent superviser l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics**, puis interagir avec les utilisateurs à l’aide du service **Push**. Les développeurs peuvent aussi utiliser **Auth** pour authentifier leurs utilisateurs ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud.
>
> Si vous souhaitez intégrer des services cloud à votre application mobile, inscrivez-vous à [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dès aujourd’hui.

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel montre comment ajouter un backend cloud à une application mobile Android à l’aide d’un backend d’application mobile Azure.  Vous allez créer un backend d’application mobile et une simple application Android *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Android sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Prérequis
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Outils de développement Android](https://developer.android.com/sdk/index.html), qui incluent l’environnement de développement intégré Android Studio et la dernière plateforme Android.
* Azure Mobile Android SDK.
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et configurer le projet client et de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Exécuter l’application Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
