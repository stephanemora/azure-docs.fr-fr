---
title: Gérer une application Azure IoT Central | Microsoft Docs
description: En tant qu’administrateur, découvrez comment gérer votre application Azure IoT Central en changeant son nom et son URL et en chargeant une image, puis comment copier et supprimer une application
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4c729d2502464df021df4d03e989d489b2d1ecea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72942838"
---
# <a name="manage-your-iot-central-application"></a>Gérer votre application IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Cet article destiné aux administrateurs vous explique comment gérer une application en changeant son nom et son URL et en chargeant une image. Vous apprendrez également à copier et à supprimer une application dans votre application Azure IoT Central.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application. 

## <a name="change-application-name-and-url"></a>Modifier le nom et l’URL de l’application

À la page **Paramètres d’application**, vous pouvez modifier le nom et l’URL de votre application, puis sélectionner **enregistrer**.

![Page Paramètres de l’application](media/howto-administer/image0-a.png)

Si votre administrateur crée un thème personnalisé pour votre application, cette page contient une option permettant de masquer le **Nom de l’application** dans l’interface utilisateur. Cette option peut s’avérer utile, par exemple si le nom de l’application figure dans le logo inclus dans le thème personnalisé. Pour plus d’informations, consultez [Personnaliser l’interface utilisateur d’Azure IoT Central](./howto-customize-ui.md).

> [!Note]
> Si vous changez votre URL, l’ancienne peut être prise par un autre client Azure IoT Central. Dans ce cas, vous ne pouvez plus l’utiliser. Quand vous changez votre URL, l’ancienne ne fonctionne plus et vous devez indiquer à vos utilisateurs la nouvelle URL à utiliser.

## <a name="prepare-and-upload-image"></a>Préparer et charger des images

Pour modifier l’image de l’application, consultez [Préparer et charger des images dans votre application Azure IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copier une application

Vous pouvez créer une copie de n’importe quelle application, à l’exception des instances d’appareils, de l’historique des données d’un appareil et des données utilisateur. Cette copie est une application avec paiement à l’utilisation qui vous sera facturée. Impossible de créer une version d’évaluation de cette manière.

Sélectionnez **Copier**. Dans la boîte de dialogue, entrez les détails de la nouvelle application avec paiement à l’utilisation. Ensuite, sélectionnez **Copier** pour confirmer que vous souhaitez continuer. En savoir plus sur les champs de ce formulaire dans le guide de démarrage rapide [Créer une application](quick-deploy-iot-central.md).

![Page Paramètres de l’application](media/howto-administer/appcopy2.png)

Une fois l’application correctement copiée, vous pouvez accéder à la nouvelle application à l’aide du lien.

![Page Paramètres de l’application](media/howto-administer/appcopy3a.png)

La copie d’une application copie également la définition des règles et de l’action de messagerie. Certaines actions telles que Flow et Logic Apps sont liées à des règles spécifiques par le biais de l’ID de règle. Quand une règle est copiée dans une autre application, elle obtient son propre ID de règle. Dans ce cas, les utilisateurs doivent créer une action, puis lui associer la nouvelle règle. En général, il est judicieux de vérifier les règles et les actions pour vous assurer qu’elles sont à jour dans la nouvelle application.

> [!WARNING]
> Si un tableau de bord inclut des vignettes qui affichent des informations d’appareils spécifiques, ces vignettes affichent alors **La ressource demandée est introuvable** dans la nouvelle application. Vous devez reconfigurer ces vignettes pour qu’elles affichent des informations sur les appareils de votre nouvelle application.

## <a name="delete-an-application"></a>Supprimer une application

Utilisez le bouton **Supprimer** pour supprimer définitivement votre application IoT Central. Cette action supprime définitivement toutes les données qui lui sont associées.

> [!Note]
> Pour pouvoir supprimer une application, vous devez aussi disposer des autorisations permettant de supprimer des ressources de l’abonnement Azure que vous avez choisi lors de la création de l’application. Pour en savoir plus, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Gérer par programme

Des packages de kit de développement logiciel (SDK) Azure Resource Manager d’IoT Central sont disponibles pour Node, Python, C#, Ruby, Java et Go. Vous pouvez utiliser ces packages pour créer, lister, mettre à jour ou supprimer des applications IoT Central. Les packages incluent des ressources d’assistance pour gérer l’authentification et les erreurs.

Vous trouverez des exemples montrant comment utiliser les kits de développement logiciel (SDK) Azure Resource Manager à l’adresse [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Pour plus d’informations, consultez les packages et dépôts GitHub suivants :

| Langage | Référentiel | Package |
| ---------| ---------- | ------- |
| Nœud | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Étapes suivantes
 
Maintenant que vous avez appris à administrer votre application Azure IoT Central, nous vous recommandons d’en apprendre davantage sur la [gestion des utilisateurs et des rôles](howto-manage-users-roles.md) dans Azure IoT Central.