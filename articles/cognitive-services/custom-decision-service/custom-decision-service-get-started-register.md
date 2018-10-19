---
title: Inscrire une application - Service Décision personnalisée
titlesuffix: Azure Cognitive Services
description: Guide détaillé pour inscrire une nouvelle application auprès du service Décision personnalisée d’Azure.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: 598300597856d858095ff7c2e2cf9e9264190a9d
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365398"
---
# <a name="register-your-application"></a>Inscrivez votre application

Pour utiliser la fonctionnalité Service Décision personnalisée pour votre application, inscrivez-la sur le portail. Cet article vous explique comment faire.

1. Accédez à la [page d’accueil](https://ds.microsoft.com/) de la fonctionnalité Service Décision personnalisée. Dans le ruban, cliquez sur **My Portal** (Mon portail), comme illustré sur l’image :

    ![My Portal (Mon portail)](./media/portal.png)

    Si vous n’êtes pas connecté, le portail vous invite à le faire avec votre [compte Microsoft](https://account.microsoft.com/account). Une fois connecté, le portail affiche votre compte Microsoft dans le coin supérieur droit de la page.

2. Pour inscrire votre application, cliquez sur le bouton **Nouvelle application**.

3. Dans la boîte de dialogue, choisissez un ID d’application pour votre application. La fonctionnalité Service Décision personnalisée requiert un ID unique pour chaque application. Si une autre personne a déjà eu cet ID, le système vous invite à en choisir un autre.

4. Spécifiez une API Ensemble d’actions. Ce paramètre est un flux Atom ou RSS qui communique le contenu disponible pour votre application à la fonctionnalité Service Décision personnalisée. Entrez un nom pour le flux, ainsi que l’URL à partir de laquelle il est pris en charge. Pour effectuer cette étape ultérieurement, cliquez sur le bouton **Flux**, puis cliquez sur le bouton **Nouveau flux**. Un exemple qui crée un flux RSS est décrit plus loin.

5. Pour inscrire votre application, cochez la case **Application personnalisée** dans le coin inférieur gauche. Entrez une [chaîne de connexion](../../storage/common/storage-configure-connection-string.md) pour le compte de stockage Azure où vos données d’application sont consignées. Pour plus d’informations sur la création d’un compte de stockage, consultez [À propos des comptes de stockage Azure](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Étapes suivantes

* Apprenez à optimiser une [page web](custom-decision-service-get-started-browser.md) ou une [application de smartphone](custom-decision-service-get-started-app.md).
* Passez en revue un [didacticiel](custom-decision-service-tutorial-news.md) pour obtenir un exemple plus détaillé.
* Consultez les [informations de référence sur les API](custom-decision-service-api-reference.md) pour en savoir plus sur les fonctionnalités fournies.