---
title: Adoption d’ontologies conformes aux standards du secteur
titleSuffix: Azure Digital Twins
description: En savoir plus sur les ontologies existantes d’un secteur, qui peuvent être adoptées pour Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 73b115ad6f49d943c423c7490df347afce7c5869
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110668419"
---
# <a name="adopting-an-industry-ontology"></a>Adoption de l’ontologie d’un secteur

Dans la mesure où il est peut-être plus facile de commencer avec une ontologie DTDL open source que de partir d’une page vierge, Microsoft s’associe à des experts de domaine pour publier des ontologies. Celles-ci représentent des conventions largement acceptées dans le secteur et prennent en charge différents cas d’usage pour les clients. 

Il en résulte un ensemble d’ontologies open source en langage DTDL, qui reposent sur des standards du secteur, en tirent parti ou les utilisent directement. Ces ontologies sont conçues pour répondre aux besoins des développeurs en aval. Elles ont le potentiel nécessaire pour être largement adoptées et/ou étendues par le secteur.

À ce stade, Microsoft a travaillé avec des partenaires pour développer des ontologies des [bâtiments intelligents](#realestatecore-smart-building-ontology), des [villes intelligentes](#smart-cities-ontology) et des [réseaux énergétiques](#energy-grid-ontology). Ces initiatives visent à fournir une base commune à la modélisation, conformément aux normes de ces secteurs pour éviter d’avoir à réinventer ce qui existe déjà. 

Chaque ontologie porte sur un ensemble initial de modèles. Les auteurs de l’ontologie vous invitent à contribuer à l’extension de l’ensemble initial de cas d’usage et à l’amélioration des modèles existants. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologie des bâtiments intelligents RealEstateCore

*Trouvez l’ontologie dans le référentiel suivant :* [Ontologie RealEstateCore basée sur le langage DTDL (Digital Twins Definition Language) pour les bâtiments intelligents](https://github.com/Azure/opendigitaltwins-building).

Microsoft s’est associé à [RealEstateCore](https://www.realestatecore.io/), un consortium suédois de propriétaires immobiliers, d’éditeurs de logiciels et d’instituts de recherche pour proposer cette ontologie DTDL open source liée au secteur immobilier.

Cette ontologie des bâtiments intelligents fournit une base commune à la modélisation des bâtiments intelligents en fonction des standards du secteur (par exemple le  [schéma BRICK](https://brickschema.org/ontology/) ou la  [spécification Building Topology Ontology du W3C](https://w3c-lbd-cg.github.io/bot/index.html)) pour éviter de réinventer ce qui existe déjà. Un ensemble de bonnes pratiques accompagne cette ontologie pour qu’elle puisse être consommée et étendue correctement. 

Pour en savoir plus sur la structure et les conventions de modélisation de l’ontologie ainsi que sur la façon de l’utiliser, de l’étendre et d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building). 

Vous pouvez également en savoir plus sur le partenariat avec RealEstateCore et sur les objectifs de cette initiative en consultant le billet de blog suivant ainsi que la vidéo qui l’accompagne : [RealEstateCore, une ontologie des bâtiments intelligents pour Digital Twins, est désormais disponible](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologie des villes intelligentes

*Trouvez l’ontologie dans le référentiel suivant :* [Ontologie basée sur le langage DTDL (Digital Twins Definition Language) pour les villes intelligentes](https://github.com/Azure/opendigitaltwins-smartcities).

Microsoft a collaboré avec des [OASC (Open Agile Smart Cities)](https://oascities.org/) et [Sirus](https://sirus.be/) pour fournir une ontologie basée sur DTDL pour les villes intelligentes, en commençant par [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Outre ETSI NGSI-LD, nous avons également évalué Saref4City, CityGML et ISO, entre autres.

Pour en savoir plus sur l’ontologie, sur la façon de l’utiliser et sur la façon d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Vous pouvez également obtenir plus d’informations sur les partenariats et l’approche pour les villes intelligentes dans le billet de blog suivant et la vidéo qui l’accompagne : [Ontologie des villes intelligentes pour Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="energy-grid-ontology"></a>Ontologie des réseaux énergétiques

*Cette ontologie est disponible dans le dépôt suivant :* [Ontologie DTDL (Digital Twins Definition Language) pour les réseaux énergétiques](https://github.com/Azure/opendigitaltwins-energygrid/).

Cette ontologie a été créée pour aider les fournisseurs de solutions à accélérer le développement de solutions Digital Twins pour les cas d’utilisation d’énergie (surveillance des ressources réseau, analyse des défaillances et des impacts, simulation et maintenance prédictive) et faciliter la transition numérique et la modernisation du réseau énergétique. Elle a été adaptée à partir du [Common Information Model (CIM)](https://cimug.ucaiug.org/), norme internationale pour la gestion des ressources des réseaux énergétiques, la modélisation des opérations du système d’alimentation et le marché des produits énergétiques physiques.

Pour en savoir plus sur cette ontologie, sur la façon de l’utiliser et la manière d’y contribuer, accédez à son dépôt sur GitHub : [Azure/opendigitaltwins-energygrid](https://github.com/Azure/opendigitaltwins-energygrid/). 

Vous pouvez également en savoir plus sur les partenariats et l’approche pour les réseaux énergétiques dans le billet de blog suivant : [Ontologie des réseaux énergétiques pour Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/energy-grid-ontology-for-digital-twins-is-now-available/ba-p/2325134).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail comment étendre les ontologies conformes aux standards du secteur pour répondre aux impératifs de vos spécifications : [Concepts : Extension des ontologies du secteur](concepts-ontologies-extend.md).

* Sinon, passez au développement de modèles à partir d’ontologies : [Utilisation de stratégies d’ontologie pour le développement de modèles](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).