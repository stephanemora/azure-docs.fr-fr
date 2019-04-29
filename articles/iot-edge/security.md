---
title: Infrastructure de sécurité - Azure IoT Edge | Microsoft Docs
description: Découvrez les normes de sécurité, d’authentification et d’autorisation qui ont été utilisées pour développer Azure IoT Edge et que vous devriez prendre en compte pour concevoir votre solution
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8aadddbc9ae13a87f89db4d7e7189ea7aa8aeef5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612024"
---
# <a name="security-standards-for-azure-iot-edge"></a>Normes de sécurité pour Azure IoT Edge

Azure IoT Edge est conçu pour résoudre les scénarios à risque qui sont intégrées lors du déplacement de vos données et l’analytique vers la périphérie intelligente. Les normes de sécurité IoT Edge fournissent la flexibilité pour les scénarios de déploiement et les différents profils de risque tout en offrant la protection que vous attendez de tous les services Azure. 

Azure IoT Edge s’exécute sur plusieurs modèles de composants matériels issus de différents fabricants, il prend en charge plusieurs systèmes d’exploitation et il s’applique à différents scénarios de déploiement. L’évaluation des risques liés à un scénario de déploiement donné dépend de nombreux facteurs, comme la propriété de la solution, la géographie du déploiement, la sensibilité des données, la confidentialité, la verticalité des applications et les conditions de réglementation. Plutôt que d’offrir des solutions concrètes pour des scénarios spécifiques, IoT Edge est une infrastructure de sécurité extensible basée sur des principes concrets et conçus pour la mise à l’échelle. 
 
Cet article fournit une vue d’ensemble du framework de sécurité IoT Edge. Pour plus d’informations, consultez [Securing the intelligent edge (Sécurisation de la périphérie intelligente)](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standards

Les standards facilitent l’examen et l’implémentation, qui sont des éléments clés de la sécurité. Une solution de sécurité doit pouvoir être examinée et évaluée afin de gagner la confiance des utilisateurs, et ne doit pas être un obstacle au déploiement. La conception du framework permettant de sécuriser Azure IoT Edge est basée sur des protocoles de sécurité testés visant à vous familiariser avec elle et à l’utiliser plus facilement. 

## <a name="authentication"></a>Authentification

Lorsque vous déployez une solution IoT, vous devez savoir que seuls les acteurs fiables, les appareils et les modules ont accès à votre solution. Ces connaissances offrent une responsabilité sécurisée des participants. Azure IoT Edge obtient ces connaissances grâce à l’authentification. L’authentification basée sur le certificat est le mécanisme principal pour l’authentification pour la plateforme Azure IoT Edge. Ce mécanisme dérive d’un ensemble de normes de l’IETF (Internet Engineering Task Force) régissant l’infrastructure à clé publique (PKiX).     

Tous les appareils, modules et acteurs qui interagissent avec l’appareil Azure IoT Edge, que ce soit physiquement ou via une connexion réseau, doivent avoir des identités de certificat uniques. Toutefois, pas tous les scénarios ou composant ne se prête à l’authentification par certificat. Dans ces scénarios, l’extensibilité du framework de sécurité propose des alternatives sécurisées. 

## <a name="authorization"></a>Authorization

Selon le principe des privilèges minimum, les utilisateurs et les composants d’un système doivent uniquement avoir accès à un ensemble minimal de ressources et aux données dont ils ont besoin pour exécuter leurs rôles. Les appareils, modules et acteurs doivent uniquement accéder aux ressources et aux données qui se trouvent dans leur étendue d’autorisations, et uniquement quand l’architecture le permet. Certaines autorisations sont configurables avec des privilèges suffisants, et d’autres sont appliquées de manière architecturale.  Par exemple, un module peut être autorisé à établir une connexion à Azure IoT Hub au moyen d’une configuration privilégiée. Toutefois, il n’y a aucune raison pour qu’un module d’un appareil Azure IoT Edge doive accéder au jumeau d’un module sur un autre appareil Azure IoT Edge.

Autres schémas d’autorisation incluent les droits de signature de certificat et de contrôle d’accès en fonction du rôle (RBAC). 

## <a name="attestation"></a>Attestation

L’attestation garantit l’intégrité des éléments logiciels.  Elle est importante pour la détection et la prévention des programmes malveillants.  L’infrastructure de sécurité Azure IoT Edge classe l’attestation dans trois catégories principales :

* Attestation statique
* Attestation d’exécution
* Attestation logicielle

### <a name="static-attestation"></a>Attestation statique

L’attestation statique vérifie l’intégrité de tous les logiciels présents sur l’appareil, notamment le système d’exploitation, tous les runtimes ainsi que les informations de configuration au moment de la mise sous tension de l’appareil. On emploie souvent le terme de « démarrage sécurisé ». Le framework de sécurité des appareils Azure IoT Edge s’étend aux fabricants et incorpore des fonctionnalités de sécurité aux composants matériels afin de garantir l’efficacité des processus d’attestation statique. Ces processus incluent le démarrage sécurisé et la mise à niveau sécurisée du microprogramme.  Le fait de collaborer étroitement avec les fournisseurs de silicium permet d’éliminer les couches de microprogramme superflues, réduisant ainsi la surface d’attaque. 

### <a name="runtime-attestation"></a>Attestation d’exécution

Une fois qu’un système a démarré de façon sécurisée et qu’il est opérationnel, les systèmes bien conçus détectent les tentatives d’injection de logiciels malveillants et prennent les contre-mesures appropriées. Attaques de programmes malveillants peuvent cibler des ports et des interfaces pour accéder au système du système. En outre, si des personnes malveillantes ont un accès physique à un appareil, elles peuvent falsifier son contenu ou faire usage d’attaques par canal auxiliaire pour y accéder. Ce contenu malveillant, ce qui peut être sous la forme de logiciels malveillants ou des modifications de configuration non autorisés, ne peut pas être détecté par attestation statique, car il est injecté après le processus de démarrage. Les contre-mesures proposées ou appliquées par les composants matériels de l’appareil permettent d’atténuer ces menaces.  Le framework de sécurité d’Azure IoT Edge appelle explicitement des extensions afin de combattre les menaces au moment de l’exécution.  

### <a name="software-attestation"></a>Attestation logicielle

Tous les systèmes sains, y compris les systèmes de périphérie intelligente, doivent accepter des correctifs et mises à niveau.  La sécurité est importante pour les processus de mise à jour, car ceux-ci peuvent constituer des vecteurs de menaces potentiels.  L’infrastructure de sécurité pour les appels d’Azure IoT Edge pour les mises à jour via packages mesurés et signés pour garantir l’intégrité des et d’authentifier la source des packages.  Ce standard s’applique à tous les systèmes d’exploitation et logiciels d’application. 

## <a name="hardware-root-of-trust"></a>Racine matérielle de confiance

Pour de nombreux appareils de périphérie intelligente, en particulier ceux qui sont situés là où des acteurs malveillants peuvent avoir un accès physique à l’appareil, la sécurité fournie par les composants matériels de l’appareil constitue la dernière défense pour la protection. Pour de tels déploiements, il est essentiel de disposer de composants matériels inviolables. Azure IoT Edge encourage les fournisseurs de composants matériels en silicium sécurisés à proposer différentes versions de racine matérielle de confiance afin de prendre en charge différents scénarios de déploiement et profils de risque. La confiance accordée aux composants matériels est garantie par les standards de protocole de sécurité courants tels que le Trusted Platform Module (ISO/IEC 11889) et le Device Identifier Composition Engine (DICE) du Trusted Computing Group. Les technologies intégrées et sécurisées comme TrustZones et Software Guard Extensions (SGX) permettent également aux composants matériels d’être considérés comme étant de confiance. 

## <a name="certification"></a>Certification

Pour aider les clients à prendre des décisions informées lors de l’approvisionnement des appareils Azure IoT Edge pour leur déploiement, l’infrastructure Azure IoT Edge inclut des exigences de certification.  À la base de ces exigences figurent des certifications relatives aux revendications de sécurité et des certifications relatives à la validation de l’implémentation de sécurité.  Par exemple, une certification de revendication de sécurité signalerait que l’appareil IoT Edge utilise du matériel sécurisé connu comme résistant aux attaques de démarrage. Un certificat de validation signalerait que le matériel sécurisé a été correctement implémenté pour offrir cette fonctionnalité dans l’appareil.  Conformément au principe de simplicité, le framework vise à réduire au maximum la charge de certification.   

## <a name="extensibility"></a>Extensibilité

Étant donné que la technologie IoT pilote différents types de transformations professionnelles, il est évident que la sécurité doit évoluer en parallèle afin de gérer les scénarios émergents.  L’infrastructure de sécurité Azure IoT Edge repose sur une base solide et extensible dans différentes dimensions de façon à inclure ce qui suit : 

* Services de sécurité internes tels que le service Device Provisioning pour Azure IoT Hub.
* Services tiers tels que des services de sécurité gérés pour différents domaines d’application (comme industrie ou soins de santé) ou focus technologiques (par exemple la surveillance de la sécurité sur les réseaux à maillage ou les services d’attestation matérielle silicone) par le biais d’un réseau complet de partenaires.
* Systèmes hérités devant inclure la récupération avec des stratégies de sécurité de substitution, comme l’utilisation d’une technologie sécurisée autre que les certificats pour la gestion de l’authentification et de l’identité.
* Matériel sécurisé pour l’adoption des nouvelles technologies matérielles sécurisées et des contributions des fournisseurs de silicium.

Au final, la réussite de la sécurisation de la périphérie intelligente résulte des contributions d’une communauté ouverte motivée par l’intérêt commun envers la sécurisation d’IoT.  Ces contributions peuvent se présenter sous la forme de technologies sécurisées ou de services.  L’infrastructure de sécurité Azure IoT Edge offre une base solide pour la sécurité, qui est extensible afin de fournir une couverture maximale, et permet d’offrir le même niveau de confiance et d’intégrité dans la périphérie intelligente que le cloud Azure.  

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la façon dont Azure IoT Edge [sécurise la périphérie intelligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).
