---
title: Configurer Postman pour les appels d’API REST Azure Media Services
description: Cet article explique comment configurer Postman pour les appels d’API REST Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 8dd6d25d1535bdc09ad7559c059424fcb531757f
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706604"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Configurer Postman pour les appels d’API REST Media Services v2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Ce didacticiel vous montre comment configurer **Postman** afin de l’utiliser pour appeler l’API REST Azure Media Services (AMS). Le didacticiel montre comment importer des fichiers d’environnement et de collection dans **Postman**. La collection contient des définitions groupées de requêtes HTTP qui appellent les API REST de Azure Media Services (AMS). Le fichier d’environnement contient des variables qui sont utilisées par la collection.

Cet environnement et la collection sont utilisés dans les articles qui indiquent comment effectuer diverses tâches liées aux API REST Azure Media Services.

## <a name="prerequisites"></a>Prérequis

- Installez le client REST [Postman](https://www.getpostman.com/) pour exécuter les API REST indiquées dans certains des didacticiels REST AMS. 

    Nous utilisons **Postman** mais n’importe quel outil REST serait approprié. Les autres solutions possibles sont : **Visual Studio Code** avec le plug-in REST ou **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurer l’environnement 

1. Créez un fichier .json qui contient les variables d’environnement utilisées dans les didacticiels AMS. Nommez le fichier (**AzureMediaServices.postman_environment.json**). Ouvrez le fichier et collez le code qui définit l’environnement Postman à partir de [cette liste de code](postman-environment.md). 
2. Ouvrez **Postman**.
3. Sur la droite de l’écran, sélectionnez l’option **Gérer environnement**.

    ![Capture d’écran de l’option Gérer l’environnement sélectionnée](./media/media-services-rest-upload-files/postman-create-env.png)
4. Dans la boîte de dialogue **Gérer environnement**, cliquez sur **Importer**.
5. Naviguez et sélectionnez le fichier **AzureMediaServices.postman_environment.json**.
6. L’environnement **AzureMedia** est ajouté.
7. Fermez la boîte de dialogue.
8. Sélectionnez l’environnement **AzureMedia**.

    ![Capture d’écran de l’environnement AzureMedia sélectionné](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurer la collection

1. Créez un fichier .json qui contient la collection **Postman** avec toutes les opérations nécessaires pour charger un fichier dans Media Services. Nommez le fichier (par exemple **AzureMediaServicesOperations.postman_collection.json**). Ouvrez le fichier et collez le code qui définit la collection **Postman** à partir de [cette liste de code](postman-collection.md).
2. Cliquez sur **Importer** pour importer le fichier de la collection.
3. Choisissez le fichier **AzureMediaServicesOperations.postman_collection.json**.

    ![Capture d’écran de la boîte de dialogue Importer avec l’option Choisir les fichiers sélectionnée](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article [charger des ressources](media-services-rest-upload-files.md).  
