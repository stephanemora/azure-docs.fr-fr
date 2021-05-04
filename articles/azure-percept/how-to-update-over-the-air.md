---
title: Mettre à jour votre DK Azure Percept en OTA
description: Découvrez comment recevoir des mises à jour en OTA pour votre DK Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: e5d431d4594a89d4bfcd3b4f09c57b5cdd61b547
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929486"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Mettre à jour votre DK Azure Percept en OTA

Suivez ce guide pour découvrir comment mettre à jour le système d’exploitation et le microprogramme de la carte support de votre DK Azure Percept en OTA avec Device Update pour IoT Hub.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md) : vous avez connecté votre kit de développement à un réseau Wi-Fi, créé un IoT Hub et connecté votre kit de développement à celui-ci
- [Device Update pour IoT Hub a été correctement configuré](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Importez votre fichier de mise à jour et votre fichier manifeste

> [!NOTE]
> Si vous avez déjà importé la mise à jour, vous pouvez passer directement à **Créer un groupe de mises à jour d’appareil**.

1. [Téléchargez le fichier manifeste (.json)](https://go.microsoft.com/fwlink/?linkid=2155625) et le [fichier de mise à jour (.swu)](https://go.microsoft.com/fwlink/?linkid=2161538) pour votre appareil Azure Percept.

1. Accédez au hub IoT Azure que vous utilisez pour votre appareil Azure Percept. Dans le panneau de menu gauche, sélectionnez **Mises à jour de l’appareil** sous **Gestion automatique des appareils**.

1. Plusieurs onglets s’affichent dans la partie supérieure de l’écran. Sélectionnez l’onglet **Mises à jour**.

1. Sélectionnez **+ Importer une nouvelle mise à jour** sous l’en-tête **Prêt pour le déploiement**.

1. Cliquez sur les cases sous **Sélectionner le fichier manifeste d’importation** et **Sélectionner les fichiers de mise à jour** pour sélectionner votre fichier manifeste (.json) et votre fichier de mise à jour (.swu).

1. Sélectionnez l’icône de dossier ou la zone de texte sous **Sélectionner un conteneur de stockage** et sélectionnez le compte de stockage approprié. Si vous avez déjà créé un conteneur de stockage, vous pouvez le réutiliser. Sinon, sélectionnez **+ Conteneur** pour créer un conteneur de stockage pour les mises à jour en OTA. Sélectionnez le conteneur que vous souhaitez utiliser, puis cliquez sur **Sélectionner**.

1. Sélectionnez **Soumettre** pour démarrer le processus d’importation. En raison de la taille de l’image, le processus d’envoi peut prendre jusqu’à 5 minutes.

    > [!NOTE]
    > Vous pouvez être invité à ajouter une règle CORS (Cross Origin Request) pour accéder au conteneur de stockage sélectionné. Sélectionnez **Ajouter une règle et réessayer** pour continuer.

1. Au début du processus d’importation, vous êtes redirigé vers l’onglet **Historique d’importation** de la page **Mises à jour de l’appareil**. Cliquez sur **Actualiser** pour contrôler la progression jusqu’à la fin du processus d’importation. En fonction de la taille de la mise à jour, cette opération peut prendre quelques minutes ou plus (pendant les heures de pointe, le service d’importation peut prendre jusqu’à une heure).

1. Lorsque la colonne **État** indique que l’importation a réussi, sélectionnez l’onglet **Prêt pour le déploiement** et cliquez sur **Actualiser**. Vous devez maintenant voir votre mise à jour importée dans la liste.

## <a name="create-a-device-update-group"></a>Créer un groupe de mises à jour d’appareil

Device Update pour IoT Hub vous permet de cibler une mise à jour vers des groupes spécifiques de DK Azure Percept. Pour créer un groupe, vous devez ajouter une étiquette à votre ensemble cible d’appareils dans Azure IoT Hub.

> [!NOTE]
> Si vous avez déjà créé un groupe, vous pouvez passer directement à la section suivante.

Conditions d’utilisation des étiquettes de groupe :

- Vous pouvez ajouter n’importe quelle valeur à votre étiquette, sauf « Sans catégorie », qui est une valeur réservée.
- Le nom de l’étiquette ne peut pas dépasser 255 caractères.
- La valeur de l’étiquette ne peut contenir que les caractères spéciaux suivants : « . »,  « - »,  « _ », « ~ ».
- Les noms des groupes et des étiquettes respectent la casse.
- Un appareil ne peut avoir qu’une seule étiquette. Toute étiquette ajoutée ultérieurement à l’appareil remplacera l’étiquette précédente.
- Un appareil ne peut appartenir qu’à un seul groupe.

1. Ajouter une étiquette à votre ou vos appareils :

    1. Dans **IoT Edge**, dans le volet de navigation gauche, recherchez votre DK Azure Percept et accédez à son **Jumeau d’appareil**.

    1. Ajoutez une nouvelle valeur d’étiquette **Device Update pour IoT Hub** comme indiqué ci-dessous (```<CustomTagValue>``` fait référence à votre valeur/nom d’étiquette, par exemple GroupeAzurePercept1). Apprenez-en davantage sur les [étiquettes de document JSON](../iot-hub/iot-hub-devguide-device-twins.md#device-twins) de jumeau d’appareil.

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Cliquez sur **Enregistrer** et résolvez les éventuels problèmes de mise en forme.

1. Créer un groupe en sélectionnant une étiquette Azure IoT Hub existante :

    1. Revenez à votre page Azure IoT Hub.

    1. Sélectionnez **Mises à jour de l’appareil** sous **Gestion automatique des appareils** dans le panneau de menu gauche.

    1. Sélectionnez l’onglet **Groupes** . La page affiche le nombre d’appareils non groupés connectés à Device Update.

    1. Sélectionnez **+ Ajouter** pour créer un groupe.

    1. Sélectionnez une étiquette IoT Hub dans la liste et cliquez sur **Envoyer**.

    1. Une fois le groupe créé, le graphique de conformité de mise à jour et la liste des groupes sont mis à jour. Le graphique montre le nombre d’appareils dans différents états de conformité : **Mise à jour la plus récente**, **Nouvelles mises à jour disponibles**, **Mises à jour en cours** et **Pas encore groupés**.

## <a name="deploy-an-update"></a>Déployer une mise à jour

1. Vous devez voir votre nouveau groupe avec une nouvelle mise à jour listée sous **Mises à jour disponibles** (vous devrez peut-être actualiser une fois). Sélectionnez la mise à jour.

1. Vérifiez que le groupe d’appareils approprié est sélectionné comme groupe d’appareils cible. Sélectionnez une **Date de début** et une **Heure de début** pour votre déploiement, puis cliquez sur **Créer un déploiement**.

    > [!CAUTION]
    > Si vous définissez une heure de début dans le passé, le déploiement se déclenche immédiatement.

1. Vérifiez le graphique de conformité. Vous devez constater que la mise à jour est maintenant en cours.

1. Une fois la mise à jour terminée, votre graphique de conformité reflètera le nouvel état de mise à jour.

1. Sélectionnez l’onglet **Déploiements** en haut de la page **Mises à jour de l’appareil**.

1. Sélectionnez votre déploiement pour en afficher les détails. Vous devrez peut-être cliquer sur **Actualiser** jusqu’à ce que l’**État** passe à **Réussi**.

## <a name="next-steps"></a>Étapes suivantes

Votre kit de développement est maintenant mis à jour. Vous pouvez continuer le développement et l’exploitation avec votre devkit.
