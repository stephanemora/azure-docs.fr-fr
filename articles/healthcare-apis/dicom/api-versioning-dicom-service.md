---
title: Contrôle de version de l’API pour le service DICOM-API de santé Azure
description: Ce guide donne une vue d’ensemble des stratégies de version de l’API pour le service DICOM.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: c07a963a9dfeaa8e05d79c43cff18ab934f44f1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434052"
---
# <a name="api-versioning-for-dicom-service"></a>Contrôle de version de l’API pour le service DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Ce guide de référence fournit une vue d’ensemble des stratégies de version de l’API pour le service DICOM. 

Toutes les versions des API DICOM sont toujours conformes aux spécifications DICOMweb™ standard, mais les versions peuvent exposer des API différentes en fonction de l' [instruction de conformité DICOM](dicom-services-conformance-statement.md).

## <a name="specifying-version-of-rest-api-in-requests"></a>Spécification de la version de l’API REST dans les demandes

La version de l’API REST doit être explicitement spécifiée dans l’URL de la demande, comme dans l’exemple suivant :

`<service_url>/v<version>/studies`

Les itinéraires actuellement sans version sont toujours pris en charge. Par exemple, `<service_url>/studies` a le même comportement que la spécification de la version v 1.0-version préliminaire. Toutefois, nous vous recommandons vivement de spécifier la version dans toutes les demandes par le biais de l’URL.

## <a name="supported-versions"></a>Versions prises en charge

Actuellement, les versions prises en charge sont les suivantes :

* v 1.0-version préliminaire

Le document OpenApi pour les versions prises en charge est disponible à l’adresse suivante :
 
`<service_url>/{version}/api.yaml`

## <a name="prerelease-versions"></a>Versions préliminaires

Une version d’API avec l’étiquette « version préliminaire » indique que la version n’est pas prête pour la production et ne doit être utilisée que dans les environnements de test. Ces points de terminaison peuvent rencontrer des modifications avec rupture sans préavis.

## <a name="how-versions-are-incremented"></a>Mode d’incrémentation des versions

À l’heure actuelle, nous incrémentons uniquement la version principale lorsqu’il existe une modification avec rupture, qui est considérée comme n’étant pas à compatibilité descendante. Toutes les versions mineures sont supposées être 0. Toutes les versions sont au format `Major.0` .

Voici quelques exemples de modifications avec rupture (la version principale est incrémentée) :

1. Attribution d’un nouveau nom ou suppression des points de terminaison.
2. Suppression de paramètres ou ajout de paramètres obligatoires.
3. Modification du code d’État.
4. La suppression d’une propriété dans une réponse ou la modification d’un type de réponse, mais il est possible d’ajouter des propriétés à la réponse.
5. Modification du type d’une propriété.
6. Comportement lorsqu’une API change, par exemple les modifications de la logique métier utilisées pour faire foo, mais elle s’exécute maintenant.

Modifications sans rupture (la version n’est pas incrémentée) :

1. Ajout de propriétés qui acceptent les valeurs null ou qui ont une valeur par défaut.
2. Ajout de propriétés à un modèle de réponse.
3. Modification de l’ordre des propriétés.

## <a name="header-in-response"></a>En-tête en réponse

ReportApiVersions est activé, ce qui signifie que nous retournerons les versions prises en charge par l’API-versions et les versions obsolètes de l’API, le cas échéant.

* les versions prises en charge par l’API répertorient les versions prises en charge pour l’API demandée. Elle est renvoyée uniquement lors de l’appel d’un point de terminaison annoté avec `ApiVersion("<someVersion>")` .

* API-Deprecated-versions répertorient les versions qui ont été dépréciées pour l’API demandée. Elle est renvoyée uniquement lors de l’appel d’un point de terminaison annoté avec `ApiVersion("<someVersion>", Deprecated = true)` .

Exemple :

ApiVersion (« 1.0 »)

ApiVersion (« 1.0-version préliminaire », déprécié = true)

[![Versions prises en charge et déconseillées de l’API. ](media/api-supported-deprecated-versions.png) ](media/api-supported-deprecated-versions.png#lightbox)

