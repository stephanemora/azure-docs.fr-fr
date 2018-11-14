---
title: Présentation de la gestion des versions des modèles d’appareil pour vos applications Azure IoT Central | Microsoft Docs
description: Itérez au sein de vos modèles d’appareil en créant une nouvelle version sans impacter vos appareils actuellement connectés
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3b9e6a59b44db9295d86e3bc8a8dda9ec9761f38
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009206"
---
# <a name="create-a-new-device-template-version"></a>Créer une nouvelle version de modèle d’appareil

Avec Azure IoT Central, vous pouvez développer rapidement des applications IoT. Vous pouvez effectuer une itération rapide au sein des modèles d’appareil que vous avez conçus en ajoutant, modifiant ou supprimant des mesures, des paramètres ou des propriétés. Certains de ces changements peuvent s’avérer intrusifs pour les appareils actuellement connectés. Azure IoT Central identifie ces changements cassants et offre un moyen de déployer de façon sécurisée ces mises à jour sur les appareils.

Chaque modèle d’appareil que vous créez se voit attribuer un numéro de version. Par défaut, il s’agit du numéro de version 1.0.0. Si vous modifiez un modèle d’appareil et que ce changement peut impacter les appareils actuellement connectés, Azure IoT Central vous invite à créer une nouvelle version du modèle d’appareil.

> [!NOTE]
> Pour en savoir plus sur la création d’un modèle d’appareil, consultez [Configurer un modèle d’appareil](howto-set-up-template.md).

## <a name="changes-that-prompt-a-version-change"></a>Changements qui appellent un changement de version

En règle générale, les changements apportés aux paramètres ou aux propriétés de votre modèle d’appareil appellent un changement de version.

> [!NOTE]
> Les changements apportés au modèle d’appareil n’appellent pas la création d’une nouvelle version dans la mesure où un seul appareil est connecté (ou aucun).

La liste suivante décrit les actions utilisateur qui peuvent exiger une nouvelle version :

* Propriétés (obligatoires)
    * Ajout ou suppression d’une propriété obligatoire
    * Changement du nom de champ d’une propriété, nom de champ utilisé par vos appareils pour l’envoi de messages
*  Propriétés (facultatives)
    * Suppression d’une propriété facultative
    * Changement du nom de champ d’une propriété, nom de champ utilisé par vos appareils pour l’envoi de messages
    * Remplacement d’une propriété facultative par une propriété obligatoire
*  Paramètres
    * Ajout ou suppression d’un paramètre
    * Changement du nom de champ d’un paramètre, nom de champ utilisé par vos appareils pour l’envoi et la réception de messages

## <a name="what-happens-on-version-change"></a>Que se passe-t-il à l’occasion d’un changement de version ?

Que se passe-t-il au niveau des règles et des tableaux de bord d’appareil en cas de changement de version ?

Les **règles** peuvent contenir des conditions dépendantes de propriétés. Si vous avez supprimé une ou plusieurs de ces propriétés, ces règles peuvent être séparées dans votre nouvelle version de modèle d’appareil. Vous pouvez accéder à ces règles spécifiques et mettre à jour les conditions pour corriger les règles. Les règles de la version précédente doivent fonctionner sans aucun impact.

Les **tableaux de bord d’appareil** peuvent contenir plusieurs types de vignette. Certaines vignettes peuvent contenir des paramètres et des propriétés. Quand une propriété ou un paramètre utilisé dans une vignette est supprimé, la vignette est entièrement ou partiellement endommagée. Vous pouvez accéder à la vignette et corriger le problème en supprimant la vignette ou en mettant à jour son contenu.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrer un appareil d’une version de modèle d’appareil vers une autre

Vous pouvez créer plusieurs versions d’un modèle d’appareil. Au fil du temps, vous aurez plusieurs appareils connectés utilisant ces modèles d’appareil. Vous pouvez migrer les appareils d’une version de votre modèle d’appareil vers une autre. Les étapes suivantes décrivent le processus de migration d’un appareil :

1. Accédez à la page **Explorateur**.
1. Sélectionnez l’appareil que vous devez migrer vers une autre version.
1. Choisissez **Migrer l’appareil**.
1. Sélectionnez le numéro de version vers lequel vous souhaitez migrer l’appareil, puis choisissez **Migrer**.

![Comment migrer un appareil](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser les versions de modèle d’appareil dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](howto-create-telemetry-rules.md)