---
title: Gérer une application IoT Central par programme | Microsoft Docs
description: Cet article explique comment créer et gérer une application IoT Central par programme. Vous pouvez afficher, modifier et supprimer l’application à l’aide de Kits de développement logiciel (SDK) pour plusieurs langages tels que JavaScript, Python, C#, Ruby et Go.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.openlocfilehash: 2feb4d85e7be4076eef8b2796d15b622f6b17c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104658299"
---
# <a name="manage-iot-central-programmatically"></a>Gérer une application IoT Central par programme

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central sur le site web du [Gestionnaire d’applications Azure IoT Central](https://aka.ms/iotcentral), vous pouvez gérer vos applications par programme avec les Kits de développement logiciel (SDK) Azure. Les langages pris en charge sont JavaScript, Python, C#, Ruby et Go.

## <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Le tableau suivant répertorie les référentiels de Kits de développement logiciel (SDK) et les commandes d’installation de packages :

| Référentiel de Kits de développement logiciel (SDK) | Installation de package |
| -------------- | ------------ |
| [Kit de développement logiciel (SDK) Azure IotCentralClient pour JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Kit de développement logiciel (SDK) Microsoft Azure pour Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Kit de développement logiciel (SDK) Azure pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Kit de développement logiciel (SDK) Microsoft Azure pour Ruby - Gestion des ressources (préversion)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Kit de développement logiciel (SDK) Azure pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Package Maven](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Kit de développement logiciel (SDK) Azure pour Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Publications des packages](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Exemples

Le référentiel [Exemples de Kits de développement logiciel (SDK) Azure IoT Central ARM](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) contient des exemples de code pour plusieurs langages de programmation qui vous montrent comment créer, mettre à jour, répertorier et supprimer des applications Azure IoT Central.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les applications Azure IoT Central par programme, la prochaine étape consisterait à en savoir plus sur le service [Azure Resource Manager](../../azure-resource-manager/management/overview.md).