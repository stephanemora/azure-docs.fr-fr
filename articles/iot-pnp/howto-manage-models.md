---
title: Gérer les modèles de préversion d’IoT Plug-and-Play dans le référentiel | Microsoft Docs
description: Comment gérer les modèles de capacité d’appareil dans le référentiel à l’aide du portail Azure Certified pour IoT, de l’interface Azure CLI et de Visual Studio Code.
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: 1b71d8bd0f0417c7dc408c580a1c73ac654743ce
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932856"
---
# <a name="manage-models-in-the-repository"></a>Gérer les modèles dans le référentiel

Le référentiel de modèles de préversion d’IoT Plug-and-Play stocke les interfaces et les modèles de capacité d'appareil. Le référentiel rend les modèles et les interfaces détectables et consommables par les développeurs de solutions.

Trois outils sont à votre disposition pour gérer le référentiel :

- Le portail Azure Certified pour IoT
- L’interface Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>Référentiels de modèles

Il existe deux types de référentiel de modèles pour le stockage des interfaces et des modèles de capacité d’appareil :

- Il existe un _référentiel public_ unique qui stocke les interfaces et les modèles de capacité des appareils figurant dans le [catalogue d’appareils Azure Certified pour IoT](https://aka.ms/iotdevcat). Ce référentiel stocke également les [interfaces courantes](./concepts-common-interfaces.md), ainsi que [les interfaces et modèles de capacité d’appareil publiés par les partenaires Microsoft](./howto-onboard-portal.md). Pour savoir comment certifier un appareil et ajouter son modèle de capacité d’appareil au référentiel public, consultez le tutoriel [Certifier votre appareil IoT Plug-and-Play](./tutorial-certification-test.md).
- Il existe plusieurs _référentiels d’entreprise_. Un référentiel d’entreprise est automatiquement créé pour votre organisation quand vous l’[intégrez au portail Azure Certified pour IoT](./howto-onboard-portal.md). Vous pouvez utiliser votre référentiel d’entreprise pour stocker vos interfaces et modèles de capacité d’appareil pendant le développement et le test.

## <a name="azure-certified-for-iot-portal"></a>Portail Azure Certified pour IoT

Dans le [portail Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com), vous pouvez effectuer les tâches suivantes :

- [Terminer le processus de certification pour votre appareil IoT](./tutorial-certification-test.md).
- Rechercher des modèles de capacité d’appareil IoT Plug-and-Play. Vous pouvez utiliser ces modèles pour [créer rapidement des appareils prêts pour l’IoT et les intégrer à des solutions](./quickstart-connect-pnp-device-solution.md).

## <a name="azure-cli"></a>D’Azure CLI

L’interface Azure CLI fournit des commandes pour la gestion des interfaces et modèles de capacité d’appareil dans les référentiels de modèles publics et d’entreprise IoT Plug-and-Play. Pour plus d’informations, consultez le guide pratique [Installer et utiliser l’extension Azure IoT pour Azure CLI](./howto-install-pnp-cli.md).

## <a name="visual-studio-code"></a>Visual Studio Code

Pour ouvrir la vue **Référentiel de modèles** dans Visual Studio Code.

1. Ouvrez Visual Studio Code, utilisez **Ctrl+Maj+P**, tapez et sélectionnez **IoT Plug-and-Play : Ouvrir le référentiel de modèles**.

1. Vous pouvez **ouvrir le référentiel de modèles public** ou **ouvrir le référentiel de modèles organisationnel**. Pour le référentiel de modèles d’entreprise, vous devez entrer votre chaîne de connexion de référentiel de modèles. Vous trouverez cette chaîne de connexion dans le [portail Microsoft Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com), sous l’onglet **Chaînes de connexion**, pour votre **référentiel d’entreprise**.

1. Un nouvel onglet ouvre la vue **Référentiel de modèles**.

    Utilisez cette vue pour ajouter, télécharger et supprimer des interfaces et des modèles de capacité d’appareil. Vous pouvez utiliser un filtre pour rechercher des éléments spécifiques dans cette liste.

1. Pour basculer entre le référentiel de modèles de votre entreprise et le référentiel de modèles public, utilisez **Ctrl+Maj+P**, puis tapez et sélectionnez **IoT Plug-and-Play : Se déconnecter du référentiel de modèles**. Utilisez ensuite la commande **IoT Plug-and-Play : Ouvrir le référentiel de modèles** une nouvelle fois.

> [!NOTE]
> Dans VS Code, le référentiel de modèles public est en lecture seule. Les partenaires Microsoft peuvent mettre à jour le référentiel public dans le [portail Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante suggérée consiste à découvrir comment [soumettre un appareil IoT Plug-and-Play à la certification](tutorial-certification-test.md).
