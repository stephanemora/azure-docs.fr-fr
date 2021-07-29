---
title: Créer un groupe d’appareils dans Device Update pour Azure IoT Hub | Microsoft Docs
description: Créer un groupe d’appareils dans Device Update pour Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 6c9f1294f1d2f80689cdb417ad16357cc5fbcece
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089323"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Créer des groupes d’appareils dans Device Update pour IoT Hub
Le service Device Update pour IoT Hub permet de déployer une mise à jour sur un groupe d’appareils IoT.

## <a name="prerequisites"></a>Prérequis

* [Accès à un hub IoT avec Device Update pour IoT Hub activé](create-device-update-account.md). Il est recommandé d’utiliser un niveau S1 (Standard) ou supérieur pour votre hub IoT. 
* Un appareil IoT (ou simulateur) provisionné pour Device Update dans IoT Hub.
* [Au moins une mise à jour a été correctement importée pour l’appareil provisionné.](import-update.md)
* Installer et démarrer l’agent Device Update sur votre appareil IoT en tant [qu’identité au niveau du module ou de l’appareil](device-update-agent-provisioning.md)

## <a name="add-a-tag-to-your-devices"></a>Ajouter une étiquette à vos appareils  

Le service Device Update pour IoT Hub permet de déployer une mise à jour sur un groupe d’appareils IoT. Pour créer un groupe, la première étape consiste à ajouter une étiquette au groupe cible d’appareils dans IoT Hub. Les étiquettes peuvent être ajoutées à votre appareil uniquement si celui-ci est déjà connecté à Device Update.

La documentation ci-dessous explique comment ajouter et mettre à jour une étiquette.

### <a name="programmatically-update-device-twin"></a>Mettre à jour le jumeau d’appareil programmatiquement

Vous pouvez mettre à jour le jumeau d’appareil avec l’étiquette appropriée en utilisant RegistryManager après avoir inscrit l’appareil auprès du service Device Update. 
[Découvrez comment ajouter des étiquettes à l’aide d’un exemple d’application .NET.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Découvrez les propriétés d’étiquette.](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format)

#### <a name="device-update-tag-format"></a>Format des étiquettes Device Update

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Utilisation de travaux

Il est possible de planifier un travail sur plusieurs appareils pour ajouter ou mettre à jour une étiquette Device Update. Pour cela, aidez-vous des exemples fournis [ici](../iot-hub/iot-hub-devguide-jobs.md). Vous pouvez mettre à jour Jumeau d’appareil ou Jumeau de module (si l’agent Device Update est configuré en tant qu’identité de module) à l’aide de Travaux. [En savoir plus](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Cette action ne respecte pas votre quota actuel de messages IoT Hub. Il est recommandé de ne pas modifier plus de 50 000 étiquettes de jumeau d’appareil ou de module à la fois ; en effet, si vous dépassez votre quota quotidien de messages IoT Hub, vous devrez acheter des unités IoT Hub supplémentaires. Pour plus d’informations, consultez [Quotas et limitation](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Mises à jour directes des jumeaux

Les étiquettes peuvent également être ajoutées ou mises à jour directement dans Jumeau d’appareil ou Jumeau de module.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT.

2. Sous « Appareils IoT » ou « IoT Edge » dans le volet de navigation gauche, recherchez votre appareil IoT, accédez à Jumeau d’appareil, ou au module Device Update, puis à Jumeau de module (cette option est disponible si l’agent Device Update est configuré comme une identité de module).

3. Dans Jumeau d’appareil ou Jumeau de module, supprimez toute valeur d’étiquette Device Update existante en lui affectant la valeur null.

4. Ajoutez une nouvelle valeur d’étiquette Device Update comme indiqué ci-dessous. [Exemple de document JSON de jumeau d’appareil avec étiquettes.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Limites

* Vous pouvez ajouter n’importe quelle valeur à votre étiquette, sauf « Sans catégorie », qui est une valeur réservée.
* Le nom de l’étiquette ne peut pas dépasser 255 caractères.
* La valeur de l’étiquette peut contenir des caractères alphanumériques et les caractères spéciaux suivants : « . », « - », « _ », « ~ ».
* Les noms des groupes et des étiquettes respectent la casse.
* Un appareil ne peut avoir qu’une seule étiquette appelée ADUGroup ; tout ajout ultérieur d’une étiquette du même nom remplacerait la valeur existante de l’étiquette ADUGroup.
* Un appareil ne peut appartenir qu’à un seul groupe.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Créer un groupe d’appareils en sélectionnant une étiquette IoT Hub existante

1. Accédez au [portail Azure](https://portal.azure.com).

2. Sélectionnez le hub IoT que vous avez précédemment connecté à votre instance Device Update.

3. Sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.

4. Sélectionnez l’onglet Groupes en haut de la page. Vous voyez maintenant le nombre d’appareils connectés à Device Update qui ne sont pas encore groupés.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Capture d’écran des appareils non groupés." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Sélectionnez le bouton Ajouter pour créer un groupe.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Capture d’écran de l’ajout d’un groupe d’appareils." lightbox="media/create-update-group/add-group.png":::

6. Sélectionnez une étiquette IoT Hub dans la liste, puis sélectionnez Créer un groupe de mises à jour.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Capture d’écran de la sélection de l’étiquette." lightbox="media/create-update-group/select-tag.png":::

7. Une fois le groupe créé, vous voyez que la liste des groupes et le graphique de conformité des mises à jour ont été mis à jour.  Le graphique de conformité des mises à jour montre le nombre d’appareils dans différents états de conformité : A la mise à jour la plus récente, Nouvelles mises à jour disponibles, Mises à jour en cours et Appareils pas encore groupés. [En savoir plus sur la conformité des mises à jour.](device-update-compliance.md)
   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Capture d’écran de la vue de la conformité des mises à jour." lightbox="media/create-update-group/updated-view.png":::

8. Vous voyez maintenant le groupe que vous venez de créer ainsi que toutes les mises à jour disponibles pour les appareils dans le nouveau groupe. Vous pouvez déployer la mise à jour sur le nouveau groupe à partir de cette vue en cliquant sur le nom de la mise à jour. Pour plus d’informations, consultez Étape suivante : Déployer la mise à jour.

## <a name="view-device-details-for-the-group-you-created"></a>Voir les détails des appareils dans le nouveau groupe

1. Accédez au groupe que vous venez de créer, puis cliquez sur le nom du groupe.

2. Vous voyez alors la liste des appareils membres du groupe ainsi que les propriétés de mise à jour de chaque appareil. Cette vue présente également des informations sur la conformité des mises à jour pour tous les appareils qui sont membres du groupe. Le graphique de conformité des mises à jour montre le nombre d’appareils dans différents états de conformité : A la mise à jour la plus récente, Nouvelles mises à jour disponibles et Mises à jour en cours.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Capture d’écran de la vue des détails du groupe d’appareils." lightbox="media/create-update-group/group-details.png":::

3. Vous pouvez aussi cliquer sur chaque appareil dans un groupe pour accéder à la page de détails de l’appareil dans IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Capture d’écran de la vue des détails de l’appareil." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Étapes suivantes 

[Déployer la mise à jour](deploy-update.md)

[En savoir plus sur les groupes d’appareils](device-update-groups.md)

[En savoir plus sur la conformité des mises à jour](device-update-compliance.md)
