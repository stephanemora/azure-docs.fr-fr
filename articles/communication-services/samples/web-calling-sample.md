---
title: Azure Communication Services - Exemple d’appel web
titleSuffix: An Azure Communication Services sample
description: Découvrez l’exemple d’appel web Communication Services.
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 35c3bd0213b6d895a81605f5858f924182e1e307
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168856"
---
# <a name="get-started-with-the-web-calling-sample"></a>Bien démarrer avec l’exemple d’appel web

L’exemple d’appel web est une application web qui sert de procédure pas à pas pour les différentes fonctionnalités fournies par le SDK web Communication Services Calling.

Cet exemple a été conçu pour les développeurs, et facilite la prise en main de Communication Services. Son interface utilisateur est divisée en plusieurs sections, chacune comportant un bouton « Show code » qui vous permet de copier du code directement à partir de votre navigateur afin de le coller dans votre propre application Communication Services.

## <a name="get-started-with-the-web-calling-sample"></a>Bien démarrer avec l’exemple d’appel web

> [!IMPORTANT]
> [Cet exemple est disponible **sur GitHub**.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Suivez les indications de /Project/readme.md pour configurer le projet et l’exécuter localement sur votre ordinateur.
Une fois que l’[exemple d’appel web](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) est en cours d’exécution sur votre ordinateur, la page d’arrivée suivante s’affiche :

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Tutoriel sur l’appel web 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Tutoriel sur l’appel web 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>Provisionnement d’utilisateur et initialisation du kit SDK

Cliquez sur « Provisioning user and initialize SDK » pour initialiser votre kit SDK à l’aide d’un jeton provisionné par le service de provisionnement de jetons back-end. Ce service back-end se trouve dans `/project/webpack.config.js`.

Cliquez sur le bouton « Show code » pour voir l’exemple de code que vous pouvez utiliser dans votre propre solution.

Une fois votre kit SDK initialisé, vous devriez voir ce qui suit :

:::image type="content" source="./media/user-provisioning.png" alt-text="Approvisionnement d’utilisateurs" lightbox="./media/user-provisioning.png":::

Vous pouvez maintenant commencer à passer des appels à l’aide de votre ressource Communication Services !

## <a name="placing-and-receiving-calls"></a>Passage et réception d’appels

Le SDK web Communication Services Calling autorise les appels **1:1**, **1:N** et de **groupe**.

Pour les appels sortants 1:1 ou 1:N, vous pouvez spécifier plusieurs identités utilisateur Communication Services à appeler à l’aide de valeurs séparées par des virgules. Vous pouvez également spécifier des numéros de téléphone traditionnels (RTC) à appeler à l’aide de valeurs séparées par des virgules.

Lors de l’appel de numéros de téléphone RTC, spécifiez votre ID d’appelant de substitution. Cliquez sur le bouton « Place call » pour passer un appel sortant :

:::image type="content" source="./media/place-a-call.png" alt-text="Passer un appel" lightbox="./media/place-a-call.png":::

Pour joindre un appel de groupe, entrez le GUID qui identifie l’appel et cliquez sur le bouton « Join group » :

:::image type="content" source="./media/join-a-group-call.png" alt-text="Rejoindre un appel de groupe" lightbox="./media/join-a-group-call.png":::

Cliquez sur le bouton « Show code » pour afficher l’exemple de code permettant de placer des appels, de recevoir des appels et de joindre des appels de groupe.

Un appel actif se présente comme suit :

:::image type="content" source="./media/group-call.png" alt-text="Appel de groupe" lightbox="./media/group-call.png":::

Cet exemple fournit également des extraits de code pour les fonctionnalités suivantes :

  - Clic sur l’icône de vidéo pour activer/désactiver votre caméra vidéo
  - Clic sur l’icône de microphone pour activer/désactiver votre microphone
  - Clic sur l’icône de lecture pour activer/désactiver la mise en attente de l’appel
  - Clic sur l’icône d’écran pour démarrer/arrêter le partage de votre écran
  - Clic sur l’icône représentant une personne pour ajouter un participant à l’appel
  - Clic sur « Supprimer un participant » dans la liste des participants pour supprimer un participant spécifique de l’appel


## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Télécharger l’exemple à partir de GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Pour plus d’informations, consultez les articles suivants :

- Familiarisez-vous avec l’[utilisation du SDK Calling](../quickstarts/voice-video-calling/calling-client-samples.md)
- Apprenez-en davantage sur le [fonctionnement des appels](../concepts/voice-video-calling/about-call-types.md)
- Lire la [documentation de référence sur les API](/javascript/api/azure-communication-services/@azure/communication-calling/)
- Consultez l’exemple [Contoso Med App](https://github.com/Azure-Samples/communication-services-contoso-med-app).

## <a name="additional-reading"></a>Documentation supplémentaire

- [Exemples](./overview.md) : Recherchez d’autres exemples sur notre page Vue d’ensemble des exemples.
- [Redux](https://redux.js.org/) - Gestion de l’état côté client
- [FluentUI](https://aka.ms/fluent-ui) - Bibliothèque d’interface utilisateur, Technologie Microsoft
- [React](https://reactjs.org/) - Bibliothèque pour la création d’interfaces utilisateur
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Framework pour la création d’applications web
