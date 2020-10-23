---
title: Questions fréquentes (FAQ) sur les services FHIR dans Azure - API Azure pour FHIR
description: Obtenez des réponses aux Questions fréquentes (FAQ) sur l’API Azure pour FHIR, par exemple l’emplacement de stockage des données utilisé par les API FHIR et la prise en charge des versions.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 9c32ebef16750954f3df1a1d1b379bf42853f2b3
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056853"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Forum aux questions sur l’API Azure pour FHIR

## <a name="azure-api-for-fhir"></a>API Azure pour FHIR

### <a name="what-is-fhir"></a>Qu’est-ce que FHIR ?
FHIR (Prononcez "fire") ou Fast Healthcare Interoperability Resources est une norme d’interopérabilité destinée à permettre l’échange de données médicales entre différents systèmes de santé. Cette norme a été développée par l’organisation HL7 et est en cours d’adoption par les organisations de la santé du monde entier. La version actuelle de FHIR est la version R4 (Release 4). L’API Azure pour FHIR prend en charge R4 et prend également en charge la version précédente STU3 (Standard for Trial Use 3). Pour plus d’informations sur FHIR, accédez à [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Les données utilisées par les API FHIR sont-elles stockées dans Azure ?

Oui, les données sont stockées dans des bases de données managées dans Azure. L’API Azure pour FHIR ne fournit pas un accès direct à la banque de données sous-jacente.

### <a name="what-identity-provider-do-you-support"></a>Quel fournisseur d’identité prenez-vous en charge ?

Nous prenons actuellement en charge Microsoft Azure Active Directory en tant que fournisseur d’identité.

### <a name="what-fhir-version-do-you-support"></a>Quelle version de FHIR prenez-vous en charge ?

Nous prenons en charge les versions 4.0.0 et 3.0.1 dans le cadre de l’API Azure pour FHIR (PaaS) et de FHIR Server pour Azure (open source).

Pour plus d’informations, consultez [Fonctionnalités prises en charge](fhir-features-supported.md). Lisez ce qui a changé entre les versions dans l’[historique des versions de HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Quelle est la différence entre l’implémentation open source de Microsoft FHIR Server pour Azure et l’API Azure pour FHIR ?

L’API Azure pour FHIR est une version hébergée et managée de la version open source de Microsoft FHIR Server pour Azure. Dans le service managé, Microsoft fournit toutes les opérations de maintenance et mises à jour. 

Quand vous exécutez FHIR Server pour Azure, vous avez un accès direct aux services sous-jacents. Toutefois, vous êtes également responsable de la maintenance et de la mise à jour du serveur ainsi que de tous les travaux de conformité nécessaires si vous stockez des données PHI.

Du point de vue du développement, chaque fonctionnalité est d’abord déployée sur l’implémentation open source de Microsoft FHIR Server pour Azure. Une fois la version open source validée, elle est mise en production sur la solution PaaS API Azure pour FHIR. Le délai de mise en production entre la version open source et la solution PaaS dépend de la complexité des fonctionnalités et d’autres priorités relatives à la feuille de route. 

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Où puis-je voir les nouveautés de l’API Azure pour FHIR ?

Pour en savoir plus sur les nouveautés de l’API Azure pour FHIR, reportez-vous à la [version release](https://github.com/microsoft/fhir-server/releases) de FHIR Server open source. Nous avons ajouté l’étiquette Azure-API-for-FHIR aux éléments inclus dans la version du service managé, qui sont généralement disponibles deux semaines après leur affichage dans la page de mise en production en open source. Nous avons également inclus [ici](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) des instructions sur la façon de tester la build dans votre propre environnement, si vous le souhaitez. Nous sommes en train d’étudier le meilleur moyen de partager d’autres mises à jour du service managé.

### <a name="what-is-smart-on-fhir"></a>Qu’est-ce que SMART sur FHIR ?

SMART (Substitutable Medical Applications and Reusable Technology) sur FHIR est un ensemble de spécifications ouvertes qui permettent d’intégrer les applications partenaires avec les serveurs FHIR et d’autres systèmes informatiques médicaux, par exemple les dossiers médicaux électroniques et les échanges d’informations médicales. En créant une application SMART sur FHIR, vous avez la garantie que votre application est accessible et exploitable par une multitude de systèmes distincts.
Authentification et API Azure pour FHIR. Pour plus d’informations sur SMART, accédez à [SMART Health IT](https://smarthealthit.org/).

### <a name="can-i-create-a-custom-fhir-resource"></a>Puis-je créer une ressource FHIR personnalisée ?

Nous n’autorisons pas les ressources FHIR personnalisées. Si vous avez besoin d’une ressource FHIR personnalisée, vous pouvez créer une ressource personnalisée en plus de la [Ressource de base](http://www.hl7.org/fhir/basic.html) avec des extensions. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Les [extensions](https://www.hl7.org/fhir/extensibility.html) sont-elles prises en charge sur l’API Azure pour FHIR ?

Nous vous autorisons à charger toutes données JSON FHIR valides dans le serveur. Si vous souhaitez stocker la définition de structure qui définit l’extension, vous pouvez l’enregistrer en tant que ressource de définition de structure. Actuellement, vous ne pouvez pas effectuer de recherche sur les extensions.

### <a name="what-is-the-limit-on-_count"></a>Quelle est la limite de _count ?

La limite actuelle sur le nombre est de 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Existe-t-il des limitations sur la fonctionnalité d’exportation de groupe ?

Pour l’exportation de groupe, vous exportez uniquement les références incluses à partir du groupe, et non toutes les caractéristiques de la [ressource de groupe](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Puis-je publier un bundle sur l’API Azure pour FHIR ?

Nous autorisons la publication de [bundles par lot](https://www.hl7.org/fhir/valueset-bundle-type.html), mais pas de bundles transactionnels dans l’API Azure pour FHIR. Vous pouvez utiliser l’implémentation open source de FHIR Server avec SQL pour publier des bundles transactionnels.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Comment puis-je obtenir toutes les ressources associées à un patient spécifique dans l’API Azure pour FHIR ?

Nous prenons en charge la [recherche par compartiment](https://www.hl7.org/fhir/compartmentdefinition.html) dans l’API Azure pour FHIR. Cela vous permet d’obtenir toutes les ressources associées à un patient spécifique. Notez que le compartiment contient maintenant toutes les ressources associées au patient, mais pas le patient lui-même. Vous devrez également effectuer une recherche pour obtenir le patient si vous avez besoin d’inclure la ressource patient dans vos résultats.

Voici quelques exemples :

* GET Patient/<id>/*
* GET Patient/<id>/Observation
* GET Patient/<id>/Observation?code=8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Où puis-je voir des exemples d’utilisation de l’API Azure pour FHIR dans un workflow ?

Nous avons une collection d’architectures de référence disponibles dans la [page Health Architecture sur GitHub](https://github.com/microsoft/health-architectures).

## <a name="azure-iot-connector-for-fhir-preview"></a>Connecteur Azure IoT pour FHIR (préversion)

### <a name="what-is-iomt"></a>Qu’est-ce qu’IoMT ?
IoMT est l’acronyme d’Internet of Medical Things (Internet des objets médicaux) et désigne une catégorie d’appareils IoT qui capturent et échangent des données relatives à la santé et au bien-être avec d’autres systèmes informatiques de santé sur un réseau. Voici quelques exemples d’appareils IoMT : technologies portables à usage clinique et sportif, capteurs de surveillance, dispositifs de suivi d’activité, kiosques de soins ou même pilule intelligente.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>De combien de connecteurs Azure IoT pour FHIR (préversion) ai-je besoin ?
Un seul Connecteur Azure IoT pour FHIR* peut être utilisé pour ingérer des données provenant d’un grand nombre de types d’appareils différents. Vous pouvez néanmoins décider d’utiliser différents connecteurs pour les raisons suivantes :
- **Mise à l’échelle** : pour la préversion publique, la capacité des ressources du Connecteur Azure IoT pour FHIR est fixe et devrait fournir un débit d’environ 200 messages par seconde. Vous pouvez ajouter des connecteurs Azure IoT pour FHIR si un débit plus élevé est nécessaire.
- **Type d’appareil** : vous pouvez configurer un Connecteur Azure IoT pour FHIR distinct pour chaque type d’appareil IoMT dont vous disposez pour des raisons de gestion des appareils.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Existe-t-il une limite au nombre de connecteurs Azure IoT pour FHIR (préversion) pendant la période de préversion publique ?
Oui, vous ne pouvez créer que deux connecteurs Azure IoT pour FHIR par abonnement tant que la fonctionnalité est en préversion publique. Cette limite existe pour empêcher les dépenses inattendues, car la fonctionnalité est disponible gratuitement pendant la préversion. Sur demande, cette limite peut être augmentée jusqu’à un maximum de cinq connecteurs Azure IoT pour FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Pour quelles régions Azure la fonctionnalité Connecteur Azure IoT pour FHIR est-elle disponible pendant la période de préversion publique ?
Le Connecteur Azure IoT pour FHIR est disponible dans toutes les régions Azure où l’API Azure pour FHIR est disponible.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Puis-je configurer la capacité de mise à l’échelle pour un Connecteur Azure IoT pour FHIR (préversion) ?
Étant donné que le Connecteur Azure IoT pour FHIR est gratuit pendant la période de préversion publique, sa capacité de mise à l’échelle est fixe et limitée. La configuration de Connecteur Azure IoT pour FHIR disponible en préversion publique est supposée fournir un débit d’environ 200 messages par seconde. Une certaine forme de configuration de la capacité des ressources sera disponible en disponibilité générale.

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Quelle version de FHIR le Connecteur Azure IoT pour FHIR (préversion) prend-il en charge ?
Le Connecteur Azure IoT pour FHIR prend actuellement en charge uniquement FHIR version R4. Par conséquent, cette fonctionnalité est uniquement visible sur les instances R4 d’API Azure pour FHIR, et Microsoft ne prévoit pas de prendre en charge la version STU3 pour le moment.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Pourquoi ne puis-je pas installer un Connecteur Azure IoT pour FHIR quand Azure Private Link est activé sur l’API Azure pour FHIR ?
Un Connecteur Azure IoT pour FHIR ne prend pas en charge la capacité d’Azure Private Link pour l’instant. Par conséquent, si vous avez activé Azure Private Link sur l’API Azure pour FHIR, vous ne pouvez pas installer de Connecteur Azure IoT pour FHIR, et vice-versa. Cette limitation devrait disparaître lorsque Connecteur Azure IoT pour FHIR sera en disponibilité générale.

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Quelle est la différence entre le Connecteur IoMT FHIR open source pour Azure et la fonctionnalité du Connecteur Azure IoT pour FHIR (préversion) du service API Azure pour FHIR ?
Le Connecteur Azure IoT pour FHIR est une version hébergée et gérée du connecteur IoMT FHIR open source pour Azure. Dans le service managé, Microsoft fournit toutes les opérations de maintenance et mises à jour.

Quand vous exécutez Connecteur IoMT FHIR pour Azure, vous avez un accès direct aux ressources sous-jacentes. Toutefois, vous êtes également responsable de la maintenance et de la mise à jour du serveur ainsi que de tous les travaux de conformité nécessaires si vous stockez des données PHI.

Du point de vue du développement, chaque fonctionnalité est d’abord déployée sur le connecteur IoMT FHIR open source pour Azure. Une fois la version open source validée, elle est mise en production sur la fonctionnalité de Connecteur Azure IoT pour FHIR PaaS du service API Azure pour FHIR. Le délai de mise en production entre la version open source et la solution PaaS dépend de la complexité de la fonctionnalité et d’autres priorités relatives à la feuille de route.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu des questions fréquentes sur l’API Azure pour FHIR. Découvrez les fonctionnalités prises en charge dans FHIR Server pour Azure :
 
>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge de FHIR](fhir-features-supported.md)

*Dans le Portail Azure, le Connecteur Azure IoT pour FHIR est appelé Connecteur IoT (préversion).

FHIR est la marque déposée de HL7 et est utilisé avec l’autorisation de HL7.