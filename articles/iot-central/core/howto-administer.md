---
title: Modifier les paramètres d’application Azure IoT Central | Microsoft Docs
description: Apprenez à gérer votre application Azure IoT Central en changeant son nom et son URL, à charger une image et à supprimer une application
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: dadec45a23191763ad803560f1362c61917c95c5
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108756662"
---
# <a name="change-iot-central-application-settings"></a>Modifier les paramètres d’application IoT Central

Cet article explique comment gérer une application en changeant son nom et son URL, charger une image et supprimer une application dans votre application Azure IoT Central.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application.

## <a name="change-application-name-and-url"></a>Modifier le nom et l’URL de l’application

À la page **Paramètres d’application**, vous pouvez modifier le nom et l’URL de votre application, puis sélectionner **enregistrer**.

![Page Paramètres de l’application](media/howto-administer/image0-a.png)

Si votre administrateur crée un thème personnalisé pour votre application, cette page contient une option permettant de masquer le **Nom de l’application** dans l’interface utilisateur. Cette option est utile si le nom de l’application figure dans le logo de l’application inclus dans le thème personnalisé. Pour plus d’informations, consultez [Personnaliser l’interface utilisateur d’Azure IoT Central](./howto-customize-ui.md).

> [!Note]
> Si vous changez votre URL, l’ancienne peut être prise par un autre client Azure IoT Central. Dans ce cas, vous ne pouvez plus l’utiliser. Quand vous changez votre URL, l’ancienne ne fonctionne plus et vous devez indiquer à vos utilisateurs la nouvelle URL à utiliser.

## <a name="delete-an-application"></a>Supprimer une application

Utilisez le bouton **Supprimer** pour supprimer définitivement votre application IoT Central. Cette action supprime définitivement toutes les données qui lui sont associées.

> [!Note]
> Pour pouvoir supprimer une application, vous devez aussi disposer des autorisations permettant de supprimer des ressources de l’abonnement Azure que vous avez choisi lors de la création de l’application. Pour en savoir plus, consultez [Affecter des rôles Azure pour gérer l’accès à vos ressources d’abonnement Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="manage-programmatically"></a>Gérer par programme

Des packages de kit de développement logiciel (SDK) Azure Resource Manager d’IoT Central sont disponibles pour Node, Python, C#, Ruby, Java et Go. Vous pouvez utiliser ces packages pour créer, lister, mettre à jour ou supprimer des applications IoT Central. Les packages incluent des ressources d’assistance pour gérer l’authentification et les erreurs.

Vous trouverez des exemples montrant comment utiliser les kits de développement logiciel (SDK) Azure Resource Manager à l’adresse [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples).

Pour plus d’informations, consultez les packages et dépôts GitHub suivants :

| Langage | Référentiel | Package |
| ---------| ---------- | ------- |
| Nœud | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à administrer votre application Azure IoT Central, nous vous recommandons d’en apprendre davantage sur la [gestion des utilisateurs et des rôles](howto-manage-users-roles.md) dans Azure IoT Central.
