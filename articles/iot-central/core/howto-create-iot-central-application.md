---
title: Créer une application IoT Central | Microsoft Docs
description: Cet article décrit les options permettant de créer une application IoT Central, notamment à partir du site d’Azure IoT Central, du portail Azure et d’un environnement de ligne de commande.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 956e84e8eb7281541fbb787a6ce7557a1f5fee03
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110586489"
---
# <a name="create-an-iot-central-application"></a>Créer une application IoT Central

Il existe plusieurs façons de créer une application IoT Central. Vous pouvez utiliser l’une des méthodes basées sur l’interface graphique utilisateur si vous préférez une approche manuelle, ou l’une des méthodes CLI ou programmatiques si vous souhaitez automatiser le processus.

Quelle que soit l’approche choisie, les options de configuration sont les mêmes et le processus prend généralement moins d’une minute.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>Options

Cette section décrit les options disponibles lorsque vous créez une application IoT Central. Selon la méthode que vous choisissez, vous devrez peut-être fournir les options dans un formulaire ou sous forme de paramètres de ligne de commande :

### <a name="pricing-plans"></a>Plans tarifaires

Le plan *gratuit* vous permet de créer une application IoT Central et de l’essayer pendant sept jours. Le plan gratuit :

- ne nécessite pas d’abonnement Azure ;
- peut uniquement être créé et géré sur le site d’[Azure IoT Central](https://aka.ms/iotcentral) ;
- vous permet de connecter jusqu’à cinq appareils ;
- peut être mis à niveau vers un plan standard si vous souhaitez conserver votre application.

Les plans *standard* :

- nécessitent un abonnement Azure. Vous devez avoir au moins un accès **Contributeur** dans votre abonnement Azure. Si vous avez créé l’abonnement vous-même, vous êtes automatiquement un administrateur disposant d’un accès suffisant. Consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle Azure ?](../../role-based-access-control/overview.md) pour en savoir plus ;
- vous permettent de créer et de gérer des applications IoT Central à l’aide de l’une des méthodes disponibles ;
- vous permettent de connecter autant d’appareils que vous le souhaitez. Vous êtes facturé par appareil. Consultez [Tarification Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/) pour en savoir plus ;
- ne peuvent pas être rétrogradés à un plan gratuit, mais peuvent être mis à niveau ou rétrogradés vers d’autres plans standard.

Le tableau suivant récapitule les différences entre les trois plans standard :

| Nom du plan | Appareils gratuits | Messages/mois | Cas d’utilisation |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | Quelques messages par jour |
| S1        | 2            | 5 000          | Quelques messages par heure |
| S2        | 2            | 30,000         | Messages tous les quelques minutes |

Pour en savoir plus, consultez [Gérer sa facture dans une application IoT Central](howto-view-bill.md).

### <a name="application-name"></a>Nom de l'application

Le _nom de l’application_ que vous choisissez apparaît dans la barre de titre de chaque page de votre application IoT Central. Il apparaît également sur la vignette de votre application sur la page **Mes applications** du site d’[Azure IoT Central](https://aka.ms/iotcentral).

Le _sous-domaine_ que vous choisissez identifie de manière unique votre application. Le sous-domaine fait partie de l’URL que vous utilisez pour accéder à l’application. L’URL d’une application IoT Central se présente comme suit : `https://yoursubdomain.azureiotcentral.com`.

### <a name="application-template-id"></a>ID du modèle d’application

Le modèle d’application que vous choisissez détermine le contenu initial de votre application, tel que les tableaux de bord et les modèles d’appareil. Pour une application personnalisée, utilisez `iotc-pnp-preview` comme ID de modèle.

Pour en savoir plus sur les modèles d’application personnalisés et sectoriels, consultez [Présentation des modèles d’application](concepts-app-templates.md).

### <a name="billing-information"></a>Informations de facturation

Si vous choisissez l’un des plans standard, vous devez fournir des informations de facturation :

- L’abonnement Azure que vous utilisez.
- Le répertoire qui contient l’abonnement que vous utilisez.
- L’emplacement où héberger votre application. IoT Central utilise les zones géographiques Azure comme emplacements : États-Unis, Europe, Asie-Pacifique, Australie, Royaume-Uni ou Japon.

## <a name="azure-iot-central-site"></a>Site d’Azure IoT Central

Le moyen le plus simple de commencer à créer des applications IoT Central est de le faire sur le site d’[Azure IoT Central](https://aka.ms/iotcentral).

La [build](https://apps.azureiotcentral.com/build) vous permet de sélectionner le modèle d’application que vous souhaitez utiliser :

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="Capture d’écran de la page de build qui vous permet de choisir un modèle d’application.":::

Si vous sélectionnez **Créer une application**, vous pouvez fournir les informations nécessaires pour créer une application à partir du modèle :

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="Capture d’écran montrant la page de création d’application pour IoT Central.":::

La page **Mes applications** répertorie toutes les applications IoT Central auxquelles vous avez accès. La liste comprend les applications que vous avez créées et celles auxquelles vous avez été autorisé à accéder.

> [!TIP]
> Toutes les applications que vous créez à l’aide d’un plan tarifaire standard sur le site d’Azure IoT Central utilisent le groupe de ressources **IOTC** dans votre abonnement. Les approches décrites dans la section suivante vous permettent de choisir un groupe de ressources à utiliser.

## <a name="other-approaches"></a>Autres approches

Vous pouvez également utiliser les approches suivantes pour créer une application IoT Central :

- [Créer une application IoT Central à partir du portail Azure](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [Créer une application IoT Central à l’aide d’Azure CLI](howto-manage-iot-central-from-cli.md#create-an-application)
- [Créer une application IoT Central à l’aide de PowerShell](howto-manage-iot-central-from-powershell.md#create-an-application)
- [Créer une application IoT Central par programmation](howto-manage-iot-central-programmatically.md)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure CLI, nous vous suggérons l’étape suivante :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
