---
title: Vue d’ensemble du service DICOM-API Azure Healthcare
description: Dans cet article, vous découvrirez les concepts du service DICOM, de l’imagerie médicale et du service DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 7cff5c0095bdb9d230673f347cdf5b3e6d099d17
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122433578"
---
# <a name="overview-of-the-dicom-service"></a>Vue d’ensemble du service DICOM

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Cet article décrit les concepts relatifs à DICOM, à l’imagerie médicale et au service DICOM.

## <a name="medical-imaging"></a>Imagerie médicale

L’imagerie médicale est la technique et le processus de création de représentations visuelles de l’intérieur d’un corps pour l’analyse clinique et l’intervention médicale, ainsi que la représentation visuelle de la fonction de certains organes ou tissus (physiologie). L’imagerie médicale cherche à révéler des structures internes masquées par la peau et les os, ainsi qu’à diagnostiquer et à traiter les maladies. L’imagerie médicale établit également une base de données d’anatomie et de physiologie normale pour permettre d’identifier les anomalies. Bien qu’il soit possible d’effectuer des opérations de création d’images d’organes et de tissus supprimés pour des raisons médicales, ces procédures font généralement partie du pathologie au lieu de l’imagerie médicale. [Wikipédia, imagerie médicale](https://en.wikipedia.org/wiki/Medical_imaging)

## <a name="dicom"></a>DICOM

DICOM (Digital Imaging and Communications en médecine) est la norme internationale pour la transmission, le stockage, la récupération, l’impression, le traitement et l’affichage d’informations sur les images médicales. il s’agit de la norme principale d’imagerie médicale acceptée dans le secteur de la santé. Bien qu’il existe certaines exceptions (dentisterie, vétérinaire), presque toutes les spécialisations médicales, les fabricants d’équipements, les fournisseurs de logiciels et les praticiens individuels s’appuient sur DICOM à une étape quelconque de tout flux de travail médical impliquant la création d’images. DICOM garantit que les images médicales sont conformes aux normes de qualité, afin que l’exactitude du diagnostic puisse être préservée. La plupart des modalités d’acquisition d’images, y compris la tomographie informatisée (CT), l’imagerie par résonance magnétique (MRI) et l’Ultrasound doivent être conformes aux normes DICOM. Les images au format DICOM doivent être accessibles et utilisées par le biais d’applications DICOM spécialisées.

## <a name="dicom-service"></a>Service DICOM

un service DICOM est un service géré qui a besoin d’un abonnement Azure et d’un compte Azure Active Directory à déployer sur l’espace de travail azure Healthcare api. Il permet la communication basée sur des normes avec les &trade; systèmes DICOMweb activés. Le service DICOM injecte des métadonnées DICOM dans un service FHIR ou un serveur FHIR, ce qui permet une seule source de vérité pour les données cliniques et les métadonnées d’acquisition d’images. 

La nécessité d’intégrer efficacement des données non-cliniques est devenue aiguë. Pour traiter efficacement les patients, rechercher de nouveaux traitements, diagnostiquer des solutions ou fournir une vue d’ensemble efficace de l’historique d’intégrité d’un seul patient, les organisations doivent intégrer des données dans plusieurs sources. L’une des intégrations les plus pressantes est entre les données cliniques et d’imagerie.

FHIR &trade; devient une norme importante pour les données cliniques et fournit une extensibilité pour prendre en charge l’intégration d’autres types de données directement ou par le biais de références. En utilisant le service DICOM, les organisations peuvent stocker des références aux données d’acquisition d’images dans FHIR &trade; et activer des requêtes qui traversent des jeux de données cliniques et d’imagerie. Cela peut permettre de nombreux scénarios différents, par exemple :

- **Création des cohortes pour la recherche.** Souvent par le biais de requêtes pour les patients qui correspondent aux données dans les systèmes cliniques et de création d’images, tels que celui-ci (qui a déclenché l’effort d’intégration des &trade; données FHIR et DICOM) : «J’ai tous les médicaments prescrits avec tous les documents de numérisation CT et leurs rapports de radiologie associés pour tout patient âgé de plus de 45 qui avait un diagnostic de osteosarcoma au cours
- **Recherche de résultats pour les patients similaires pour comprendre les options et planifier les traitements.** Lorsqu’il est présenté avec un diagnostic de patient, un médecin peut identifier les résultats des patients et les plans de traitement pour les patients passés avec un diagnostic similaire, même s’ils incluent des données d’acquisition d’images.
- **Fournir une vue longitudinale d’un patient pendant le diagnostic.** Radiologists, en particulier teleradiologists, n’ont souvent pas un accès complet à l’historique médical d’un patient et les études d’images associées. Grâce à &trade; l’intégration de FHIR, ces données peuvent être facilement fournies, même pour Radiologists en dehors du réseau local de l’organisation.
- **Fermeture de la boucle de commentaires avec teleradiologists.** Idéalement, un radiologist a accès aux données cliniques d’un hôpital pour fermer la boucle de commentaires après avoir fait une recommandation. Toutefois, pour teleradiologists, cela n’est souvent pas le cas. Au lieu de cela, ils sont souvent incapables de fermer la boucle de commentaires après avoir effectué un diagnostic, car ils n’ont pas accès aux données des patients après la lecture initiale. Sans (ou limité) accès aux résultats cliniques ou aux résultats, ils ne peuvent pas obtenir les commentaires nécessaires pour améliorer leurs compétences. Comme sur teleradiologist, placez-le : «prenez les parathyroïdes par exemple. Nous faisons plus que tout autre stage dans le pays, et j’ai encore Beg et plaidoiries pour que les chirurgiens me disent ce qu’ils ont réellement trouvés. Parmi plus de 500 études que je fais chaque mois, je reçois des commentaires directs sur seulement trois ou quatre.»  Grâce à l’intégration à FHIR &trade; , une organisation peut facilement créer un outil qui fournira des commentaires directs à teleradiologists, les aidant ainsi à affiner leurs compétences et à apporter de meilleures recommandations à l’avenir.
- **fermeture de la boucle de commentaires pour les modèles AI/ML.** Les modèles de machine learning conviennent mieux lorsque les commentaires réels peuvent être utilisés pour améliorer leurs modèles. toutefois, les fournisseurs de modèles de ML tiers obtiennent rarement les commentaires dont ils ont besoin pour améliorer leurs modèles au fil du temps. Par exemple, un ISV le met de la façon suivante : «nous utilisons une combinaison de modèles de machines et d’experts humains pour recommander un plan de traitement pour la chirurgie cardiaque. Toutefois, nous obtenons rarement des commentaires des médecins sur la précision de notre plan. Par exemple, nous recommandons souvent une taille de stent. Nous aimerions recevoir des commentaires sur si notre prédiction était correcte, mais le seul moment où nous entendons les clients est lorsqu’il y a un problème majeur avec nos recommandations.» Comme avec les commentaires pour teleradiologists, l’intégration à FHIR &trade; permet aux organisations de créer un mécanisme pour fournir des commentaires au pipeline de reformation du modèle.

## <a name="deploy-dicom-service-to-azure"></a>Déployer le service DICOM sur Azure

Le service DICOM a besoin d’un abonnement Azure pour configurer et exécuter les composants requis. Ces composants sont, par défaut, créés à l’intérieur d’un groupe de ressources Azure existant ou nouveau pour simplifier la gestion. en outre, un compte de Azure Active Directory est requis. Pour chaque instance du service DICOM, nous créons une combinaison de ressources isolées et mutualisées.

## <a name="summary"></a>Récapitulatif

Cet article conceptuel vous a fourni une vue d’ensemble de DICOM, de l’imagerie médicale et du service DICOM.
 
## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser le service DICOM, consultez :

>[!div class="nextstepaction"]
>[Déployer le service DICOM sur Azure](deploy-dicom-services-in-azure.md)

>[!div class="nextstepaction"]
>[Utilisation &trade; des API standard DICOMweb avec le service DICOM](dicomweb-standard-apis-with-dicom-services.md)
