---
title: Sélectionner le package de mise à jour le mieux adapté à votre Azure Percept DK
description: Comment identifier votre version d’Azure Percept DK et sélectionner le package de mise à jour le mieux adapté
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 05/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 7528e11e24485fecc3e83773d98e119d98ca63cf
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968750"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>Comment déterminer et télécharger le package de mise à jour le mieux adapté pour les mises à jour OTA et USB

Cette page fournit des conseils sur la façon de sélectionner le package de mise à jour le mieux adapté à votre kit de développement et les emplacements de téléchargement des packages de mise à jour.

Pour plus d’informations sur la mise à jour de votre appareil, consultez les articles suivants :
- [Mettre à jour le DK Azure Percept en OTA](./how-to-update-over-the-air.md)
- [Mettre à jour le DK Azure Percept via USB](./how-to-update-via-usb.md)


## <a name="prerequisites"></a>Prérequis

- Une instance [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270) qui a été [configurée et connectée à Azure Percept Studio et IoT Hub](./quickstart-percept-dk-set-up.md).

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>Identifiez le nom du modèle et la version logicielle de votre kit de développement
Pour vous assurer que vous appliquez le package de mise à jour approprié à votre kit de développement, vous devez d’abord déterminer la version du logiciel en cours d’exécution.

> [!WARNING]
> L’application d’un package de mise à jour incorrect peut entraîner l’incompatibilité de votre kit de développement. Il est important de suivre ces étapes pour vous assurer d’appliquer le package de mise à jour approprié.

Option 1 :
1. Connectez-vous à [Azure Percept Studio](./overview-azure-percept-studio.md).
2. Dans **Appareils**, choisissez votre appareil DevKit.
3. Dans l’onglet **Général**, recherchez les informations relatives au **modèle** et à la **version du logiciel**.

Option n°2 :
1. Affichez l’**appareil IoT Edge** du service **IoT Hub** à partir du portail Microsoft Azure.
2. Choisissez votre appareil DevKit dans la liste des appareils.
3. Sélectionnez **Jumeau d’appareil**.
4. Faites défiler les propriétés de représentation d’appareil et recherchez **model** et **swVersion** sous **deviceInformation**, et notez leurs valeurs.

## <a name="determine-the-correct-update-package"></a>Déterminer le package de mise à jour correct
À l’aide des valeurs **model** et **swVersion** identifiées dans la section précédente, consultez le tableau ci-dessous pour déterminer le package de mise à jour à télécharger.


|model  |swVersion  |Mettre à jour la méthode  |Liens de téléchargement  |Notes  |
|---------|---------|---------|---------|---------|
|PE-101     |2020.108.101.105, <br>2020.108.114.120, <br>2020.109.101.122, <br>2020.109.116.120, <br>2021.101.106.118        |**USB uniquement**         |[2021.105.111.112 Package de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2155734)         |Version de mai (2105)         |
|PE-101     |2021.102.108.112, <br>         |OTA ou USB        |[2021.105.111.112 Manifeste OTA (PE-101)](https://go.microsoft.com/fwlink/?linkid=2155625)<br>[2021.105.111.112 Package de mise à jour OTA](https://go.microsoft.com/fwlink/?linkid=2161538)<br>[2021.105.111.112 Package de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2155734)          |Version de mai (2105)         |
|APDK-101     |Tous les swVersions        |OTA ou USB       | [2021.105.111.112 Manifeste OTA (APDK-101)](https://go.microsoft.com/fwlink/?linkid=2163554)<br>[2021.105.111.112 Package de mise à jour OTA](https://go.microsoft.com/fwlink/?linkid=2163456)<br>[2021.105.111.112 Package de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2163555)        |Version de mai (2105)         |


## <a name="next-steps"></a>Étapes suivantes
Mettez à jour vos kits de développement via les méthodes et packages de mise à jour définis dans la section précédente.
- [Mettre à jour le DK Azure Percept en OTA](./how-to-update-over-the-air.md)
- [Mettre à jour le DK Azure Percept via USB](./how-to-update-via-usb.md)