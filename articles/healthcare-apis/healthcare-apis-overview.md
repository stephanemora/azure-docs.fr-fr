---
title: Qu’est-ce que les API de santé Azure ?
description: Cet article est une vue d’ensemble des API de santé Azure.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: 01d808813d944d243582cc631f960a241061b46f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783955"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Qu’est-ce qu’Azure Healthcare API (préversion) ?

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Les données d’intégrité avec lesquelles vous travaillez sont fragmentées sur plusieurs systèmes et formats. La gestion de ces données est suffisamment difficile, mais il semble impossible d’essayer d’obtenir des informations. Vous devez trouver un moyen de rassembler tous ces systèmes et données disparates. Une approche unifiée des données d’intégrité vous permettra de découvrir les Insights opérationnels et cliniques, de connecter de nouvelles applications d’utilisateur final ou d’activer de nouveaux projets de recherche. Les API Azure Healthcare sont un ensemble d’outils et de connecteurs qui vous permettent d’améliorer les soins de santé grâce à des analyses découvertes en rassemblant des ensembles disparates de PHI et en les connectant de bout en bout avec des outils pour la Machine Learning, l’analyse et l’intelligence artificielle.

Les API Azure Healthcare offrent les avantages suivants :
* Autorisez les nouvelles charges de travail à tirer parti de la PHI en permettant la collecte et l’accès aux données dans un même emplacement, de manière cohérente.
* Découvrez de nouvelles connaissances en rapprochant les briquets et en connectant l’informatique de bout en bout avec les outils pour Machine Learning, l’analyse et l’intelligence artificielle.
* Créez un Cloud approuvé en toute confiance dans la façon dont les informations d’intégrité protégées sont gérées, stockées et mises à disposition.
en plus de FHIR, les Microsoft Azure nouvelles api de santé prennent en charge d’autres normes de données du secteur de la santé, telles que DICOM, qui étendent l’interopérabilité des données de santé. Le modèle d’entreprise et la plate-forme d’infrastructure ont été repensées pour prendre en charge l’expansion et l’introduction de normes de données médicales différentes et futures. Les clients peuvent utiliser des données d’intégrité de types différents dans les normes de santé sous le même parapluie de conformité. Les outils ont été intégrés au service géré qui permettent aux clients de transformer les données de formats propriétaires hérités ou d’appareils, en FHIR. Certains de ces outils ont déjà été développés et open source. D’autres seront nets nouveaux.

Les API de santé Azure vous permettent d’effectuer les opérations suivantes : 
* Connectez rapidement des sources de données d’intégrité disparates et des formats tels que des données structurées, de création d’images et de périphériques, et normalisez-les pour les rendre persistantes dans le Cloud.
* Transformez et transformez les données en FHIR. Par exemple, vous pouvez transformer les données d’intégrité à partir de formats hérités, tels que HL7v2 ou CDA, ou à partir de données IoT à haute fréquence dans des formats propriétaires d’appareils vers FHIR.
* Connecter vos données stockées dans les api de santé avec des services dans l’écosystème Azure, tels que la Synapse et les produits Microsoft, comme Teams, afin d’obtenir de nouvelles informations grâce à l’analytique et à la Machine Learning, ainsi qu’à la connexion à des applications FHIR intelligentes.
* Gérez les charges de travail avancées avec des fonctionnalités d’entreprise qui offrent fiabilité, évolutivité et sécurité pour garantir la protection de vos données, ainsi que des certifications de confidentialité et de conformité requises pour le secteur de la santé.


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Quelles sont les principales différences entre les API de santé Azure et l’API Azure pour FHIR ?

**Services liés**

Les API de santé Azure prennent désormais en charge plusieurs normes de données d’intégrité pour l’échange de données structurées. une collection unique d’api Azure Healthcare vous permet de déployer plusieurs instances de différents types de services (service FHIR, service DICOM et IoT Connector) qui fonctionnent en toute transparence les unes avec les autres.

**Présentation du service DICOM**

Les API Azure Healthcare incluent désormais la prise en charge des services DICOM. DICOM permet l’échange sécurisé des données d’image et des métadonnées qui leur sont associées. DICOM est la norme internationale pour la transmission, le stockage, la récupération, l’impression, le traitement et l’affichage d’informations sur les images médicales. il s’agit de la norme principale d’imagerie médicale acceptée dans le secteur de la santé. Pour plus d’informations sur le service DICOM, consultez [vue d’ensemble de DICOM](./dicom/dicom-services-overview.md).

**Modifications incrémentielles apportées au service FHIR**

Pour l’échange sécurisé de données FHIR, les API de santé offrent quelques fonctionnalités incrémentielles disponibles dans l’API Azure pour FHIR. 
* Prise en charge des transactions : dans les API de santé, le service FHIR prend en charge les offres groupées de transactions. Pour plus d’informations sur les offres groupées de transactions, visitez HL7.org et reportez-vous aux interactions par lots/transactions.
* Améliorations de la recherche chaînée : la recherche chaînée & la recherche chaînée de réserve n’est plus limitée par 100 éléments par sous-requête.


## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser les API de santé Azure, suivez le démarrage rapide de 5 minutes pour déployer un espace de travail.

> [!div class="nextstepaction"]
> [Déployer l’espace de travail dans le Portail Azure](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [Vue d’ensemble de l’espace de travail](workspace-overview.md)
