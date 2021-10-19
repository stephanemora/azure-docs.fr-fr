---
title: Qu’est-ce que le service FHIR ?
description: Le service FHIR permet l’échange rapide de données via les API FHIR. Ingérez, gérez et conservez les informations PHI (informations médicales protégées) avec un service cloud managé.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 08/03/2021
ms.author: chrupa
ms.openlocfilehash: 5ddbef4874a5efd9f63a099f8da52204065b6b4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779063"
---
# <a name="what-is-fhirreg-service"></a>Qu’est-ce que le &reg; service FHIR ?

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Le service FHIR dans les API de santé Azure (par exemple, le service FHIR) permet l’échange rapide de données via des API FHIR® (Fast Healthcare Interoperability Resources), soutenu par une offre PaaS (Platform-as-a-service) gérée dans le Cloud. Elle permet à quiconque travaille sur des données médicales d’ingérer, de gérer et de conserver les informations [PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) (informations médicales protégées) dans le cloud : 

- Service FHIR managé, provisionné dans le cloud en quelques minutes 
- point de terminaison de niveau Enterprise, FHIR dans Azure pour l’accès aux données et stockage au format FHIR
- Haute performance, faible latence
- Gestion sécurisée des données PHI (informations médicales protégées) dans un environnement cloud conforme
- SMART sur FHIR pour les implémentations mobiles et web
- Contrôler vos propres données à grande échelle avec la fonctionnalité RBAC (contrôle d’accès en fonction du rôle)
- Suivi des journaux d’audit pour l’accès, la création, la modification et les lectures dans chaque magasin de données

Le service FHIR vous permet de créer et de déployer un serveur FHIR en quelques minutes pour tirer parti de la mise à l’échelle élastique du Cloud. Les services Azure qui alimentent le service FHIR sont conçus pour des performances rapides, quelle que soit la taille des jeux de données que vous gérez.

L’API FHIR et le magasin de données conforme vous permettent de vous connecter de manière sécurisée et d’interagir avec les systèmes qui utilisent les API FHIR.  Microsoft prend en charge les opérations, la maintenance, les mises à jour et les exigences de conformité de l’offre PaaS, pour que vous puissiez libérer vos propres ressources opérationnelles et de développement. 

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Tirer profit de la puissance de vos données avec FHIR

Le secteur de la santé transforme rapidement les données médicales pour les rendre conformes à la nouvelle norme [FHIR&reg;](https://hl7.org/fhir) (Fast Healthcare Interoperability Resources). FHIR offre un modèle de données robuste et extensible incluant une sémantique et un échange de données normalisés pour permettre à tous les systèmes utilisant FHIR de travailler ensemble.  La transformation de vos données au format FHIR vous permet de connecter rapidement des sources de données existantes telles que des systèmes de dossiers médicaux électroniques ou des bases de données de recherche. FHIR permet également l’échange rapide de données dans les implémentations modernes du développement web et mobiles. Plus important encore, FHIR peut simplifier l’ingestion des données et accélérer le développement à l’aide d’outils d’analytique et de machine learning.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Gérer de manière sécurisée les données médicales dans le cloud

Le service FHIR permet l’échange de données via des API FHIR, RESTful et cohérentes basées sur la spécification HL7 FHIR. Basée sur une offre PaaS managée dans Azure, elle fournit également un environnement scalable et sécurisé pour la gestion et le stockage des données PHI (informations médicales protégées) au format FHIR natif.  

### <a name="free-up-your-resources-to-innovate"></a>Libérer vos ressources pour innover

Vous pouvez investir dans la création et l’exécution de vos propres serveurs FHIR, mais avec le service FHIR dans les API de santé Azure, Microsoft prend en charge la charge de travail des opérations, la maintenance, les mises à jour et les exigences de conformité, ce qui vous permet de libérer vos propres ressources opérationnelles et de développement.

### <a name="enable-interoperability-with-fhir"></a>Activer l’interopérabilité avec FHIR

L’utilisation du service FHIR vous permet de vous connecter avec n’importe quel système qui tire parti des API FHIR pour la lecture, l’écriture, la recherche et d’autres fonctions. Elle peut être utilisée comme un outil puissant pour centraliser, normaliser et appliquer le machine learning aux données cliniques provenant de dossiers médicaux électroniques, de tableaux de bord de cliniciens et de patients, de programmes de supervision à distance ou de bases de données externes à votre système et qui disposent d’API FHIR.

### <a name="control-data-access-at-scale"></a>Contrôler l’accès aux données à grande échelle

Vous contrôlez vos données. La fonctionnalité RBAC (contrôle d’accès en fonction du rôle) vous permet de gérer la façon dont vos données sont stockées et accessibles. En fournissant une sécurité accrue et en réduisant la charge de travail d’administration, vous déterminez qui a accès aux jeux de données que vous créez, en fonction des définitions de rôles que vous créez pour votre environnement.  

### <a name="secure-your-data"></a>Sécurisez vos données

Protégez vos données PHI à l’aide d’une intelligence de sécurité inégalée. Vos données sont isolées dans une seule base de données par instance d’API et sont protégées par un basculement multirégion. Le service FHIR implémente une défense en profondeur et une protection avancée contre les menaces pour vos données.  

## <a name="applications-for-the-fhir-service"></a>Applications pour le service FHIR

Les serveurs FHIR sont des outils clés pour l’interopérabilité des données médicales. Le service FHIR est conçu comme une API et un service que vous pouvez créer, déployer et commencer à utiliser rapidement. Étant donné que le standard FHIR se développe dans le secteur de la santé, les cas d’utilisation continuent de croître, mais certaines applications clientes initiales où le service FHIR est utile sont les suivantes : 

- **Démarrage/IOT et développement d’applications :**  Les clients développant un patient ou une application centrée sur le fournisseur (mobile ou Web) peuvent tirer parti du service FHIR en tant que service backend entièrement géré. Le service FHIR fournit une ressource précieuse dans la mesure où les clients peuvent gérer les données et échanger des données dans un environnement de Cloud sécurisé conçu pour les données d’intégrité, tirer parti des instructions d’implémentation de FHIR INTELLIGENTes et permettre à leur technologie d’être utilisée par tous les systèmes fournisseurs (par exemple, la plupart des EHRs ont activé les API de lecture FHIR).   

- **Écosystèmes médicaux :**  Bien que les DME (dossiers médicaux électroniques) représentent une « source officielle » pour de nombreux paramètres cliniques, il n’est pas rare que les fournisseurs disposent de plusieurs bases de données qui ne sont pas connectées les unes aux autres, ou qui stockent les données dans des formats distincts.  L’utilisation du service FHIR en tant que service qui se trouve au-dessus de ces systèmes vous permet de normaliser les données au format FHIR.  Cela vous permet d’activer l’échange de données sur plusieurs systèmes avec un format de données cohérent. 

- **Recherche :** Les chercheurs en santé trouveront la norme FHIR en général et le service FHIR utile, car il normalise les données autour d’un modèle de données FHIR commun et réduit la charge de travail pour le Machine Learning et le partage de données.
Exchange de données via le service FHIR fournit des journaux d’audit et des contrôles d’accès qui permettent de contrôler le workflow des données et qui a accès aux types de données. 

## <a name="fhir-from-microsoft"></a>FHIR de Microsoft

Les fonctionnalités FHIR de Microsoft sont disponibles dans trois configurations :

* Service FHIR dans les API de santé Azure : offre PaaS dans Azure, facilement approvisionnée dans le Portail Azure et gérée par Microsoft. Offre la possibilité de configurer d’autres jeux de données, tels que DICOM dans le même espace de travail. Cette version est disponible en version préliminaire publique. 
* API Azure pour FHIR : offre PaaS dans Azure, facilement approvisionnée dans le Portail Azure et gérée par Microsoft. Cette implémentation comprend uniquement des données FHIR et est un produit GA. 
* FHIR Server pour Azure : projet open source qui peut être déployé sur votre abonnement Azure, disponible sur GitHub à l’adresse https://github.com/Microsoft/fhir-server.

Pour les cas d’utilisation qui nécessitent l’extension ou la personnalisation du serveur FHIR, ou qui nécessitent l’accès aux services sous-jacents, par exemple la base de données, sans passer par les API FHIR, les développeurs doivent choisir le projet open source FHIR Server pour Azure.  Pour l’implémentation d’une API FHIR prête pour la production et d’un service principal où les données persistantes doivent être accessibles uniquement par le biais de l’API FHIR, les développeurs doivent choisir le service FHIR.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser le service FHIR, suivez le Guide de démarrage rapide de 5 minutes pour déployer le service FHIR.

>[!div class="nextstepaction"]
>[Déployer le service FHIR](fhir-portal-quickstart.md)
