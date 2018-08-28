---
title: Créer une plateforme Windows universelle sur Mobile Apps| Microsoft Docs
description: Suivez ce didacticiel pour commencer à utiliser des services principaux d’applications mobiles Azure pour le développement d’applications UWP en C#, Visual Basic ou JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 28a741393fd4b7b4076449c90575f8a4ab30e0fc
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919908"
---
# <a name="create-a-windows-app"></a>Créer une application Windows

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel présente l’ajout d’un service principal cloud à une application de plateforme Windows universelle (UWP). Pour plus d’informations, consultez [Que sont les applications Mobile Apps ?](app-service-mobile-value-prop.md). Voici les captures d’écran générées à partir de l’application terminée :

![Application de bureau terminée](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile App pour les applications UWP.

## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio Community 2017].

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure

Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Télécharger et exécuter le projet client

Une fois que vous avez configuré le serveur principal de votre application Mobile App, vous pouvez créer une application cliente ou modifier une application existante pour se connecter à Azure. Dans cette section, vous téléchargez un projet d’exemple d’application UWP, personnalisé pour se connecter à votre serveur principal Mobile App.

1. De retour dans le panneau **Démarrage rapide** pour votre serveur principal Mobile App, cliquez sur **Créer une application** > **Télécharger**, puis extrayez les fichiers de projet compressés sur votre ordinateur local.

    ![Télécharger le projet de démarrage rapide Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

2. Ouvrez le projet UWP et appuyez sur la touche F5 pour déployer et exécuter l’application.
3. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel*, dans la zone de texte **Insérer un TodoItem**, puis cliquez sur **Enregistrer**.

    ![Démarrage rapide Windows - Bureau terminé](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Ceci envoie une demande POST vers le nouveau backend d'application mobile qui est hébergé dans Azure.

> [!TIP]
> Vous pouvez ajouter le projet d’application UWP à la solution avec le projet de serveur si vous utilisez le serveur principal .NET. Cela simplifie le débogage et les tests de l’application et du serveur principal dans la même solution Visual Studio. Pour ajouter un projet d’application UWP à la solution de serveur principal, vous devez utiliser Visual Studio 2017.

## <a name="next-steps"></a>Étapes suivantes

* [Ajout de l’authentification à votre application](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Ajouter des notifications Push à votre application](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Apprenez à ajouter la prise en charge des notifications Push à votre application et à configurer le serveur principal d’applications mobiles pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile App. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://go.microsoft.com/fwLink/p/?LinkID=534203
