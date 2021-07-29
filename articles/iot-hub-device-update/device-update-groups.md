---
title: Comprendre les groupes d’appareils Device Update pour Azure IoT Hub | Microsoft Docs
description: Comprenez comment les groupes d’appareils sont utilisés.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aa3ee7e5b92044c35ac1856309f7265ad06923a1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083527"
---
# <a name="device-groups"></a>Groupes d’appareils

Un groupe d’appareils est une collection d’appareils. Les groupes d’appareils permettent de mettre à l’échelle des déploiements sur de nombreux appareils. Chaque appareil appartient à un seul groupe d’appareils à la fois.
Vous pouvez choisir de créer plusieurs groupes d’appareils pour organiser vos appareils. Par exemple, Contoso peut utiliser le groupe d’appareils « Distribution de version d’évaluation » pour les appareils de son laboratoire de test et le groupe d’appareils « Évaluation » pour les appareils que son équipe de terrain utilise dans le centre d’opérations. De plus, Contoso peut choisir de regrouper ses appareils de production selon leurs régions géographiques, afin de pouvoir les mettre à jour selon une planification en adéquation avec les fuseaux horaires de ces régions. 


## <a name="using-device-or-module-twin-tag-for-device-group-creation"></a>Utilisation d’une étiquette de jumeau d’appareil ou de module pour la création d’un groupe d’appareils

Les étiquettes permettent aux utilisateurs de regrouper des appareils. Les appareils ont besoin d’une clé ADUGroup et d’une valeur dans leur jumeau d’appareil ou de module afin de pouvoir être regroupés.

### <a name="device-or-module-twin-tag-format"></a>Format d’étiquette de jumeau d’appareil ou de module

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Groupe d’appareils sans catégorie

La désignation « Sans catégorie » est réservée au regroupement des appareils qui :
- N’ont pas d’étiquette de jumeau d’appareil ou de module ADUGroup.
- Ont une étiquette de jumeau d’appareil ou de module ADUGroup, mais aucun groupe créé ne porte ce nom de groupe.

Par exemple, considérez les appareils et leurs étiquettes de jumeau d’appareil ci-dessous :

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Voici les appareils et les groupes pouvant être créés pour eux.

|Appareil |Groupe  |
|-----------|--------------|
|Device1    |Groupe1|
|Appareil2    |Groupe1|
|Device3    |Groupe2|
|Appareil4    |Sans catégorie|



## <a name="next-steps"></a>Étapes suivantes

[Créer un groupe d’appareils](./create-update-group.md)
