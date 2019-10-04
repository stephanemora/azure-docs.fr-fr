---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ecdd419331c88e712644851f9213861f882cf0f6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177223"
---
## <a name="prepare-your-web-app"></a>Préparation de votre application web

Pour lier un certificat SSL personnalisé (un certificat tiers ou un certificat App Service) à votre application web, votre [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) doit se trouver dans le niveau **De base**, **Standard**, **Premium** ou **Isolé**. Au cours de cette étape, vous allez vous assurer que votre application web se trouve dans le niveau de tarification pris en charge.

### <a name="log-in-to-azure"></a>Connexion à Azure

Ouvrez le [portail Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Accès à votre application web

Dans le menu de gauche, cliquez sur **App Services** puis sur le nom de votre application web.

![Sélectionner de l’application web](./media/app-service-ssl-prepare-app/select-app.png)

Vous accédez à la page de gestion de votre application web.  

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

Dans la navigation de gauche de la page de votre application web, accédez à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)** .

![Menu Monter en puissance](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Vérifiez que votre application web ne se trouve pas dans le niveau **F1** ou **D1**. Le niveau actuel de votre application web est encadré d’un rectangle bleu foncé.

![Vérification du niveau de tarification](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Le certificat SSL personnalisé n’est pas pris en charge pour les niveaux **F1** et **D1**. Si vous avez besoin de monter en puissance, consultez la section ci-après. Sinon, fermez la page **Monter en puissance** et ignorez la section [Évolution de votre plan App Service](#scale-up-your-app-service-plan).

### <a name="scale-up-your-app-service-plan"></a>Évolution de votre plan App Service

Sélectionnez un niveau payant (**B1**, **B2**, **B3**, ou n’importe quel niveau dans la catégorie **Production**). Pour obtenir des options supplémentaires, cliquez sur **Afficher d’autres options**.

Cliquez sur **Appliquer**.

![Sélection du niveau tarifaire](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Lorsque la notification suivante s’affiche, cela signifie que la montée en charge est terminée.

![Notification de montée en puissance](./media/app-service-ssl-prepare-app/scale-notification.png)

