---
title: Connecteur Azure IoT pour FHIR (préversion) – Guide de dépannage et procédures
description: Comment résoudre des problèmes de messages d’erreur courants du Connecteur Azure IoT pour FHIR (préversion) et copier des fichiers de mappage
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553680"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guide de dépannage du Connecteur Azure IoT pour FHIR (préversion)

Cet article décrit la procédure de résolution de messages et conditions d’erreur courants du Connecteur Azure IoT pour FHIR*.  

Vous allez également apprendre à créer des copies des fichiers de mappage JSON du Connecteur Azure IoT pour FHIR à des fins de modification et d’archivage en dehors du portail Azure, ou de fourniture de support technique Azure lors de l’ouverture d’un ticket de support. 

## <a name="error-messages-and-fixes"></a>Messages d’erreur et correctifs

|Message   |Condition  |Fix         |
|----------|-----------|------------|
|Nom de mappage non valide, le nom de mappage doit être nu appareil ou FHIR|Le type de mappage fourni n’est pas un appareil ou FHIR|Utilisez l’un des deux types de mappages pris en charge (par exemple : appareil ou FHIR)|
|Régénérer des paramètres de clé non définis|Régénérer une demande de clé|Inclure les paramètres dans la demande de régénération de clé|
|Atteinte du nombre maximal d’instances de Connecteur IoT* qui peuvent être approvisionnées dans cet abonnement|Le quota d’abonnements du Connecteur Azure IoT pour FHIR est atteint (la valeur par défaut est (2) par abonnement)|Supprimez l’une des instances existantes du Connecteur Azure IoT pour FHIR, utilisez un autre abonnement qui n’a pas atteint le quota d’abonnement ou demandez une augmentation du quota d’abonnement|
|Le déplacement de ressource n’est pas pris en charge pour l’API Azure compatible avec le Connecteur IoT pour la ressource FHIR|Tentative d’effectuer une opération de déplacement sur une ressource d’API Azure pour FHIR qui a une ou plusieurs instances du Connecteur Azure IoT pour FHIR|Supprimer des instances existantes du Connecteur Azure IoT pour FHIR pour accomplir l’opération de déplacement|
|Connecteur IoT non approvisionné|Tentative d’utilisation des services enfants (connexions & mappages) lorsque le parent (Connecteur Azure IoT pour FHIR) n’a pas été approvisionné|Approvisionner un Connecteur Azure IoT pour FHIR|
|La demande n’est pas prise en charge|Une requête d’API spécifique n’est pas prise en charge|Utiliser la requête d’API correcte|
|Le compte n’existe pas|La tentative d’ajout d’un connecteur Azure IoT pour FHIR et de l’API Azure pour la ressource FHIR n’existe pas|Créer l’API Azure pour la ressource FHIR, puis retenter l’opération|
|La version de l’API Azure pour la ressource FHIR n’est pas prise en charge pour le Connecteur IoT|Tentative d’utilisation d’un connecteur Azure IoT pour FHIR avec une version incompatible de la ressource API Azure pour FHIR|Créer une nouvelle ressource API Azure pour FHIR (version R4) ou utiliser une ressource API Azure pour FHIR (version R4)

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Création de copies du Connecteur Azure IoT pour les fichiers de mappage FHIR (préversion)
La copie du connecteur Azure IoT pour les fichiers de mappage FHIR peut être utile pour la modification et l’archivage en dehors du site web du portail Azure et pour fournir un support technique Azure lors de l’ouverture d’un ticket de support.

> [!NOTE]
> JSON est le seul format pris en charge pour les fichiers de mappage d’appareil et FHIR à ce stade.

> [!TIP]
> En savoir plus sur le Connecteur Azure IoT pour les [fichiers JSON de mappage d’appareil et FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Sélectionnez **Connecteur IoT (préversion)** en bas à gauche du tableau de bord des ressources de l’API Azure pour FHIR dans la section **Compléments**.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Connecteur IoT" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Sélectionnez le **Connecteur** à partir duquel vous allez copier les fichiers de mappage.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Connecteur IoT" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Ce processus peut également être utilisé pour copier et enregistrer le contenu du JSON **Configurer le mappage FHIR**.

3. Sélectionnez **Configurer le mappage d’appareil**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Connecteur IoT" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Sélectionnez le contenu du JSON et effectuez une opération de copie (par exemple, sélectionnez Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Connecteur IoT" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Effectuez une opération de collage (par exemple, sélectionnez Ctrl + v) dans un nouveau fichier à l’intérieur d’un éditeur (par exemple, Visual Studio Code, Bloc-notes), et enregistrez le fichier avec une extension *.json.

> [!TIP]
> Si vous comptez ouvrir un ticket de [support technique Azure](https://azure.microsoft.com/support/create-ticket/) pour le Connecteur Azure IoT pour FHIR, veillez à inclure des copies de vos fichiers de mappage pour faciliter le processus de résolution des problèmes.

## <a name="next-steps"></a>Étapes suivantes

Consultez les questions fréquemment posées sur le Connecteur Azure IoT pour FHIR.

>[!div class="nextstepaction"]
>[FAQ sur le Connecteur Azure IoT pour FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*Dans le Portail Azure, le Connecteur Azure IoT pour FHIR est appelé Connecteur IoT (préversion).

FHIR est la marque déposée de HL7 et est utilisé avec l’autorisation de HL7.