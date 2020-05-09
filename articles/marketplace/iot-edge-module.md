---
title: Module IoT Edge de la place de marché Azure
description: Offre Module IoT Edge sur la Place de marché Microsoft Azure pour les éditeurs de services.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 762d9947046f159e992f09211bfcd76ff8d6712e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684340"
---
# <a name="iot-edge-modules"></a>Modules IoT Edge

La plateforme [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) repose sur Cloud Azure.  Cette plateforme permet aux utilisateurs de déployer des charges de travail cloud en vue de les exécuter directement sur des appareils IoT.  Un module IoT Edge peut exécuter des charges de travail hors connexion et effectuer une analyse de données localement. Ce type d’offre permet d’économiser la bande passante, de protéger les données locales et sensibles et offre des temps de réponse à faible latence.  Vous pouvez désormais tirer parti de ces charges de travail prédéfinies. Jusqu’à présent, seules quelques solutions internes de Microsoft étaient disponibles.  Vous deviez investir du temps et des ressources dans la création de vos propres solutions IoT personnalisées.

En introduisant les [modules IoT Edge sur la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), nous mettons à la disposition des éditeurs une destination unique pour exposer et vendre leurs solutions à l’audience IoT. Les développeurs IoT peuvent finalement trouver et acheter des fonctionnalités pour accélérer le développement de leurs solutions.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principaux avantages des modules Azure IoT Edge sur la Place de marché Microsoft Azure

| **Pour les éditeurs**    | **Pour les clients (développeurs IoT)**  |
| :------------------- | :-------------------|
| Atteindre des millions de développeurs qui cherchent à créer et à déployer des solutions IoT Edge.  | Composer une solution IoT Edge avec la confiance de pouvoir utiliser des composants sécurisés et testés. |
| Publier une seule fois et exécuter sur tout matériel IoT Edge qui prend en charge les conteneurs. | Réduire les délais de commercialisation en recherchant et déployant des modules IoT Edge internes et tiers pour des besoins spécifiques. |
| Monétiser avec les options de facturation flexibles. <ul> <li> Licence gratuite et BYOL (apportez votre propre licence). </li> </ul> | Effectuer des achats avec votre choix de modèles de facturation. <ul> <li> Licence gratuite et BYOL (apportez votre propre licence). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Qu’est-ce qu’un module IoT Edge ?

Azure IoT Edge vous permet de déployer et de gérer la logique métier sur la périphérie sous la forme de modules. Les modules Azure IoT Edge sont les plus petites unités de calcul gérées par IoT Edge. Ils peuvent contenir des services Microsoft (par exemple Azure Stream Analytics), des services tiers ou votre propre code spécifique à la solution. Pour en savoir plus sur les modules IoT Edge, consultez [Présentation des modules Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Quelle est la différence entre un type d’offre Conteneur et un type d’offre Module IoT Edge ?**

Le type d’offre Module IoT Edge est un type spécifique de conteneur s’exécutant sur un appareil IoT Edge. Il est fourni avec des paramètres de configuration par défaut pour s’exécuter dans le contexte d’IoT Edge et utilise éventuellement le SDK du module IoT Edge pour être intégré au runtime IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publication de votre module IoT Edge

**Sélection de la vitrine appropriée**

Les modules IoT Edge sont uniquement publiés sur la Place de marché Microsoft Azure. AppSource ne s’applique pas.  Pour plus d’informations sur les différences entre les vitrines et leur public cible, consultez [Détermination de l’option de publication pour votre solution](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Options de facturation**

La place de marché prend en charge les options de facturation **Gratuit** et **BYOL (apportez votre propre licence)** pour les modules IoT Edge.
 
**Options de publication**

Dans tous les cas, les modules IoT Edge doivent sélectionner l’option de publication **Transact**.  Consultez [Choisir une option de publication](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) pour plus d’informations sur les options de publication.  

## <a name="eligibility-criteria"></a>Critères d’éligibilité

Toutes les conditions des contrats et politiques de la Place de marché Microsoft Azure s’appliquent aux offres Module IoT Edge.  En outre, il existe des prérequis et des exigences techniques pour les modules IoT Edge.  

**Conditions préalables**

Pour publier un module IoT Edge sur la Place de marché Microsoft Azure, vous devez satisfaire aux prérequis suivants :

- Accès à l’Espace partenaires. Pour plus d’informations, consultez le [Guide de publication sur AppSource et la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Héberger votre module IoT Edge dans un registre de conteneurs Azure. 
- Disposer des métadonnées de votre module IoT Edge suivantes, (liste non exhaustive) : 
    - Un titre
    - Une description (au format HTML)
    - Une image de logo (format PNG et tailles d’image fixes, y compris 40x40px, 90x90px, 115x115px, 255x115px)
    - Conditions d’utilisation et politique de confidentialité
    - Configuration du module par défaut (route, propriétés souhaitées du jumeau, createOptions, variables d’environnement)
    - Documentation
    - Contacts du support

**Exigences techniques**

Les principales exigences techniques pour qu’un module IoT Edge soit certifié et publié sur la Place de marché Azure sont détaillées dans [Préparer vos ressources techniques de module IoT Edge](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Documentation et ressources

[Créer une offre de module IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) : étapes de publication d’une nouvelle offre de module IoT Edge dans l’Espace partenaires.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait,

- [En savoir plus](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Pour vous inscrire dans l’Espace partenaires et commencer à créer une offre ou à en utiliser une existante,

- Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Pour plus d’informations sur la publication d’une offre de module IoT Edge, consultez [Créer une offre de module Azure IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md).
