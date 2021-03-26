---
title: Adoption d’ontologies conformes aux standards du secteur
titleSuffix: Azure Digital Twins
description: En savoir plus sur les ontologies existantes d’un secteur, qui peuvent être adoptées pour Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124225"
---
# <a name="adopting-an-industry-ontology"></a>Adoption de l’ontologie d’un secteur

Dans la mesure où il est peut-être plus facile de commencer avec une ontologie DTDL open source que de partir d’une page vierge, Microsoft s’associe à des experts de domaine pour publier des ontologies. Celles-ci représentent des conventions largement acceptées dans le secteur et prennent en charge toutes sortes de cas d’usage pour les clients. 

Il en résulte un ensemble d’ontologies open source en langage DTDL, qui reposent sur des standards du secteur, en tirent parti ou les utilisent directement. Ces ontologies sont conçues pour répondre aux besoins des développeurs en aval. Elles ont le potentiel nécessaire pour être largement adoptées et/ou étendues par le secteur.

À l’heure actuelle, Microsoft travaille avec des partenaires pour développer une ontologie des [bâtiments intelligents](#realestatecore-smart-building-ontology) et une ontologie des [villes intelligentes](#smart-cities-ontology). Ces initiatives visent à fournir une base commune à la modélisation conformément aux normes de ces secteurs pour éviter de réinventer ce qui existe déjà. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologie des bâtiments intelligents RealEstateCore

*Trouvez l’ontologie ici : [**Ontologie RealEstateCore basée sur le langage DTDL (Digital Twins Definition Language) pour les bâtiments intelligents**](https://github.com/Azure/opendigitaltwins-building)* .

Microsoft s’est associé à [RealEstateCore](https://www.realestatecore.io/), un consortium suédois de propriétaires immobiliers, d’éditeurs de logiciels et d’instituts de recherche pour proposer cette ontologie DTDL open source liée au secteur immobilier.

Cette ontologie des bâtiments intelligents fournit une base commune à la modélisation des bâtiments intelligents en fonction des standards du secteur (par exemple le  [schéma BRICK](https://brickschema.org/ontology/) ou la  [spécification Building Topology Ontology du W3C](https://w3c-lbd-cg.github.io/bot/index.html)) pour éviter de réinventer ce qui existe déjà. Un ensemble de bonnes pratiques accompagne cette ontologie pour qu’elle puisse être consommée et étendue correctement. 

Pour en savoir plus sur la structure et les conventions de modélisation de l’ontologie ainsi que sur la façon de l’utiliser, de l’étendre et d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building). 

Vous pouvez également en savoir plus sur le partenariat avec RealEstateCore et sur les objectifs de cette initiative en consultant le billet de blog suivant ainsi que la vidéo qui l’accompagne : [RealEstateCore, une ontologie des bâtiments intelligents pour Digital Twins, est désormais disponible](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologie des villes intelligentes

*Trouvez l’ontologie ici : [**Ontologie DTDL (Digital Twins Definition Language) pour les villes intelligentes**](https://github.com/Azure/opendigitaltwins-smartcities)* .

Microsoft a collaboré avec des [OASC (Open Agile Smart Cities)](https://oascities.org/) et [Sirus](https://sirus.be/) pour fournir une ontologie basée sur DTDL pour les villes intelligentes, en commençant par [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Outre ETSI NGSI-LD, nous avons également évalué Saref4City, CityGML et ISO, entre autres.

La version actuelle de l’ontologie se concentre sur un ensemble initial de modèles. Les auteurs de l’ontologie vous invitent à contribuer à l’extension de l’ensemble initial de cas d’usage, ainsi qu’à l’amélioration des modèles existants. 

Pour en savoir plus sur l’ontologie, sur la façon de l’utiliser et sur la façon d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Vous pouvez également obtenir plus d’informations sur les partenariats et l’approche pour les villes intelligentes dans ce billet de blog et la vidéo qui l’accompagne : [Ontologie des villes intelligentes pour Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail comment étendre les ontologies conformes aux standards du secteur pour répondre aux impératifs de vos spécifications : [*Concepts : Extension des ontologies du secteur*](concepts-ontologies-extend.md).

* Sinon, passez au développement de modèles à partir d’ontologies : [*Utilisation de stratégies d’ontologie pour le développement de modèles*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).