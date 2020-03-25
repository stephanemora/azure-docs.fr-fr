---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176752"
---
### <a name="open-the-publish-wizard"></a>Ouvrir l’Assistant de publication

Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **SharingService**, puis sélectionnez **Publier**.

L’Assistant de publication démarre. Sélectionnez **App Service** > **Publier** pour ouvrir la boîte de dialogue **Créer App Service**.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Dans la boîte de dialogue **Créer App Service**, sélectionnez **Ajouter un compte**, puis connectez-vous à votre abonnement Azure. Si vous êtes déjà connecté, sélectionnez le compte que vous souhaitez dans la liste déroulante.

> [!NOTE]
> Si vous êtes déjà connecté, ne sélectionnez pas encore **Créer**.
>

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [resource group intro text](resource-group.md)]

En regard de **Groupe de ressources**, sélectionnez **Nouveau**.

Nommez le groupe de ressources **myResourceGroup**, puis sélectionnez **OK**.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [app-service-plan](app-service-plan.md)]

En regard de **Plan d’hébergement**, sélectionnez **Nouveau**.

Dans la boîte de dialogue **Configurer le plan d’hébergement**, utilisez ces paramètres :

| Paramètre | Valeur suggérée | Description |
|-|-|-|
|Plan App Service| MySharingServicePlan | Nom du plan App Service. |
| Location | USA Ouest | Centre de données dans lequel l’application web est hébergée. |
| Size | Gratuit | [Niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) déterminant les fonctionnalités d’hébergement. |

Sélectionnez **OK**.

### <a name="create-and-publish-the-web-app"></a>Créer et publier l’application web

Dans **Nom de l’application**, entrez un nom d’application unique (les caractères valides sont `a-z`, `0-9` et `-`) ou acceptez le nom unique généré automatiquement. L’URL de l’application web est `https://<app_name>.azurewebsites.net`, où `<app_name>` est le nom de votre application.

Sélectionnez **Créer** pour commencer à créer les ressources Azure.

À la fin de l’Assistant, il publie l’application web ASP.NET Core sur Azure, puis il ouvre l’application dans votre navigateur par défaut.

![Application web ASP.NET publiée dans Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Le nom d’application que vous avez utilisé dans cette section est utilisé en tant que le préfixe d’URL au format `https://<app_name>.azurewebsites.net`. Prenez note de cette URL, car vous en aurez besoin.
