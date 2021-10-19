---
title: FAQ sur les services FHIR dans les API de santé Azure
description: Obtenir des réponses aux questions fréquemment posées sur le service FHIR, telles que l’emplacement de stockage des données derrière les API FHIR et la prise en charge des versions.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.custom: references_regions
ms.openlocfilehash: 7e36e91125e721d8ce5ed4dbc2603109838ca531
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122780008"
---
# <a name="frequently-asked-questions-about-the-fhir-service"></a>Forum aux questions sur le service FHIR

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Cette section traite des questions fréquemment posées sur le service FHIR des API de santé Azure (par le biais du service FHIR).

## <a name="fhir-service-the-basics"></a>Service FHIR : notions de base

### <a name="what-is-fhir"></a>Qu’est-ce que FHIR ?

FHIR (Prononcez "fire") ou Fast Healthcare Interoperability Resources est une norme d’interopérabilité destinée à permettre l’échange de données médicales entre différents systèmes de santé. Cette norme a été développée par l’organisation HL7 et est en cours d’adoption par les organisations de la santé du monde entier. La version actuelle de FHIR est la version R4 (Release 4). Le service FHIR prend en charge R4 et prend également en charge la version précédente STU3 (standard pour l’utilisation d’essai 3). Pour plus d’informations sur FHIR, accédez à [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Les données utilisées par les API FHIR sont-elles stockées dans Azure ?

Oui, les données sont stockées dans des bases de données managées dans Azure. Le service FHIR dans les API de santé Azure ne fournit pas un accès direct au magasin de données sous-jacent.

### <a name="what-identity-provider-do-you-support"></a>Quel fournisseur d’identité prenez-vous en charge ?

Nous prenons actuellement en charge Microsoft Azure Active Directory en tant que fournisseur d’identité.

### <a name="what-fhir-version-do-you-support"></a>Quelle version de FHIR prenez-vous en charge ?

Nous prenons en charge les versions 4.0.0 et 3.0.1.

Pour plus d’informations, consultez [Fonctionnalités prises en charge](fhir-features-supported.md). Découvrez ce qui a changé entre les versions de FHIR (à savoir lors du passage de la version STU3 à la version R4) dans l’[historique des versions de HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="what-is-the-difference-between-the-azure-api-for-fhir-and-the-fhir-service-in-the-healthcare-apis"></a>Quelle est la différence entre l’API Azure pour FHIR et le service FHIR dans les API de santé ?

Le service FHIR est notre implémentation de la spécification FHIR qui se trouve dans les API de santé Azure, ce qui vous permet d’avoir un service FHIR et un service DICOM au sein d’un seul espace de travail. L’API Azure pour FHIR était notre produit de première disponibilité et est toujours disponible en tant que produit autonome. Les principales différences entre les fonctionnalités sont les suivantes :

* Le service FHIR a une limite de 4 to et est en version préliminaire publique alors que l’API Azure pour FHIR prend en charge plus de 4 to et est GA.
* Le service FHIR prend en charge les [offres groupées de transactions](https://www.hl7.org/fhir/http.html#transaction).
* L’API Azure pour FHIR dispose de davantage de fonctionnalités de plateforme (telles que le lien privé, les clés gérées par le client et la journalisation) qui ne sont pas encore disponibles dans le service FHIR dans les API de santé Azure. Pour plus d’informations sur ces fonctionnalités, consultez GA.

### <a name="whats-the-difference-between-fhir-service-in-the-azure-healthcare-apis-and-the-open-source-fhir-server"></a>Quelle est la différence entre « service FHIR dans les API de santé Azure » et le « serveur FHIR » Open source ?

Le service FHIR dans les API de santé Azure est une version hébergée et gérée du serveur Microsoft FHIR Open source pour Azure. Dans le service managé, Microsoft fournit toutes les opérations de maintenance et mises à jour.

Lorsque vous exécutez le serveur FHIR pour Azure, vous avez un accès direct aux services sous-jacents, mais vous êtes responsable de la maintenance et de la mise à jour du serveur, ainsi que de tout travail de conformité requis en cas de stockage de données PHI.

### <a name="in-which-regions-is-the-fhir-service-available"></a>Dans quelles régions le service FHIR est-il disponible ?

Nous développons en permanence les empreintes globales des API de santé en fonction des demandes des clients. Le service FHIR est actuellement disponible dans 25 régions, y compris deux régions du gouvernement américain : Australie-est, sud du Brésil *, centre du Canada, Inde centrale*, Asie est *, est des États-Unis 2, est des États-Unis, Allemagne Nord **, Allemagne Centre-Ouest, Allemagne Nord**, est du Japon, ouest du Japon*, Corée du sud-est, asie du sud-est, Suisse Nord,  Royaume-Uni Sud, Royaume-Uni Ouest, centre-ouest des États-Unis, Europe de l’Ouest, ouest des États-Unis 2, gouvernement américain Virginie, gouvernement américain Arizona.

* indique les régions privées de la version préliminaire ; * * désigne la région de récupération d’urgence uniquement.

Pour plus d’informations, consultez la [prise en charge des régions géographiques](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=all).

### <a name="where-can-i-see-what-is-releasing-into-the-fhir-service"></a>Où puis-je voir ce qui est publié dans le service FHIR ?

Pour voir certains des éléments qui se libèrent dans le service FHIR, reportez-vous à la [version](https://github.com/microsoft/fhir-server/releases) du serveur FHIR Open source. Nous avons travaillé à la baliser des éléments avec FHIR-Service s’ils se libèrent dans le service géré et sont généralement disponibles deux semaines après qu’ils se trouvent sur la page de mise en service dans Open-source. Nous avons également inclus [ici](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) des instructions sur la façon de tester la build dans votre propre environnement, si vous le souhaitez. Nous sommes en train d’étudier le meilleur moyen de partager d’autres mises à jour du service managé.

### <a name="what-is-smart-on-fhir"></a>Qu’est-ce que SMART sur FHIR ?

SMART (Substitutable Medical Applications and Reusable Technology) sur FHIR est un ensemble de spécifications ouvertes qui permettent d’intégrer les applications partenaires avec les serveurs FHIR et d’autres systèmes informatiques médicaux, par exemple les dossiers médicaux électroniques et les échanges d’informations médicales. En créant une application SMART sur FHIR, vous avez la garantie que votre application est accessible et exploitable par une multitude de systèmes distincts.
Pour plus d’informations sur SMART, accédez à [SMART Health IT](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Où puis-je trouver la version de FHIR en cours d’exécution sur ma base de données ?

Vous pouvez trouver la version exacte de FHIR exposée dans l’instruction Capability sous la propriété « fhirVersion » (URL/métadonnées FHIR).

## <a name="fhir-implementations-and-specifications"></a>Implémentations et spécifications de FHIR

### <a name="can-i-create-a-custom-fhir-resource"></a>Puis-je créer une ressource FHIR personnalisée ?

Nous n’autorisons pas les ressources FHIR personnalisées. Si vous avez besoin d’une ressource FHIR personnalisée, vous pouvez créer une ressource personnalisée en plus de la [Ressource de base](http://www.hl7.org/fhir/basic.html) avec des extensions. 

### <a name="are-extensions-supported-on-the-fhir-service"></a>Les [Extensions](https://www.hl7.org/fhir/extensibility.html) sont-elles prises en charge sur le service FHIR ?

Nous vous autorisons à charger toutes données JSON FHIR valides dans le serveur. Si vous souhaitez stocker la définition de structure qui définit l’extension, vous pouvez l’enregistrer en tant que ressource de définition de structure. Pour rechercher des extensions, vous devez [définir vos propres paramètres de recherche](how-to-do-custom-search.md). 

### <a name="what-is-the-limit-on-_count"></a>Quelle est la limite de _count ?

La limite actuelle sur _count est 1000. Si vous affectez à _count une valeur supérieure à 1000, vous recevrez un avertissement dans le groupe auquel seuls 1000 enregistrements seront affichés.

### <a name="can-i-post-a-bundle-to-the-fhir-service"></a>Puis-je envoyer un bundle au service FHIR ?

Nous prenons actuellement en charge la publication de [lots de lots](https://www.hl7.org/fhir/valueset-bundle-type.html) et la publication de lots de [transactions](https://www.hl7.org/fhir/http.html#transaction) dans le service FHIR.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-fhir-service"></a>Comment puis-je obtenir toutes les ressources d’un seul patient dans le service FHIR ?

Nous prenons en charge l' [opération $patient tout](patient-everything.md) ce qui vous permet d’obtenir toutes les données relatives à un seul patient. 

## <a name="using-the-fhir-service"></a>Utilisation du service FHIR

### <a name="where-can-i-see-some-examples-of-using-the-fhir-service-within-a-workflow"></a>Où puis-je voir des exemples d’utilisation du service FHIR dans un flux de travail ?

Nous avons une collection d’architectures de référence disponibles dans la [page Health Architecture sur GitHub](https://github.com/microsoft/health-architectures).

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-fhir-service"></a>Où puis-je voir un exemple de connexion d’une application Web au service FHIR ?

Nous avons une [page GitHub Health Architecture](https://aka.ms/health-architectures) contenant des exemples d’applications et de scénarios. Il illustre la connexion d’une application Web au service FHIR.
