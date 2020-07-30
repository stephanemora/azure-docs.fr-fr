---
title: Questions fréquentes (FAQ) sur les services FHIR dans Azure - API Azure pour FHIR
description: Obtenez des réponses aux Questions fréquentes (FAQ) sur l’API Azure pour FHIR, par exemple l’emplacement de stockage des données utilisé par les API FHIR et la prise en charge des versions.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536723"
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

### <a name="what-is-smart-on-fhir"></a>Qu’est-ce que SMART sur FHIR ?

SMART (Substitutable Medical Applications and Reusable Technology) sur FHIR est un ensemble de spécifications ouvertes qui permettent d’intégrer les applications partenaires avec les serveurs FHIR et d’autres systèmes informatiques médicaux, par exemple les dossiers médicaux électroniques et les échanges d’informations médicales. En créant une application SMART sur FHIR, vous avez la garantie que votre application est accessible et exploitable par une multitude de systèmes distincts.
Authentification et API Azure pour FHIR. Pour plus d’informations sur SMART, accédez à [SMART Health IT](https://smarthealthit.org/).


## <a name="iot-connector-preview"></a>Connecteur IoT (préversion)

### <a name="what-is-iomt"></a>Qu’est-ce qu’IoMT ?
IoMT est l’acronyme d’Internet of Medical Things (Internet des objets médicaux) et désigne une catégorie d’appareils IoT qui capturent et échangent des données relatives à la santé et au bien-être avec d’autres systèmes informatiques de santé sur un réseau. Voici quelques exemples d’appareils IoMT : technologies portables à usage clinique et sportif, capteurs de surveillance, dispositifs de suivi d’activité, kiosques de soins ou même pilule intelligente.

### <a name="how-many-iot-connectors-do-i-need"></a>De combien de connecteurs IoT ai-je besoin ?
Un seul connecteur IoT peut être utilisé pour ingérer des données provenant d’un grand nombre de types d’appareils différents. Vous pouvez néanmoins décider d’utiliser différents connecteurs pour les raisons suivantes :
- **Mise à l’échelle** : Pour la préversion publique, la capacité des ressources Connecteur IoT est fixe et devrait fournir un débit d’environ 200 messages par seconde. Si vous avez besoin d’un débit plus élevé, vous pouvez ajouter d’autres connecteurs IoT.
- **Type d’appareil** : Vous pouvez configurer un connecteur IoT distinct pour chaque type d’appareil IoMT dont vous disposez pour des raisons de gestion des périphériques.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Existe-t-il une limite du nombre de connecteurs IoT sur la préversion publique ?
Oui, vous ne pouvez créer que deux connecteurs IoT par abonnement tant que la fonctionnalité est en préversion publique. Cette limite existe pour empêcher les dépenses inattendues, car la fonctionnalité est disponible gratuitement pendant la préversion. À la demande, cette limite peut être augmentée jusqu’à un maximum de cinq connecteurs IoT.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Pour quelles régions Azure la fonctionnalité Connecteur IoT est-elle disponible sur la préversion publique ?
Connecteur IoT est disponible dans toutes les régions Azure où API Azure pour FHIR est disponible.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Puis-je configurer la capacité de mise à l’échelle pour Connecteur IoT ?
Étant donné que Connecteur IoT est gratuit pendant la préversion publique, sa capacité de mise à l’échelle est fixe et limitée. La configuration de Connecteur IoT disponible en préversion publique est supposée fournir un débit d’environ 200 messages par seconde. Une certaine forme de configuration de la capacité des ressources sera disponible en disponibilité générale.

### <a name="what-fhir-version-does-iot-connector-support"></a>Quelle version de FHIR Connecteur IoT prend-il en charge ?
Connecteur IoT prend actuellement en charge FHIR version R4 uniquement. Par conséquent, cette fonctionnalité est uniquement visible sur les instances R4 d’API Azure pour FHIR, et Microsoft ne prévoit pas de prendre en charge la version STU3 pour le moment.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Pourquoi ne puis-je pas installer Connecteur IoT quand Azure Private Link est activé sur API Azure pour FHIR ?
Connecteur IoT ne prend pas en charge la capacité Azure Private Link pour l’instant. Par conséquent, si vous avez activé Azure Private Link sur API Azure pour FHIR, vous ne pouvez pas installer Connecteur IoT et vice-versa. Cette limitation devrait disparaître lorsque Connecteur IoT sera disponible en disponibilité générale.

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Quelle est la différence entre le connecteur IoMT FHIR open source pour Azure et la fonctionnalité Connecteur IoT du service API Azure pour FHIR ?
Connecteur IoT est une version hébergée et gérée du connecteur IoMT FHIR open source pour Azure. Dans le service managé, Microsoft fournit toutes les opérations de maintenance et mises à jour.

Quand vous exécutez Connecteur IoMT FHIR pour Azure, vous avez un accès direct aux ressources sous-jacentes. Toutefois, vous êtes également responsable de la maintenance et de la mise à jour du serveur ainsi que de tous les travaux de conformité nécessaires si vous stockez des données PHI.

Du point de vue du développement, chaque fonctionnalité est d’abord déployée sur le connecteur IoMT FHIR open source pour Azure. Une fois la version open source validée, elle est mise en production sur la fonctionnalité Connecteur IoT PaaS du service API Azure pour FHIR. Le délai de mise en production entre la version open source et la solution PaaS dépend de la complexité de la fonctionnalité et d’autres priorités relatives à la feuille de route.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu des questions fréquentes sur l’API Azure pour FHIR. Découvrez les fonctionnalités prises en charge dans FHIR Server pour Azure :
 
>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge de FHIR](fhir-features-supported.md)