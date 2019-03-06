---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963361"
---
### <a name="launch-the-publish-wizard"></a>Lancer l’assistant de publication

Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **SharingService**, puis sélectionnez **Publier**.

L’Assistant Publication est lancé automatiquement. Sélectionnez **App Service** > **Publier** pour ouvrir la boîte de dialogue **Créer App Service**.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Dans la boîte de dialogue **Créer App Service**, cliquez sur **Ajouter un compte** puis connectez-vous à votre abonnement Azure. Si vous êtes déjà connecté, sélectionnez le compte que vous souhaitez dans la liste déroulante.

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

Dans la boîte de dialogue **Configurer le plan d’hébergement**, utilisez les paramètres spécifiés dans la table.

| Paramètre | Valeur suggérée | Description |
|-|-|-|
|Plan App Service| MySharingServicePlan | Nom du plan App Service. |
| Lieu | USA Ouest | Centre de données dans lequel l’application web est hébergée. |
| Taille | Gratuit | Le [niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) détermine les fonctionnalités d’hébergement. |

Sélectionnez **OK**.

### <a name="create-and-publish-the-web-app"></a>Créer et publier l’application web

Dans **Nom de l’application**, saisissez un nom d’application unique (les caractères valides sont `a-z`, `0-9` et `-`) ou acceptez le nom unique généré automatiquement. L’URL de l’application web est `https://<app_name>.azurewebsites.net`, où `<app_name>` est le nom de votre application.

Sélectionnez **Créer** pour commencer à créer les ressources Azure.

Une fois que l’Assistant a terminé, il publie l’application web ASP.NET Core sur Azure, puis il lance l’application dans le navigateur par défaut.

![Application web ASP.NET publiée dans Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Le nom de l’application spécifié à [l’étape de création et de publication](#create-and-publish-the-web-app) est utilisé en tant que préfixe d’URL au format `https://<app_name>.azurewebsites.net`. Notez cette URL, car elle sera utilisée par la suite.
