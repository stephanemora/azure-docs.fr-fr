---
title: Présentation de la gestion des versions des modèles d’appareil pour vos applications Azure IoT Central | Microsoft Docs
description: Itérez au sein de vos modèles d’appareil en créant une nouvelle version sans impacter vos appareils actuellement connectés
author: philmea
ms.author: philmea
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: 3c13c0b8cb118df877642328fa1b5512be31cffa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014424"
---
# <a name="create-a-new-device-template-version"></a>Créer une nouvelle version de modèle d’appareil

*Cet article s’applique aux créateurs de solutions et aux développeurs d’appareils.*

Un modèle d’appareil comprend un schéma décrivant comment un appareil interagit avec IoT Central. Ces interactions incluent une télémétrie, des propriétés et des commandes. L’appareil et l’application de IoT Central s’appuient sur une compréhension partagée de ce schéma pour échanger des informations. Vous ne pouvez apporter que des modifications limitées au schéma sans rompre le contrat. C’est pourquoi la plupart des changements de schéma nécessitent une nouvelle version du modèle d’appareil. Le contrôle de version du modèle d’appareil permet aux appareils plus anciens de continuer à utiliser la version de schéma qu’ils comprennent, tandis que les appareils plus récents ou mis à jour utilisent une version de schéma ultérieure.

Le schéma d’un modèle d’appareil est défini dans le modèle de capacité d’appareil et ses interfaces. Les modèles d’appareils incluent d’autres informations, telles que des propriétés de cloud, des personnalisations d’affichage et des vues. Si vous apportez des modifications à ces parties du modèle d’appareil qui ne définissent pas la manière dont l’appareil échange des données avec IoT Central, vous n’avez pas besoin de contrôler la version du modèle.

Vous devez publier toutes les modifications apportées au modèle d’appareil, qu’elles nécessitent ou non une mise à jour de version, avant qu’un opérateur puisse l’utiliser. IoT Central vous empêche de publier les modifications importantes apportées à un modèle d’appareil sans contrôle de version préalable du modèle.

> [!NOTE]
> Pour en savoir plus sur la création d’un modèle d’appareil, consultez [Configurer et gérer un modèle d’appareil](howto-set-up-template.md)

## <a name="versioning-rules"></a>Règles de contrôle de version

Cette section résume les règles de contrôle de version qui s’appliquent aux modèles d’appareils. Les modèles de capacité d’appareil et les interfaces ont des numéros de version. L’extrait de code suivant montre le modèle de capacité d’appareil pour un dispositif de capteur environnemental. Le modèle de capacité d’appareil a deux interfaces : **DeviceInformation** et **EnvironmentalSensor**. Vous pouvez voir les numéros de version à la fin des champs `@id`. Pour afficher ces informations dans l’interface utilisateur d’IoT Central, dans l’éditeur de modèle d’appareil, sélectionnez **Afficher l’identité**.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* Après qu’un modèle de capacité d’appareil a été publié, vous ne pouvez pas supprimer d’interfaces, même dans une nouvelle version du modèle d’appareil.
* Après qu’un modèle de capacité d’appareil a été publié, vous pouvez ajouter une interface en créant une nouvelle version du modèle d’appareil.
* Après qu’un modèle de capacité d’appareil a été publié, vous pouvez remplacer une interface par une version plus récente en créant une nouvelle version du modèle d’appareil. Par exemple, si le modèle d’appareil capteur v1 utilise l’interface EnvironmentalSensor v1, vous pouvez créer un modèle d’appareil capteur v2 qui utilise l’interface EnvironmentalSensor v2.
* Après la publication d’une interface, vous ne pouvez pas supprimer le contenu de celle-ci, même dans une nouvelle version du modèle d’appareil.
* Après la publication d’une interface, vous pouvez ajouter des éléments au contenu de celle-ci en créant une nouvelle version de l’interface et du modèle d’appareil. Les éléments que vous pouvez ajouter à l’interface incluent une télémétrie, des propriétés et des commandes.
* Après la publication d’une interface, vous pouvez apporter des modifications hors schéma à des éléments existants de l’interface en créant une nouvelle version de l’interface et du modèle d’appareil. Les parties hors schéma d’un élément d’interface incluent le nom d’affichage et le type sémantique. Les parties schéma d’un élément d’interface que vous ne pouvez pas modifier sont le nom, le type de fonctionnalité et le schéma.

Les sections suivantes vous guident à travers des exemples de modification de modèles d’appareils dans IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personnaliser le modèle d’appareil sans contrôle de version

Certains éléments de vos capacités d’appareil peuvent être modifiés sans avoir à contrôler la version de votre modèle d’appareil et de vos interfaces. Par exemple, certains de ces champs incluent le nom d'affichage, le type sémantique, la valeur minimale, la valeur maximale, les décimales, la couleur, l’unité, l’unité d’affichage, le commentaire et la description. Pour ajouter l’une de ces personnalisations :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil que vous souhaitez personnaliser.
1. Choisissez l’onglet **Personnaliser**.
1. Toutes les fonctionnalités définies dans votre modèle de capacité d’appareil sont répertoriées ici. Vous pouvez modifier, enregistrer et utiliser tous ces champs sans devoir contrôler la version de votre modèle d’appareil. Si des champs que vous souhaitez modifier sont en lecture seule, vous devez contrôler la version de votre modèle d’appareil pour pouvoir le faire. Sélectionnez un champ que vous souhaitez modifier et entrez les nouvelles valeurs.
1. Cliquez sur **Enregistrer**. À présent, ces valeurs remplacent celles qui ont été initialement enregistrées dans votre modèle d’appareil, et sont utilisées dans l’application.

## <a name="version-a-device-template"></a>Contrôler la version d’un modèle d’appareil

La création d’une nouvelle version de votre modèle d’appareil a pour effet de créer une version provisoire du modèle dans laquelle le modèle de capacité d’appareil peut être modifié. Toutes les interfaces publiées le restent jusqu’à ce que leur version soit contrôlée. Pour modifier une interface publiée, commencez par créer une nouvelle version du modèle d’appareil.

Ne contrôlez la version du modèle d’appareil que lorsque vous tentez de modifier une partie du modèle de capacité d’appareil que vous ne pouvez pas modifier dans la section des personnalisations.

Pour contrôler la version d’un modèle d’appareil :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil dont vous tentez de contrôler la version.
1. Cliquez sur le bouton **Version** en haut de la page et donnez un nouveau nom au modèle. IoT Central suggère un nouveau nom que vous pouvez modifier.
1. Cliquez sur **Créer**.
1. Votre modèle d’appareil est maintenant en mode brouillon. Vous pouvez voir que vos interfaces sont toujours verrouillées. Contrôles la version toutes les interfaces que vous souhaitez modifier.

## <a name="version-an-interface"></a>Contrôler la version d’une interface

Le contrôle de version d’une interface vous permet d’ajouter, de mettre à jour et de supprimer les fonctionnalités à l’intérieur de l’interface que vous avez déjà créée.

Pour contrôler la version d’une interface :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil en mode brouillon.
1. Sélectionnez l’interface en mode publié dont vous souhaitez contrôler la version et que vous voulez modifier.
1. Cliquez sur le bouton **Version** en haut de la page de l’interface.
1. Cliquez sur **Créer**.
1. Votre interface est maintenant en mode brouillon. Vous pouvez ajouter ou modifier des fonctionnalités dans votre interface sans détruire des personnalisations ou des vues existantes.

## <a name="migrate-a-device-across-versions"></a>Migrer un appareil vers d’autres versions

Vous pouvez créer plusieurs versions d’un modèle d’appareil. Au fil du temps, vous vous retrouvez avec plusieurs appareils connectés utilisant ces modèles d’appareils. Vous pouvez migrer les appareils d’une version de votre modèle d’appareil vers une autre. Les étapes suivantes décrivent le processus de migration d’un appareil :

1. Accédez à la page **Explorateur d’appareils**.
1. Sélectionnez l’appareil que vous devez migrer vers une autre version.
1. Choisissez **Migrer**.
1. Sélectionnez le modèle d’appareil avec le numéro de version vers lequel vous souhaitez migrer l’appareil, puis choisissez **Migrer**.

![Comment migrer un appareil](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes un opérateur ou un créateur de solution, l’étape suivante suggérée consiste à [apprendre à gérer vos appareils](./howto-manage-devices.md).

Si vous êtes un développeur d’appareils, nous vous suggérons de vous renseigner sur les [appareils Azure IoT Edge et Azure IoT Central](./concepts-iot-edge.md).
