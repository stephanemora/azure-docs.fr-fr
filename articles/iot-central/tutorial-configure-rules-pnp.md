---
title: Configurer des règles et des actions dans Azure IoT Central
description: Ce didacticiel vous montre, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878830"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Didacticiel : Configurer des règles et des actions pour votre appareil dans Azure IoT Central (fonctionnalités d’évaluation)

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Dans ce tutoriel, vous allez créer une règle qui envoie un e-mail quand la température d’un capteur environnemental dépasse 90&deg; F.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer le tutoriel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) pour créer le modèle d’appareil **Capteur environnemental** à utiliser.

## <a name="create-a-telemetry-based-rule"></a>Créer une règle basée sur la télémétrie

1. Pour ajouter une nouvelle règle basée sur la télémétrie à votre application, dans le menu de navigation de gauche, sélectionnez **Règles**.

1. Pour créer une nouvelle règle, sélectionnez **+ Nouveau**. Choisissez ensuite **Télémétrie**.

1. Entrez **Température ambiante** comme nom de la règle.

1. Dans la section **Étendue**, sélectionnez le **Capteur environnemental** comme modèle d’appareil. Étendue d’appareils à laquelle s’applique cette règle. Vous pouvez ajouter d’autres critères de filtre à l’aide de **+ Filtre**.

1. Dans la section **Condition**, vous définissez ce qui déclenche votre règle. Utilisez les informations suivantes pour définir une condition basée sur la télémétrie de température :

    | Champ                                        | Valeur                             |
    | -------------------------------------------- | ------------------------------    |
    | Mesure                                  | Température                       |
    | Operator                                     | est supérieur à                   |
    | Valeur                                        | 90                                |

    Pour ajouter d’autres conditions, sélectionnez **+ Condition**.

    ![Créer une condition de règle](./media/tutorial-configure-rules-pnp/condition.png)

1. Pour ajouter une action à exécuter quand la règle se déclenche, sélectionnez **+ Action**, puis **E-mail**.

1. Utilisez les informations du tableau suivant pour définir votre action :

    | Paramètre   | Valeur                                             |
    | --------- | ------------------------------------------------- |
    | À        | Votre adresse e-mail                                |
    | Notes     | La température ambiante a dépassé le seuil. |

    > [!NOTE]
    > Pour recevoir une notification par courrier électronique, l’adresse e-mail doit être un [ID d’utilisateur dans l’application](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), et l’utilisateur doit s’être connecté au moins une fois à l’application.

    ![Créer une action de règle](./media/tutorial-configure-rules-pnp/action.png)

1. Sélectionnez **Enregistrer**. Votre règle est listée dans la page **Règles**.

## <a name="test-the-rule"></a>Tester la règle

Peu après l’enregistrement de la règle, elle devient active. Lorsque les conditions définies dans la règle sont remplies, votre application envoie un message à l’adresse e-mail que vous avez spécifiée dans l’action.

> [!NOTE]
> Une fois le test terminé, désactivez la règle pour ne plus recevoir d’alertes dans votre boîte de réception.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Créer une règle basée sur la télémétrie
* Ajouter une action

Maintenant que vous avez défini une règle basée sur un seuil, nous vous suggérons de passer à l’étape suivante :

> [!div class="nextstepaction"]
> [Créer une règle d’événements et configurer des notifications](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
