---
title: Configurer des règles et des actions dans Azure IoT Central
description: Ce didacticiel vous montre, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 30bfd3b119b3ae4badbda50747158e8b08a9dd08
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875520"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Tutoriel : Configurer des règles et des actions pour votre appareil dans Azure IoT Central

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Dans ce didacticiel, vous créez une règle qui envoie un courrier électronique lorsque la température d’un climatiseur raccordé dépasse 90&deg; F.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer le tutoriel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md) pour créer le modèle d’appareil **Climatiseur raccordé** à utiliser.

## <a name="create-a-telemetry-based-rule"></a>Créer une règle basée sur la télémétrie

1. Pour ajouter une nouvelle règle basée sur la télémétrie pour votre application, dans le menu de navigation de gauche, sélectionnez **Modèles d’appareil** :

    ![Page Modèles d’appareil](media/tutorial-configure-rules/templatespage1.png)

    Vous voyez le modèle d’appareil **Climatiseur connecté (1.0.0)** que vous avez créé dans le tutoriel précédent.

2. Pour personnaliser votre modèle d’appareil, sélectionnez le modèle **Climatiseur connecté** que vous avez créé dans le tutoriel précédent.

3. Pour ajouter une règle basée sur la télémétrie dans la vue **Règles**, sélectionnez **Règles**, sélectionnez **+Nouvelle règle**, puis sélectionnez **Télémétrie** :

    ![Vue Règles](media/tutorial-configure-rules/newrule.png)

5. Pour définir votre règle, utilisez les informations du tableau suivant :

    | Paramètre                                      | Valeur                             |
    | -------------------------------------------- | ------------------------------    |
    | Nom                                         | Alerte de température du climatiseur |
    | Activer la règle pour tous les appareils de ce modèle | Il en va                                |
    | Condition                                    | Température supérieure à 90    |
    | Agrégation                                  | Aucun                              |

    ![Condition de règle de température](media/tutorial-configure-rules/temperaturerule.png)

    Ensuite, sélectionnez **Enregistrer**.

## <a name="add-an-action"></a>Ajouter une action

Lorsque vous définissez une règle, vous définissez également une action à exécuter lorsque les conditions de la règle sont remplies. Dans ce tutoriel, vous créez une règle avec une action qui envoie une notification par e-mail.

1. Pour ajouter une **action**, **enregistrez** d’abord la règle, puis faites défiler vers le bas le panneau **Configurer une règle de télémétrie**. Choisissez **+** à côté de **Actions**, puis choisissez **E-mail** :

    ![Action de règle de température](media/tutorial-configure-rules/addaction.png)

2. Pour définir votre action, utilisez les informations du tableau suivant :

    | Paramètre   | Valeur                          |
    | --------- | ------------------------------ |
    | À        | Votre adresse e-mail             |
    | Notes     | La température du climatiseur a dépassé le seuil. |

    > [!NOTE]
    > Pour recevoir une notification par courrier électronique, l’adresse e-mail doit être un [ID d’utilisateur dans l’application](howto-administer.md), et l’utilisateur doit s’être connecté au moins une fois à l’application.

    ![Action Température](media/tutorial-configure-rules/temperatureaction.png)

3. Sélectionnez **Enregistrer**. Votre règle est listée dans la page **Règles**.

## <a name="test-the-rule"></a>Tester la règle

Peu après l’enregistrement de la règle, elle devient active. Lorsque les conditions définies dans la règle sont remplies, votre application envoie un message à l’adresse e-mail que vous avez spécifiée dans l’action.

> [!NOTE]
> Une fois le test terminé, désactivez la règle pour ne plus recevoir d’alertes dans votre boîte de réception.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

Maintenant que vous avez défini une règle basée sur un seuil, nous vous suggérons de passer à l’étape suivante, [Personnaliser les vues de l’opérateur](tutorial-customize-operator.md).

Pour en savoir plus sur les différents types de règles dans Azure IoT Central et comment paramétrer la définition de règle, consultez :
* [Créer une règle de télémétrie et configurer des notifications](howto-create-telemetry-rules.md).
* [Créer une règle d’événements et configurer des notifications](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->