---
title: Sélectionner votre package de mise à jour Azure Percept DK
description: Comment identifier votre version d’Azure Percept DK et sélectionner le package de mise à jour le mieux adapté
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 3e18d8a009736e8e597d5accffb654c1b95aec5d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225218"
---
# <a name="select-your-azure-percept-dk-update-package"></a>Sélectionner votre package de mise à jour Azure Percept DK

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
1. Dans **Appareils**, choisissez votre appareil DevKit.
1. Dans l’onglet **Général**, recherchez les informations relatives au **modèle** et à la **version du logiciel**.

Option n°2 :

1. Affichez les **périphériques IoT** du service **IoT Hub** à partir du Portail Microsoft Azure.
1. Choisissez votre appareil DevKit dans la liste des appareils.
1. Sélectionnez **Jumeau d’appareil**.
1. Faites défiler les propriétés de représentation d’appareil et recherchez **model** et **swVersion** sous **deviceInformation**, et notez leurs valeurs.

## <a name="determine-the-correct-update-package"></a>Déterminer le package de mise à jour correct

À l’aide des valeurs **model** et **swVersion** identifiées dans la section précédente, consultez le tableau ci-dessous pour déterminer le package de mise à jour à télécharger.

|model  |swVersion  |Mettre à jour la méthode  |Liens de téléchargement  |Notes  |
|---------|---------|---------|---------|---------|
|PE-101     |Tous les swVersions       |**USB uniquement**         |[Package de mise à jour USB 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)         |Version de juillet (2107)         |
|APDK-101     |Toute swVersion antérieure à 2021.106.111.115 |**USB uniquement**         |[Package de mise à jour USB 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)         |Version de juillet (2107)         |
|APDK-101     |2021.106.111.115        |OTA ou USB       |[Package de mise à jour OTA 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169245)<br>[Package de mise à jour USB 2021.107.129.116](https://go.microsoft.com/fwlink/?linkid=2169086)        |Version de juillet (2107)         |

## <a name="next-steps"></a>Étapes suivantes

Mettez à jour vos kits de développement via les méthodes et packages de mise à jour définis dans la section précédente.

- [Mettre à jour le DK Azure Percept en OTA](./how-to-update-over-the-air.md)
- [Mettre à jour le DK Azure Percept via USB](./how-to-update-via-usb.md)
