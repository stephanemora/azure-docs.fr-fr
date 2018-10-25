---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 08/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f3fa6c75fdaf30d13231bc3072bd671614c9a668
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650653"
---
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

2. Sélectionnez **Application Azure Function**, choisissez **Créer**, puis sélectionnez **Publier**.

    ![Choisir une cible de publication](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    Lorsque vous activez l’option **Exécuter à partir du ZIP**, votre application de fonction dans Azure s’exécute directement à partir du package de déploiement. Pour plus d’informations, consultez l’article [Run your Azure Functions from a package file](../articles/azure-functions/run-functions-from-deployment-package.md) (Exécuter vos fonctions Azure à partir d’un fichier de package).

3. Si vous n’avez pas déjà connecté Visual Studio à votre compte Azure, sélectionnez **Ajouter un compte...**.

4. Dans la boîte de dialogue **Créer App Service**, utilisez les paramètres d’**hébergement** comme spécifiés dans le tableau sous l’image :

    ![Boîte de dialogue Créer App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Paramètre      | Valeur suggérée  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie uniquement votre nouvelle application de fonction. |
    | **Abonnement** | Choisissez votre abonnement | Sélectionnez l’abonnement Azure à utiliser. |
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nom du groupe de ressources où créer votre application de fonction. Choisissez **Nouveau** pour créer un groupe de ressources.|
    | **[Plan App Service](../articles/azure-functions/functions-scale.md)** | Plan de consommation | Veillez à choisir **Consommation** sous **Taille** après avoir cliqué sur **Nouveau** pour créer un plan serverless. Choisissez également un **Emplacement** dans une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. Si vous exécutez un plan autre que le plan **Consommation**, vous devez gérer la [mise à l’échelle de votre application de fonction](../articles/azure-functions/functions-scale.md).  |
    | **[Compte de stockage](../articles/storage/common/storage-quickstart-create-account.md)** | Compte de stockage à usage général | Un compte de stockage Azure est obligatoire par le runtime Functions. Cliquez sur **Nouveau** pour créer un compte de stockage à usage général. Vous pouvez également utiliser un compte existant qui répond aux [exigences relatives aux comptes de stockage](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Cliquez sur **Créer** pour créer une application de fonctions et les ressources associées dans Azure avec ces paramètres et déployer votre code de projet de fonction. 

6. Une fois le déploiement terminé, notez la valeur **URL du site**, qui est l’adresse de votre application de fonctions dans Azure.

    ![Message de réussite de publication](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
