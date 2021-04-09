---
title: Comprendre les groupes d’appareils Device Update pour Azure IoT Hub | Microsoft Docs
description: Comprenez comment les groupes d’appareils sont utilisés.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101678355"
---
# <a name="device-groups"></a>Groupes d’appareils

Un groupe d’appareils est une collection d’appareils. Les groupes d’appareils permettent de mettre à l’échelle des déploiements sur de nombreux appareils. Chaque appareil appartient à un seul groupe d’appareils à la fois.
Vous pouvez choisir de créer plusieurs groupes d’appareils pour organiser vos appareils. Par exemple, Contoso peut utiliser le groupe d’appareils « Distribution de version d’évaluation » pour les appareils de son laboratoire de test et le groupe d’appareils « Évaluation » pour les appareils que son équipe de terrain utilise dans le centre d’opérations. De plus, Contoso peut choisir de regrouper ses appareils de production selon leurs régions géographiques, afin de pouvoir les mettre à jour selon une planification en adéquation avec les fuseaux horaires de ces régions. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Utilisation d’une étiquette de jumeau d’appareil pour la création d’un groupe d’appareils

Les étiquettes de jumeau d’appareil permettent aux utilisateurs de regrouper des appareils. Les appareils ont besoin d’une clé ADUGroup et d’une valeur dans leur jumeau d’appareil afin de pouvoir être regroupés.

### <a name="device-twin-tag-format"></a>Format d’étiquette de jumeau d’appareil

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Groupe d’appareils sans catégorie

La désignation « Sans catégorie » est réservée au regroupement des appareils qui :
- N’ont pas d’étiquette de jumeau d’appareil ADUGroup.
- Ont une étiquette de jumeau d’appareil ADUGroup, mais aucun groupe créé ne porte ce nom de groupe.

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
