---
title: FAQ sur la Place de marché des modules IoT Edge| Microsoft Docs
description: FAQ sur la Place de marché des modules IoT Edge.
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
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806149"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Forum aux questions sur la Place de marché des modules IoT Edge


## <a name="what-is-the-edge-module-marketplace"></a>Qu'est-ce que la Place de marché des modules Edge ?


La Place de marché des modules Edge est une liste de modules Edge prédéfinis et *certifiés* qui sont *accessibles* via la Place de marché Microsoft Azure.

![Modules IoT Edge](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Où les modules Edge seront-ils visibles ? 


Sur la [place de marché du portail Azure](https://ms.portal.azure.com/) (expérience authentifiée), sous la catégorie Internet des objets, étiquette « Module IoT Edge ».

Et sur le [site web de la Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (expérience anonyme) sous la catégorie Internet des objets, étiquette « Module IoT Edge ».

## <a name="is-it-open-to-partners"></a>Est-ce ouvert aux partenaires ?


Pas encore. Actuellement, seuls les modules créés par Microsoft sont publiés dans la section IoT Edge de la place de marché. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Pourquoi les partenaires doivent-ils publier leurs modules IoT Edge ?


-   Pour accroître la visibilité de leurs produits en ajoutant ce canal au marché et en y présentant leurs solutions.

-   Pour obtenir plus de prospects. La Place de marché Microsoft Azure leur permet d'obtenir des informations sur les personnes intéressées par leur solution.

-   Soyez parmi les premiers à tirer parti de nouvelles fonctionnalités de monétisation.

## <a name="what-is-the-onboarding-process"></a>Quel est le processus d’intégration ?


Bien qu'il ne soit pas encore accessible, le processus d’intégration passera par la Place de marché Microsoft Azure. Pour plus d'informations, consultez le [Guide de publication sur AppSource et la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Reportez-vous au type de liste de transactions des conteneurs. 

Les partenaires devront d'abord devenir éditeur pour la Place de marché Microsoft Azure. Ils pourront ensuite intégrer leurs modules Edge via le [portail Cloud Partner](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Existe-t-il des fonctionnalités de monétisation ?


Elles ne sont pas prêtes à l’emploi pour le moment. Notre premier objectif est d'ouvrir une place de marché avec des modules Edge *gratuits* ou *BYOL (apportez votre propre licence)*. Nous ajouterons ensuite d'autres fonctionnalités de monétisation, comme un modèle de facturation de la consommation.

## <a name="what-is-bring-your-own-license-byol"></a>Qu'est-ce que le modèle BYOL (apportez votre propre licence) ?


Dans les [Politiques concernant la participation à la Place de marché Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/), la définition officielle est la suivante :

- *Les clients obtiennent le droit d'accéder à l'offre ou de l'utiliser en dehors de la Place de marché Azure et ne font l'objet d'aucune facturation pour l'utilisation de l'offre au sein de la Place de marché Azure.*

Il appartient aux partenaires de concéder une licence de leur logiciel et de percevoir des revenus pour celui-ci.

## <a name="can-partners-publish-a-freemium-module"></a>Les partenaires peuvent-ils publier un module « freemium » ?


Oui. Les modules freemium, disponibles gratuitement mais avec certaines restrictions, seront considérés comme n’importe quelle autre publication.

## <a name="is-intellectual-property-protected"></a>La propriété intellectuelle est-elle protégée ?


Un module Edge est un conteneur compatible Docker. Il incombe aux partenaires de protéger leur propriété intellectuelle dans les conteneurs distribués.

## <a name="where-will-the-modules-be-hosted"></a>Où les modules seront-ils hébergés ?


Les modules IoT Edge seront hébergés dans un registre de conteneurs appartenant à Microsoft qui pourra être interrogé de façon anonyme, comme le Hub Docker.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Quels sont les plans d’intégration entre la Place de marché Microsoft Azure et les outils Azure IoT ?

Nous allons établir une intégration plus étroite entre la Place de marché Microsoft Azure et les outils Azure IoT. Nous envisageons par exemple d’autoriser la navigation sur la Place de marché des modules IoT Edge à partir du portail IoT Hub ou de Visual Studio Code.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Quel système d’exploitation ou quelle architecture mon conteneur doit-il prendre en charge ?

Les modules IoT Edge doivent prendre en charge la même matrice système d’exploitation / architecture qu'IoT Edge en production. Cette liste est gérée dans la [Prise en charge par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support). Par exemple, un module doit actuellement prendre en charge Linux x64 et Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Y a-t-il d’autres contraintes de certification à connaître ?

L'ensemble exact des contraintes de certification est encore à l'étude. Nos principes directeurs sont les suivants :

-   Promouvoir la qualité plutôt que la quantité.

-   Des conteneurs spécifiques à IoT Edge (et non des conteneurs aléatoires).

-   Qualité de production.

-   Déploiement en 1 clic (au moins un ensemble de valeurs de configuration par défaut fourni).

## <a name="any-other-questions"></a>D’autres questions ?


Contactez l'équipe d'[intégration d'Azure IoT Edge](mailto:azureiotedgeonboarding@service.microsoft.com).
