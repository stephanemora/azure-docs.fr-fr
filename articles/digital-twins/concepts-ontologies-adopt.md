---
title: Adoption d’ontologies conformes aux standards du secteur
titleSuffix: Azure Digital Twins
description: En savoir plus sur les ontologies existantes d’un secteur, qui peuvent être adoptées pour Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563353"
---
# <a name="adopting-an-industry-ontology"></a>Adoption de l’ontologie d’un secteur

Dans la mesure où il est peut-être plus facile de commencer avec une ontologie DTDL open source que de partir d’une page vierge, Microsoft s’associe à des experts de domaine pour publier des ontologies. Celles-ci représentent des conventions largement acceptées dans le secteur et prennent en charge toutes sortes de cas d’usage pour les clients. 

Il en résulte un ensemble d’ontologies open source en langage DTDL, qui reposent sur des standards du secteur, en tirent parti ou les utilisent directement. Ces ontologies sont conçues pour répondre aux besoins des développeurs en aval. Elles ont le potentiel nécessaire pour être largement adoptées et/ou étendues par le secteur.

À l’heure actuelle, Microsoft travaille avec des partenaires immobiliers pour développer une ontologie des bâtiments intelligents. Cette initiative vise à fournir une base commune à la modélisation des bâtiments intelligents en fonction des standards du secteur pour éviter de réinventer ce qui existe déjà. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologie des bâtiments intelligents RealEstateCore

Microsoft s’est associé à [RealEstateCore](https://www.realestatecore.io/), un consortium suédois de propriétaires immobiliers, d’éditeurs de logiciels et d’instituts de recherche pour proposer une ontologie DTDL open source liée au secteur du bâtiment : l’[**ontologie RealEstateCore DTDL pour les bâtiments intelligents**](https://github.com/Azure/opendigitaltwins-building).

Cette ontologie des bâtiments intelligents fournit une base commune à la modélisation des bâtiments intelligents en fonction des standards du secteur (par exemple le  [schéma BRICK](https://brickschema.org/ontology/) ou la  [spécification Building Topology Ontology du W3C](https://w3c-lbd-cg.github.io/bot/index.html)) pour éviter de réinventer ce qui existe déjà. Un ensemble de bonnes pratiques accompagne cette ontologie pour qu’elle puisse être consommée et étendue correctement. 

Pour en savoir plus sur la structure et les conventions de modélisation de l’ontologie ainsi que sur la façon de l’utiliser, de l’étendre et d’y contribuer, accédez à son [dépôt sur GitHub](https://github.com/Azure/opendigitaltwins-building). 

Vous pouvez également en savoir plus sur le partenariat avec RealEstateCore et sur les objectifs de cette initiative en consultant le billet de blog suivant ainsi que la vidéo qui l’accompagne : [*RealEstateCore, une ontologie des bâtiments intelligents pour Digital Twins, est désormais disponible*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail comment étendre les ontologies conformes aux standards du secteur pour répondre aux impératifs de vos spécifications : [*Concepts : Extension des ontologies du secteur*](concepts-ontologies-extend.md).

* Sinon, passez au développement de modèles à partir d’ontologies : [*Utilisation de stratégies d’ontologie pour le développement de modèles*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).