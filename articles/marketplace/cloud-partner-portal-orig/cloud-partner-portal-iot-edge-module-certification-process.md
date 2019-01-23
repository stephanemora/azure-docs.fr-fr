---
title: Certification de module IoT Edge | Microsoft Docs
description: Certifiez un module IoT Edge pour la Place de marché.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c8056bd4912605a4cd3ee333b1be87d4f3a6d5ba
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198046"
---
# <a name="the-iot-edge-module-certification-process"></a>Processus de certification de module IoT Edge

Cet article décrit les étapes et les exigences à respecter pour certifier un module IoT Edge en vue de le publier sur la Place de marché Azure. 

>[!Note]
>Il s’agit d’un document provisoire. La Place de marché des modules IoT Edge est créée en parallèle et cet article sera remplacé par la documentation publique.

## <a name="understanding-an-iot-edge-module"></a>Compréhension d’un module IoT Edge

Terminologie des modules :

-   Une **image de module** est un package contenant le logiciel qui définit un module.

-   Une **instance de module** est l’unité de calcul spécifique exécutant l’image de module sur un appareil IoT Edge. L’instance de module est démarrée par le runtime IoT Edge.

Les modules peuvent aussi inclure le kit SDK de module IoT, qui utilise la terminologie suivante :

-   Une **identité de module** est une information (informations d’identification de sécurité) stockée dans IoT Hub et associée à chaque instance du module.

-   Un **jumeau de module** est un document JSON stocké dans IoT Hub qui contient les informations d’état relatives à une instance de module, notamment des métadonnées, des configurations et des conditions.

-   Les **kits SDK** servent à développer des modules personnalisés dans plusieurs langages, notamment : C\#, C, Python, Java et Node.JS.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>Processus d’intégration d’un module IoT Edge

La capture d’écran suivante illustre le processus de publication d’un module IoT Edge sur la Place de marché Azure.

![Processus de certification](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Détail des étapes d’intégration

-   **Étape 1** : les partenaires soumettent leur offre avec le type d’offre **Module IoT Edge** dans l’outil Portail Cloud Partner de l’équipe de la Place de marché Azure. Vous devez avoir la qualité de partenaire MS officiel pour pouvoir accéder à l’outil Portail Cloud Partner. Pour plus d’informations, consultez le [guide de publication](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **Étape 2** : la Place de marché exécute automatiquement des vérifications antivirus et anti-programme malveillant. L’équipe IoT peut exécuter ses tests d’ingestion automatisés personnalisés au cours de cette phase.

-   **Étape 3** : le module est public. Il est référencé sur la Place de marché et le conteneur peut être extrait de façon anonyme à partir d’une URL. Par exemple, *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>Critères de certification de module IoT Edge

Les principales conditions à remplir pour qu’un module IoT Edge soit certifié et publié sur la Place de marché Azure sont indiquées ci-dessous.

>[!Note]
>Ces conditions peuvent évoluer. Vous en serez informé par avance et disposerez d’un délai pour mettre à jour vos conteneurs pour qu’ils respectent les nouvelles conditions.

### <a name="technical-requirements"></a>Exigences techniques

**Être un module IoT Edge**

-   Seuls les conteneurs compatibles avec Docker sont actuellement pris en charge en tant que modules IoT Edge. Le module doit s’exécuter sur [Moby](https://mobyproject.org/). 

    >[!Note]
    >Les conteneurs Docker sont censés fonctionner avec Moby, car Moby est le projet open source sous-jacent de Docker.

-   Le partenaire doit fournir les paramètres par défaut suivants : 

    -   Une valeur par défaut **tag** (qui ne peut pas être vide.)

    -   Une valeur par défaut **createOptions** (qui peut être vide.)

    -   Si vous utilisez le kit SDK Module IoT, une valeur par défaut **twin** (qui peut être vide.)

    -   Si vous utilisez le kit SDK Module IoT, une valeur par défaut **routes** (qui peut être vide.)

**Prise en charge de plateformes**

-   Seules les plateformes **mises à la disposition générale** dans IoT Edge Niveau 1 (comme indiqué dans [Prise en charge par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)) doivent être prises en charge. Par exemple, cela signifie pour l’heure que les combinaisons d’architectures et de systèmes d’exploitation suivantes doivent être prises en charge :

    -   Ubuntu Server 18.04 pour x64

    -   Ubuntu Server 16.04 pour x64

    -   Raspbian-stretch pour arm32 (armhf)

**Dimensionnement d’appareil**

-   Tout appareil ayant des dimensions (UC/RAM/stockage/GPU, etc.) équivalentes à celles d’un Raspberry Pi ou supérieur peut faire office d’appareil IoT Edge. Si un module ne fonctionne qu’avec des contraintes de dimension spécifiques, ces contraintes doivent être indiquées dans la description du module.

**Paramètres par défaut**

-   Un module doit commencer avec les paramètres par défaut prédéfinis pour chaque plateforme prise en charge (système d’exploitation + architecture).

-   Les paramètres de configuration doivent être clairement documentés (balises, variables d’environnement, jumeau, itinéraires.) Dans le référencement, les partenaires peuvent définir une description pour leur module qui prend en charge le balisage HTML de base ou qui pointe vers une page web externe.

**Contrôle de version**

-   Les clients doivent pouvoir choisir de mettre à jour automatiquement un module issu de la Place de marché ou d’utiliser la version exacte qu’ils ont testée. Les modules de la Place de marché doivent suivre le même modèle de contrôle de version que le runtime IoT Edge. Par exemple : 

    -   Les balises de propagation comme « 1.0 » peuvent être mises à jour.

    -   Les balises de versions mineures comme « 1.3.24 » ne peuvent pas être mises à jour.

**Télémétrie**

-   Les modules utilisant le kit SDK Module IoT doivent définir l’identificateur de module unique affecté par la Place de marché à des fins de télémétrie. Cela permet à la Place de marché Azure d’identifier le nombre d’instances de module qui s’exécutent. Cet identificateur unique est le nom de conteneur affecté par la Place de marché au moment de l’ingestion. Pour définir cet identificateur, utilisez les méthodes des kits SDK suivants :
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

-   Pour les modules qui n’utilisent pas le SDK Module IoT, des insights moins précis sont disponibles via le portail Cloud Partner. Par exemple, le nombre de téléchargements.

**Sécurité**

-   Les conteneurs doivent accéder à l’hôte avec le moins de privilèges possible. Les conteneurs « privilégiés » sont très rares.

-   Les partenaires doivent préserver la sécurité de leur module dans le cadre du support qu’ils fournissent.

**Mises à jour**

-   Les partenaires doivent faire en sorte que leur module reste à jour et fonctionnel. Si des évolutions importantes sont prévues pour le runtime IoT Edge, ils en seront notifiés à l’avance.

**Kit SDK Module IoT**

-   Si la certification ne passe pas nécessairement par l’inclusion du kit SDK Module IoT,
    celle-ci peut offrir une meilleure expérience utilisateur avec, par exemple, la prise en charge du routage, de l’envoi de messages au cloud, etc. Le kit SDK Module IoT est nécessaire pour obtenir des données de télémétrie sur le nombre d’instances de module en cours d’exécution.

**Exigence subjective**

-   Doit respecter au moins un cas d’usage IoT Edge réel. Par exemple, un conteneur WordPress ne doit pas être intégré, à moins qu’un client veuille l’utiliser à partir d’IoT Edge.

**Exigences légales (en vertu des politiques d’AMP)**

-   Le module doit respecter les contrats et les politiques de la Place de marché Microsoft Azure. Pour plus d’informations, consultez le Contrat d’Éditeur et les [Politiques concernant la participation](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) ci-joints.

-   Outre les contrats et les politiques de la Place de marché Azure, il peut exister d’autres obligations légales propres à un type d’offre de module IoT Edge. Cet aspect est actuellement examiné.

-   Le module doit s’accompagner de *conditions d’utilisation* et d’une *politique de confidentialité*.

-   Par ailleurs, si le module utilise des produits tiers, un avis dans ce sens doit aussi être inclus.

**Obligations de support (en vertu des politiques d’AMP)**

-   Les partenaires sont tenus d’assurer le support de leurs modules IoT Edge. Ils doivent veiller à ce que les options de support présentées dans la description du module IoT Edge restent disponibles et qu’au moins une option de support soit toujours disponible. (Pour plus d’informations, consultez la Section 4 du Contrat d’Éditeur d’AMP.)

**Regroupement en catégories**

-   Tous les modules IoT Edge se trouvent dans la catégorie **Internet des objets \>Module IoT Edge**. Il appartient au partenaire de choisir la sous-catégorie qui correspond le mieux à son produit.
