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
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870979"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Forum aux questions sur l’API Azure pour FHIR

## <a name="what-is-fhir"></a>Qu’est-ce que FHIR ?
FHIR (Prononcez "fire") ou Fast Healthcare Interoperability Resources est une norme d’interopérabilité destinée à permettre l’échange de données médicales entre différents systèmes de santé. Cette norme a été développée par l’organisation HL7 et est en cours d’adoption par les organisations de la santé du monde entier. La version actuelle de FHIR est la version R4 (Release 4). L’API Azure pour FHIR prend en charge R4 et prend également en charge la version précédente STU3 (Standard for Trial Use 3). Pour plus d’informations sur FHIR, accédez à [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Les données utilisées par les API FHIR sont-elles stockées dans Azure ?

Oui, les données sont stockées dans des bases de données managées dans Azure. L’API Azure pour FHIR ne fournit pas un accès direct à la banque de données sous-jacente.

## <a name="what-identity-provider-do-you-support"></a>Quel fournisseur d’identité prenez-vous en charge ?

Nous prenons actuellement en charge Microsoft Azure Active Directory en tant que fournisseur d’identité.

## <a name="what-fhir-version-do-you-support"></a>Quelle version de FHIR prenez-vous en charge ?

Nous prenons en charge les versions 4.0.0 et 3.0.1 dans le cadre de l’API Azure pour FHIR (PaaS) et de FHIR Server pour Azure (open source).

Pour plus d’informations, consultez [Fonctionnalités prises en charge](fhir-features-supported.md). Lisez ce qui a changé entre les versions dans l’[historique des versions de HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Quelle est la différence entre l’implémentation open source de Microsoft FHIR Server pour Azure et l’API Azure pour FHIR ?

L’API Azure pour FHIR est une version hébergée et managée de la version open source de Microsoft FHIR Server pour Azure. Dans le service managé, Microsoft fournit toutes les opérations de maintenance et mises à jour. 

Quand vous exécutez FHIR Server pour Azure, vous avez un accès direct aux services sous-jacents. Toutefois, vous êtes également responsable de la maintenance et de la mise à jour du serveur ainsi que de tous les travaux de conformité nécessaires si vous stockez des données PHI.

Du point de vue du développement, chaque fonctionnalité est d’abord déployée sur l’implémentation open source de Microsoft FHIR Server pour Azure. Une fois la version open source validée, elle est mise en production sur la solution PaaS API Azure pour FHIR. Le délai de mise en production entre la version open source et la solution PaaS dépend de la complexité des fonctionnalités et d’autres priorités relatives à la feuille de route. 

## <a name="what-is-smart-on-fhir"></a>Qu’est-ce que SMART sur FHIR ?

SMART (Substitutable Medical Applications and Reusable Technology) sur FHIR est un ensemble de spécifications ouvertes qui permettent d’intégrer les applications partenaires avec les serveurs FHIR et d’autres systèmes informatiques médicaux, par exemple les dossiers médicaux électroniques et les échanges d’informations médicales. En créant une application SMART sur FHIR, vous avez la garantie que votre application est accessible et exploitable par une multitude de systèmes distincts.
Authentification et API Azure pour FHIR. Pour plus d’informations sur SMART, accédez à [SMART Health IT](https://smarthealthit.org/).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu des questions fréquentes sur l’API Azure pour FHIR. Découvrez les fonctionnalités prises en charge dans FHIR Server pour Azure :
 
>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge de FHIR](fhir-features-supported.md)