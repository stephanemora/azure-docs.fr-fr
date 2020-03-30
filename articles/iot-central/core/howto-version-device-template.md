---
title: Présentation de la gestion des versions des modèles d’appareil pour vos applications Azure IoT Central | Microsoft Docs
description: Itérez au sein de vos modèles d’appareil en créant une nouvelle version sans impacter vos appareils actuellement connectés
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bb77d1a09cb2692765c4c834ce617d13465d4d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157566"
---
# <a name="create-a-new-device-template-version"></a>Créer une nouvelle version de modèle d’appareil



Avec Azure IoT Central, vous pouvez développer rapidement des applications IoT. Vous pouvez effectuer une itération rapide au sein des modèles d’appareil que vous avez conçus en ajoutant, en modifiant ou en supprimant des capacités d’appareil, des vues et des personnalisations. Une fois que vous avez publié votre modèle d’appareil, le modèle de capacité de l’appareil s’affiche comme étant **Publié** avec des icônes de verrouillage en regard du modèle. Pour apporter des modifications au modèle de capacité de l’appareil, vous devez créer une nouvelle version du modèle d’appareil. Entre-temps, les propriétés, les personnalisations et les vue du cloud peuvent être modifiés à tout moment sans avoir besoin d’une version du modèle d’appareil. Une fois que vous avez enregistré certaines de ces modifications, vous pouvez publier le modèle d’appareil pour mettre les dernières modifications à la disposition de l’opérateur, qui pourra les afficher dans Device Explorer.

> [!NOTE]
> Pour en savoir plus sur la création d’un modèle d’appareil, consultez [Configurer et gérer un modèle d’appareil](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Ajouter des personnalisations au modèle d’appareil sans contrôle de version

Certains éléments de vos capacités d’appareil peuvent être modifiés sans avoir à contrôler la version de votre modèle d’appareil et de vos interfaces. Par exemple, certains de ces champs incluent le nom d'affichage, le type sémantique, la valeur minimale, la valeur maximale, les décimales, la couleur, l’unité, l’unité d’affichage, le commentaire et la description. Pour ajouter l’une de ces personnalisations :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil que vous souhaitez personnaliser.
1. Choisissez l’onglet **Personnaliser**.
1. Toutes les fonctionnalités définies dans votre modèle de capacité d’appareil sont répertoriées ici. Tous les champs que vous pouvez modifier ici peuvent être enregistrés et utilisés dans votre application, sans que vous ayez besoin de contrôler la version de votre modèle d’appareil. Si des champs que vous souhaitez modifier sont en lecture seule, vous devez contrôler la version de votre modèle d’appareil pour pouvoir le faire. Sélectionnez un champ que vous souhaitez modifier et entrez les nouvelles valeurs.
1. Cliquez sur **Enregistrer**. À présent, ces valeurs remplacent celles qui ont été initialement enregistrées dans votre modèle d’appareil et qui seront utilisées dans l’application.

## <a name="versioning-a-device-template"></a>Contrôle de version d’un modèle d’appareil

La création d’une nouvelle version de votre modèle d’appareil entraîne la création d’une version brouillon du modèle dans lequel le modèle de capacité de l’appareil peut être modifié. Toutes les interfaces publiées restent publiées jusqu’à ce qu’elles aient une version individuelle. Pour pouvoir modifier une interface publiée, vous devez d’abord créer une version de modèle d’appareil.

La version du modèle d’appareil ne doit être contrôlée que lorsque vous essayez de modifier une partie du modèle de capacité de l’appareil que vous ne pouvez pas modifier dans la section Personnalisations du modèle d’appareil. 

Pour obtenir la version d’un modèle d’appareil :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil dont vous voulez contrôler la version.
1. Cliquez sur le bouton **Version** en haut de la page et donnez un nouveau nom au modèle. Nous vous avons suggéré un nouveau nom qui peut être modifié.
1. Cliquez sur **Créer**.
1. Votre modèle d’appareil est maintenant en mode brouillon. Vous verrez que vos interfaces sont toujours verrouillées et que leur version doit être contrôlée individuellement pour qu’elles soient modifiées. 

### <a name="versioning-an-interface"></a>Contrôle de version d’une interface

Le contrôle de version d’une interface vous permet d’ajouter, de mettre à jour et de supprimer les fonctionnalités à l’intérieur de l’interface que vous avez déjà créée. 

Pour contrôler la version d’une interface :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil en mode brouillon.
1. Sélectionnez l’interface en mode publié dont vous souhaitez contrôler la version et que vous voulez modifier.
1. Cliquez sur le bouton **Version** en haut de la page de l’interface. 
1. Cliquez sur **Créer**.
1. Votre interface est maintenant en mode brouillon. Vous pouvez ajouter ou modifier des fonctionnalités dans votre interface sans arrêter les personnalisations et les vues existantes. 

> [!NOTE]
> Les interfaces standard publiées par Azure IoT ne peuvent pas faire l’objet d’un contrôle de version ni être modifiées. Ces interfaces standard sont utilisées pour la certification des appareils.

> [!NOTE]
> Une fois l’interface publiée, vous ne pouvez supprimer aucune de ses fonctionnalités, même en mode brouillon. Les fonctionnalités ne peuvent être modifiées ou ajoutées à l’interface qu’en mode brouillon.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrer un appareil d’une version de modèle d’appareil vers une autre

Vous pouvez créer plusieurs versions d’un modèle d’appareil. Au fil du temps, vous aurez plusieurs appareils connectés utilisant ces modèles d’appareil. Vous pouvez migrer les appareils d’une version de votre modèle d’appareil vers une autre. Les étapes suivantes décrivent le processus de migration d’un appareil :

1. Accédez à la page **Explorateur d’appareils**.
1. Sélectionnez l’appareil que vous devez migrer vers une autre version.
1. Choisissez **Migrer**.
1. Sélectionnez le modèle d’appareil avec le numéro de version vers lequel vous souhaitez migrer l’appareil, puis choisissez **Migrer**.

![Comment migrer un appareil](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser les versions de modèle d’appareil dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](tutorial-create-telemetry-rules.md)
