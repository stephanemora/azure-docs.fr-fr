---
title: Liste de vérification préalable à la certification pour les offres de modules IoT Edge dans la Place de marché Azure
description: En savoir plus sur les conditions de certification spécifiques à la publication d’offres de modules IoT Edge dans la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 4782e6c0ed866c467051fc1811a60df0010a3db1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743085"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Liste de vérification préalable à la certification pour les modules IoT Edge

> [!NOTE]
> Nous recommandons vivement aux éditeurs de consulter cette liste de contrôle et de valider la fonctionnalité du module avant de demander des certifications. Cela permet d’accélérer votre processus de certification en réduisant le besoin de modifications et de retransmissions.

## <a name="validation-of-image"></a>Validation de l’image

Une fois que l’image de module Edge est prête à être envoyée, procédez comme suit pour vous assurer que l’image fonctionne comme prévu par Microsoft.

### <a name="steps-to-perform-in-the-azure-portal"></a>Étapes à effectuer dans le Portail Azure

1. Ouvrez le [portail Azure](https://partner.microsoft.com/).
1. Créez un groupe de ressources.
1. Créez un IoT Hub.
1. Créez un appareil IoT Edge.
1. Copiez la chaîne de connexion et enregistrez-la dans Bloc-notes.
1. Sélectionnez les **modules définis sur l’appareil Edge créé**.
1. Ajoutez les détails de l’ACR où se trouve la dernière version de l’image.
1. Sélectionnez **Ajouter module IoT Edge** et indiquez :
    - L’URI d’image dans le paramètre de module
    - La variable d’environnement (identique à celle ajoutée dans l’Espace partenaires)
    - Les options de création de conteneur (identiques à celles ajoutées dans l’Espace partenaires)
    - Le paramètre de jumeau de module (identique à celui ajouté dans l’Espace partenaires)
1. Ajoutez des itinéraires (identiques à ceux ajoutés dans l’Espace partenaires).
1. Sélectionnez **Vérifier + créer**.

Les modules Edge sont déployés sur l’appareil Edge créé sur Azure.

### <a name="steps-to-perform-on-the-device"></a>Étapes à effectuer sur l’appareil

#### <a name="device-details"></a>Informations sur l’appareil

L’équipe de certification utilise le matériel suivant pour valider les images sur différentes architectures :

- Pour les images x64, une machine virtuelle Azure avec une taille de configuration D2s v3 standard exécutant Ubuntu Server 18.04/Ubuntu Server 16.04.
- Pour les images Azure Resource Manager 32, Raspberry Pi 3 modèle B.
- Pour les images Azure Resource Manager 64, NVIDIA Jetson Nano 4 Go.

#### <a name="steps"></a>Étapes

1. Assurez-vous que les appareils/machines virtuelles créés sont accessibles par le biais de Putty.
1. Téléchargez le [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge) sur l’appareil.
1. Mettez à jour la chaîne de connexion copiée à l’étape 5 dans le fichier config.yaml.
1. Redémarrez le module Edge avec `sudo systemctl restart iotedge`.
1. Vérifiez si le module est déployé sur l’appareil avec `sudo iotedge list` ; il doit être à l’état En cours d’exécution.
1. Vérifiez que les journaux du module déployé avec `sudo iotedge logs “Module Name“ -f` ne comportent pas d’erreurs. S’il existe des erreurs connues, décrivez-les dans l’Espace partenaires **Notes pour le réviseur** avant de soumettre l’offre.

## <a name="metadata-validation"></a>Validation des métadonnées

Vérifiez les éléments suivants :

- La balise la plus récente est indiquée à la fois dans l’Espace partenaires et Azure Container Registry.
- La configuration matérielle minimale requise est ajoutée à la description de l’offre.
- Le nom d’utilisateur et le mot de passe du registre de conteneurs Azure sont mis à jour et ajoutés dans l’Espace partenaires.
- Exactitude de la **propriété de jumeau** souhaitée, *le cas échéant*.
- Exactitude des **variables d’environnement** souhaitées, *le cas échéant*.
- Exactitude des **options de création** souhaitées, *le cas échéant*.
- La chaîne de connexion de gestion des prospects est présente.
- Politique de confidentialité présente
- Conditions d’utilisation présentes

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modules à partir de la place de marché commerciale](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [Publier le module Edge dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [Déployer un module IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
