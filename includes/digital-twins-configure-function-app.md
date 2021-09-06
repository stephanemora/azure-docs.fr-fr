---
author: baanders
description: Inclure un fichier décrivant comment configurer une fonction Azure pour fonctionner avec Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 6aabec311df33c1d08d12b48117d9763ccf1761d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801075"
---
Vous pouvez configurer l’accès de sécurité pour l’application de fonction en utilisant l’interface Azure CLI ou le portail Azure. Suivez les étapes correspondant à l’option choisie.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

[!INCLUDE [digital-twins-configure-function-app-cli.md](digital-twins-configure-function-app-cli.md)]

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Effectuez les étapes suivantes dans le [portail Azure](https://portal.azure.com/).

#### <a name="assign-an-access-role"></a>Attribuer un rôle d’accès

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

Une identité managée affectée par le système permet aux ressources Azure de s’identifier auprès des services cloud (comme Azure Key Vault, par exemple), sans stocker les informations d’identification dans le code. Une fois que vous avez activé l’identité managée affectée par le système, toutes les autorisations nécessaires peuvent être accordées par le biais du contrôle d’accès en fonction du rôle Azure. 

Le cycle de vie de ce type d’identité managée est lié au cycle de vie de cette ressource. De plus, chaque ressource ne peut avoir qu’une seule identité managée affectée par le système.

1. Dans le [portail Azure](https://portal.azure.com/), recherchez votre application de fonction en tapant son nom dans la zone de recherche. Sélectionnez votre application dans les résultats. 

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure : le nom de l’application de fonction est indiqué dans la barre de recherche du portail et le résultat de la recherche est mis en évidence.":::

1. Dans la page de l’application de fonction, sélectionnez __Identité__ dans le menu de gauche pour utiliser une identité managée pour la fonction. Sur la page __Attribué par le système__, vérifiez que le __Statut__ est défini sur **Activé**. Si ce n’est pas le cas, modifiez le statut maintenant, puis **enregistrez** la modification.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png" alt-text="Capture d’écran du portail Azure : dans la page Identité de l’application de fonction, l’option État est activée." lightbox="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png":::

1. Sélection d’__Attributions de rôles Azure__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png" alt-text="Capture d’écran du portail Azure. Dans la page Identité de la fonction Azure, sous Autorisations, le bouton Attributions de rôle Azure est mis en surbrillance." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png":::

    Sélectionnez __+ Ajouter une attribution de rôle (préversion)__ .

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png" alt-text="Capture d’écran du portail Azure. Dans la page Attributions de rôle Azure, le bouton Ajouter une attribution de rôle (préversion) est mis en surbrillance." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png":::

1. Dans la page __Ajouter une attribution de rôle (préversion)__ , sélectionnez les valeurs suivantes :

    * **Portée** : _Groupe de ressources_
    * **Abonnement**: Sélectionnez votre abonnement Azure.
    * **Groupe de ressources** : Sélectionnez votre groupe de ressources.
    * **Rôle** : _Propriétaire des données Azure Digital Twins_

    Enregistrez les détails en sélectionnant __Enregistrer__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-3.png" alt-text="Capture d’écran du portail Azure, montrant comment ajouter une nouvelle attribution de rôle. La boîte de dialogue montre les champs Étendue, Abonnement, Groupe de ressources et Rôle.":::

#### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Pour rendre l’URL de votre instance Azure Digital Twins accessible à votre fonction, vous pouvez définir une variable d’environnement pour celle-ci. Les paramètres d’application sont exposés en tant que variables d’environnement pour permettre d’accéder à l’instance Azure Digital Twins. Pour plus d’informations sur les variables d’environnement, consultez [Gérer votre application de fonction](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Pour définir une variable d'environnement avec l'URL de votre instance, recherchez d'abord le nom d'hôte de votre instance : 

1. Recherchez votre instance dans le [portail Azure](https://portal.azure.com). 
1. Dans le menu de gauche, sélectionnez __Vue d’ensemble__. 
1. Copiez la valeur __Nom d’hôte__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/instance-host-name.png" alt-text=" Capture d'écran du portail Azure. Sur la page Vue d’ensemble de l'instance, la valeur Nom d'hôte est mise en surbrillance. ":::

Vous pouvez maintenant créer un paramètre d’application :

1. Recherchez votre application de fonction dans la barre de recherche du portail, puis sélectionnez-la dans les résultats.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Capture d’écran du portail Azure. Le nom de l’application de fonction est indiqué dans la barre de recherche du portail. Le résultat de la recherche est mis en évidence.":::

1. Sur la gauche, sélectionnez __Configuration__ Puis, sous l’onglet __Paramètres de l’application__, sélectionnez __+ Nouveau paramètre d’application__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting.png" alt-text="Capture d’écran du portail Azure. Dans l’onglet Configuration de l’application de fonction, le bouton Nouveau paramètre d’application est mis en évidence.":::

1. Dans la fenêtre qui s’ouvre, utilisez la valeur de nom d’hôte copiée pour créer un paramètre d’application.
    * **Nom** : `ADT_SERVICE_URL`
    * **Valeur** : `https://<your-Azure-Digital-Twins-host-name>`
    
    Sélectionnez __OK__ pour créer un paramètre d’application.
    
    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-application-setting.png" alt-text="Capture d’écran du portail Azure. Dans la page Ajouter/modifier le paramètre d’application, les champs Nom et Valeur sont remplis. Le bouton OK est mis en surbrillance.":::

1. Une fois le paramètre créé, il doit apparaître sous l’onglet __Paramètres de l’application__ . Vérifiez qu’**ADT_SERVICE_URL** apparaît dans la liste. Enregistrez ensuite le nouveau paramètre d’application en sélectionnant __Enregistrer__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting-save-details.png" alt-text="Capture d’écran du portail Azure. Sous l’onglet des paramètres de l’application, le nouveau paramètre ADT_SERVICE_URL et le bouton Enregistrer sont tous les deux en surbrillance.":::

1. Tout changement apporté aux paramètres d’application nécessite un redémarrage de l’application. Par conséquent, sélectionnez __Continuer__ pour redémarrer votre application quand vous y êtes invité.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/save-application-setting.png" alt-text="Capture d’écran du portail Azure. Une note indique que toute modification apportée aux paramètres d’application entraînera le redémarrage de votre application.":::

---
