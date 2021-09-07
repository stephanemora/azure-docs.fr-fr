---
title: Créer une application IoT Central | Microsoft Docs
description: Cet article décrit les options permettant de créer une application IoT Central, notamment à partir du site d’Azure IoT Central, du portail Azure et d’un environnement de ligne de commande.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: c9f9dec23209d8bc401313a7213239dff52a1023
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586200"
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

## <a name="copy-an-application"></a>Copier une application

Vous pouvez créer une copie de n’importe quelle application, à l’exception des instances d’appareils, de l’historique des données d’un appareil et des données utilisateur. La copie utilise un plan tarifaire Standard qui vous sera facturé. Vous ne pouvez pas créer une application qui utilise le plan Gratuit en copiant une application.

Sélectionnez **Copier**. Dans la boîte de dialogue, entrez les détails de la nouvelle application. Ensuite, sélectionnez **Copier** pour confirmer que vous souhaitez continuer. Pour plus d’informations sur les champs du formulaire, consultez [Créer une application](howto-create-iot-central-application.md).

:::image type="content" source="media/howto-create-iot-central-application/app-copy-1.png" alt-text="Capture d’écran montrant la page des paramètres de l’application.":::

:::image type="content" source="media/howto-create-iot-central-application/app-copy-2.png" alt-text="Capture d’écran montrant la page de paramètres « Copier l’application ».":::

Une fois l’application correctement copiée, vous pouvez accéder à la nouvelle application à l’aide du lien.

La copie d’une application copie également la définition des règles et de l’action de messagerie. Certaines actions, comme Flow et Logic Apps, sont liées à des règles spécifiques par le biais de l’ID de règle. Quand une règle est copiée dans une autre application, elle obtient son propre ID de règle. Dans ce cas, les utilisateurs doivent créer une action, puis lui associer la nouvelle règle. En général, il est judicieux de vérifier que les règles et les actions sont à jour dans la nouvelle application.

> [!WARNING]
> Si un tableau de bord inclut des vignettes qui affichent des informations d’appareils spécifiques, ces vignettes affichent alors **La ressource demandée est introuvable** dans la nouvelle application. Vous devez reconfigurer ces vignettes pour qu’elles affichent des informations sur les appareils de votre nouvelle application.

## <a name="create-and-use-a-custom-application-template"></a>Créer et utiliser un modèle d’application personnalisé

Lorsque vous créez une application Azure IoT Central, vous avez un choix de plusieurs exemples de modèles intégrés. Vous pouvez également créer vos propres modèles d’application à partir des applications IoT Central existantes. Vous pouvez ensuite utiliser vos propres modèles d’application lorsque vous créez des applications.

Lorsque vous créez un modèle d’application, il comprend les éléments suivants de votre application existante :

- Le tableau de bord par défaut de l’application, dont la disposition et toutes les vignettes que vous avez définies.
- Les modèles d’appareil, dont les mesures, les paramètres, les propriétés, les commandes et le tableau de bord.
- Les règles. Toutes les définitions de règle sont incluses. Toutefois, les actions, à l’exception des actions de messagerie, ne sont pas incluses.
- Groupes d’appareils, ce qui inclut leurs requêtes.

> [!WARNING]
> Si un tableau de bord inclut des vignettes qui affichent des informations d’appareils spécifiques, ces vignettes affichent alors **La ressource demandée est introuvable** dans la nouvelle application. Vous devez reconfigurer ces vignettes pour qu’elles affichent des informations sur les appareils de votre nouvelle application.

Lorsque vous créez un modèle d’application, il n’inclut pas les éléments suivants :

- Appareils
- Utilisateurs
- Définitions des exportations de données continues

Ajoutez ces éléments manuellement à toutes les applications créées depuis un modèle d’application.

Pour créer un modèle d’application à partir d’une application IoT Central existante :

1. Accédez à la section **Administration** de votre application.
1. Sélectionnez **Exportation de modèle d’application**.
1. Sur la page **Exportation de modèle d’application**, entrez un nom et une description pour votre modèle.
1. Sélectionnez le bouton **Exporter** pour créer le modèle d’application. Vous pouvez désormais copier le **Lien de partage** pour permettre à quelqu’un de créer une application depuis le modèle :

:::image type="content" source="media/howto-create-iot-central-application/create-template.png" alt-text="Capture d’écran montrant la création d’un modèle d’application.":::

:::image type="content" source="media/howto-create-iot-central-application/create-template-2.png" alt-text="Capture d’écran montrant l’exportation d’un modèle d’application.":::

### <a name="use-an-application-template"></a>Utiliser un modèle d’application

Pour utiliser un modèle d’application afin de créer une application IoT Central, vous devez d’abord avoir créé un **Lien de partage**. Collez le **Lien de partage** dans la barre d’adresse de votre navigateur. La page **Créer une application** s’affiche avec les modèles d’application personnalisés sélectionnés :

:::image type="content" source="media/howto-create-iot-central-application/create-app.png" alt-text="Capture d’écran montrant la création d’une application à partir d’un modèle.":::

Sélectionnez votre plan tarifaire et remplissez les autres champs du formulaire. Sélectionnez ensuite **Créer** pour créer une application IoT Central à partir d’un modèle d’application.

### <a name="manage-application-templates"></a>Gérer des modèles d’application

Sur la page **Exportation de modèle d’application**, vous pouvez supprimer ou mettre à jour le modèle d’application.

Si vous supprimez un modèle d’application, vous ne pouvez plus utiliser le lien de partage généré précédemment pour créer des applications.

Pour mettre à jour votre modèle d’application, modifiez le nom du modèle ou sa description sur la page **Exportation de modèle d’application**. Sélectionnez ensuite le bouton **Exporter**. Cette action génère un nouveau **Lien de partage** et rend non valide les **Liens de partage** précédents.

## <a name="other-approaches"></a>Autres approches

Vous pouvez également utiliser les approches suivantes pour créer une application IoT Central :

- [Créer une application IoT Central à partir du portail Azure](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [Créer une application IoT Central à l’aide de la ligne de commande](howto-manage-iot-central-from-cli.md#create-an-application)
- [Créer une application IoT Central par programmation](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans Azure CLI, nous vous suggérons l’étape suivante :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)
