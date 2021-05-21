---
title: Sélectionner le package de mise à jour le mieux adapté à votre Azure Percept DK
description: Comment identifier votre version d’Azure Percept DK et sélectionner le package de mise à jour le mieux adapté
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 05/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 6f32e785af7f302124a11613a40e286fe3b641b9
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109488048"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>Comment déterminer et télécharger le package de mise à jour le mieux adapté pour les mises à jour OTA et USB

Cette page fournit des conseils sur la façon de sélectionner le package de mise à jour le mieux adapté à votre kit de développement et les emplacements de téléchargement des packages de mise à jour.

Pour plus d’informations sur la mise à jour de votre appareil, consultez les articles suivants :
- [Mettre à jour le DK Azure Percept en OTA](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air)
- [Mettre à jour le DK Azure Percept via USB](https://docs.microsoft.com/azure/azure-percept/how-to-update-via-usb)


## <a name="prerequisites"></a>Prérequis

- Une instance [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270) qui a été [configurée et connectée à Azure Percept Studio et IoT Hub](https://docs.microsoft.com/azure/azure-percept/quickstart-percept-dk-set-up).

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>Identifiez le nom du modèle et la version logicielle de votre kit de développement
Pour vous assurer que vous appliquez le package de mise à jour approprié à votre kit de développement, vous devez d’abord déterminer la version du logiciel en cours d’exécution.

> [!WARNING]
> L’application d’un package de mise à jour incorrect peut entraîner l’incompatibilité de votre kit de développement. Il est important de suivre ces étapes pour vous assurer d’appliquer le package de mise à jour approprié.

1. Mettez sous tension votre kit de développement et assurez-vous qu’il est connecté à Azure Percept Studio.
1. Dans Azure Percept Studio, sélectionnez **Appareils** dans le menu de gauche.
1. Dans la liste des appareils, sélectionnez le nom de l’appareil actuellement connecté. L’état indique **Connecté**.
1. Sélectionnez **Ouvrir l’appareil dans IoT Hub**
1. Vous serez peut-être amené à vous connecter à nouveau à votre compte Azure.
1. Sélectionnez **Jumeau d’appareil**.
1. Faites défiler les propriétés de représentation d’appareil et recherchez **model** et **swVersion** sous **deviceInformation**, et notez leurs valeurs.

## <a name="determine-the-correct-update-package"></a>Déterminer le package de mise à jour correct
À l’aide des valeurs **model** et **swVersion** identifiées dans la section précédente, consultez le tableau ci-dessous pour déterminer le package de mise à jour à télécharger.


|model  |swVersion  |Mettre à jour la méthode  |Liens de téléchargement  |
|---------|---------|---------|---------|
|PE-101     |2020.108.101.105, <br>2020.108.114.120, <br>2020.109.101.122, <br>2020.109.116.120, <br>2021.101.106.118        |**USB uniquement**         |[Package de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2155734)         |
|PE-101     |2021.102.108.112, <br>         |OTA ou USB        |[Manifeste OTA](https://go.microsoft.com/fwlink/?linkid=2155625)<br>[Package de mise à jour OTA](https://go.microsoft.com/fwlink/?linkid=2161538)<br>[Package de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2155734)          |
|APDK-101     |Tous les swVersions        |OTA ou USB       | [Manifeste OTA](https://go.microsoft.com/fwlink/?linkid=2162292)<br>[Package de mise à jour OTA](https://go.microsoft.com/fwlink/?linkid=2161538)<br>[Package de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2155734)        |



## <a name="next-steps"></a>Étapes suivantes
Mettez à jour vos kits de développement via les méthodes et packages de mise à jour définis dans la section précédente.
- [Mettre à jour le DK Azure Percept en OTA](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air)
- [Mettre à jour le DK Azure Percept via USB](https://docs.microsoft.com/azure/azure-percept/how-to-update-via-usb)

