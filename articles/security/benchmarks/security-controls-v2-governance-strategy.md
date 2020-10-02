---
title: Benchmark de sécurité Azure v2 - Gouvernance et stratégie
description: Benchmark de sécurité Azure v2, gouvernance et stratégie
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059026"
---
# <a name="security-control-governance-and-strategy"></a>Contrôle de sécurité : Gouvernance et stratégie

La sauvegarde et la récupération couvrent les contrôles pour s’assurer que les sauvegardes de données et de configuration des différents niveaux de service sont effectuées, validées et protégées.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1 : Définir la stratégie de gestion et de protection des actifs

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Veillez à documenter et à communiquer une stratégie claire pour la surveillance et la protection continues des systèmes et des données. Hiérarchisez la découverte, l’évaluation, la protection et la surveillance des données et systèmes critiques de l’entreprise. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Norme de classification des données en fonction des risques pour l’entreprise

-   Visibilité de l’organisation de sécurité sur les risques et l’inventaire des actifs 

-   Approbation de l’organisation de sécurité pour les services Azure en vue de leur utilisation 

-   Sécurité des ressources tout au long de leur cycle de vie

-   Stratégie de contrôle d’accès requise conformément à la classification des données organisationnelles

-   Utilisation des fonctionnalités de protection des données natives et tierces d’Azure

-   Exigences de chiffrement des données pour les cas d’utilisation en transit et au repos

-   Normes de chiffrement appropriées

Remarque : Votre approche de gestion et de protection des actifs dans le cloud et en local peut être différente en fonction de plusieurs facteurs, comme le modèle d’hébergement/service d’application, les risques commerciaux et les exigences de conformité. 

- [Recommandation d’architecture de sécurité Azure - Stockage, données et chiffrement](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Notions de base de la sécurité Azure - Sécurité, chiffrement et stockage des données Azure](../fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Meilleures pratiques en matière de chiffrement et de sécurité des données Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark de sécurité Azure - Gestion des ressources](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Benchmark de sécurité Azure - Protection des données](/azure/security/benchmarks/security-controls-v2-data-protection)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2 : Définir la stratégie de gestion de la posture de la sécurité

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

Mesurez et atténuez en permanence les risques liés à vos ressources individuelles et à l’environnement dans lequel elles sont hébergées. Priorisez les ressources à valeur élevée et les surfaces d’attaque hautement exposées, comme les applications publiées, les points d’entrée et de sortie du réseau, les points de terminaison utilisateur et administrateur, etc.

- [Benchmark de sécurité Azure - Gestion de la posture et des vulnérabilités](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3 : Aligner les rôles et les responsabilités de l’organisation

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-3 | N/A | PL, PM |

Veillez à documenter et à communiquer une stratégie claire pour les rôles et les responsabilités de votre organisation de sécurité. Veillez définir clairement les responsabilités pour les décisions de sécurité, l’éducation sur le modèle de responsabilité partagée et la formation technique pour la sécurité du cloud. 

- [Meilleures pratiques pour la sécurité Azure 1 – Personnes : Former les équipes pour le parcours vers la sécurité dans le cloud](https://aka.ms/AzSec1)

- [Meilleures pratiques pour la sécurité Azure 2 – Personnes : Former les équipes pour les technologies de sécurité dans le cloud](https://aka.ms/AzSec2)

- [Meilleures pratiques pour la sécurité Azure 3 – Processus : Affecter les responsabilités pour les décisions de sécurité dans le cloud](https://aka.ms/AzSec3)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4 : Définir la stratégie de sécurité réseau

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-4 | 9 | CA, SC |

Établissez une approche de sécurité réseau Azure dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Responsabilité centralisée pour la gestion et la sécurité du réseau

-   Modèle de segmentation de réseau virtuel aligné avec la stratégie de segmentation de l’entreprise

-   Stratégie de correction dans différents scénarios de menaces et d’attaques

-   Stratégie de périphérie d’Internet et d’entrée et de sortie

-   Stratégie de cloud hybride et d’interconnexion locale

-   Artefacts de sécurité réseau à jour (par exemple diagrammes réseau, architecture de réseau de référence)

Remarque : Votre approche de sécurité réseau pour le cloud et locale peut être différente en fonction de plusieurs facteurs, comme le modèle de service d’application, l’exposition aux menaces et la configuration de réseau hybride.

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](https://aka.ms/AzSec11)

- [Benchmark de sécurité Azure – Sécurité réseau](/azure/security/benchmarks/security-controls-v2-network-security)

- [Vue d’ensemble de la sécurité réseau d’Azure](../fundamentals/network-overview.md)

- [Stratégie d’architecture de réseau d’entreprise](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5 : Définir une stratégie d’accès privilégié et d’identité

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Établissez une approche d’identité Azure et d’accès privilégié dans le cadre de la stratégie de contrôle d’accès de sécurité globale de votre organisation.  

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Un système centralisé d’identité et d’authentification et son interconnexion avec d’autres systèmes d’identité internes et externes

-   Méthodes d’authentification fortes dans différents cas d’usage et différentes conditions

-   Protection des utilisateurs disposant de privilèges élevés

-   Surveillance et gestion des activités anormales des utilisateurs  

-   Vérification de l’identité et de l’accès des utilisateurs et processus de rapprochement

Remarque : Votre approche d’accès aux identités et aux privilèges pour le cloud et en local peut être différente en fonction de plusieurs facteurs, comme le chemin d’accès aux données/applications, le modèle de service et la stratégie d’accès client/partenaire.

- [Benchmark de sécurité Azure - Gestion des identités](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Benchmark de sécurité Azure - Accès privilégié](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Meilleures pratiques pour la sécurité Azure 11 – Architecture. Stratégie de sécurité unifiée unique](https://aka.ms/AzSec11)

- [Vue d’ensemble de la sécurité et de la gestion des identités Azure](../fundamentals/identity-management-overview.md) 

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6 : Définir la stratégie de journalisation et de réponse aux menaces

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Établissez une stratégie de journalisation et de réponse aux menaces pour détecter et corriger rapidement les menaces tout en répondant aux exigences de conformité. Veillez à ce que les analystes reçoivent des alertes de haute qualité et des expériences homogènes afin qu’ils puissent se concentrer sur les menaces plutôt que sur l’intégration et les étapes manuelles. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Rôle et responsabilités de l’organisation d’opérations de sécurité (SecOP) 

-   Un processus de réponse aux incidents bien défini, aligné avec NIST ou autre cadre réglementaire du secteur 

-   Capture et rétention des journaux pour prendre en charge la détection des menaces, la réponse aux incidents et les besoins de conformité

-   Visibilité centralisée des informations de corrélation sur les menaces, avec SIEM, les fonctionnalités Azure natives et d’autres sources 

-   Plan de communication et de notification avec vos clients, fournisseurs et les parties publiques pertinentes

-   Utilisation de plateformes Azure natives et tierces pour la gestion des incidents, comme la journalisation et la détection des menaces, les investigations et la correction et l’éradication des attaques

-   Processus de gestion des incidents et des activités postérieures aux incidents, comme les leçons apprises et la rétention des preuves

Remarque : Votre approche de journalisation et de détection des menaces pour le cloud et en local peut être différente en fonction de plusieurs facteurs, comme les exigences de conformité, le contexte des menaces, et les capacités de détection et correction. 

- [Benchmark de sécurité Azure - Journalisation et détection des menaces](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Benchmark de sécurité Azure - Réponse aux incidents](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Meilleures pratiques pour la sécurité Azure 4 - Processus. Mise à jour des processus de réponse aux incidents pour le cloud](https://aka.ms/AzSec11)

- [Guide pour le cadre d’adoption d’Azure, la journalisation et la prise de décision pour les rapports](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Mise à l’échelle, gestion et surveillance d’entreprise Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7 : Définir la stratégie de sauvegarde et de récupération

| Identifiant Azure | ID des contrôles CIS v7.1 | ID NIST SP800-53 r4 |
|--|--|--|--|
| GS-7 | 10 | CP |

Établissez une stratégie de sauvegarde et de récupération Azure pour votre organisation. 

Cette stratégie doit inclure les recommandations, stratégies et normes documentées pour les éléments suivants : 

-   Définitions des objectifs de temps de récupération (RTO) et de point de récupération (RPO) conformément aux objectifs de résilience de votre entreprise

-   Conception de la redondance dans vos applications et configuration de l’infrastructure

-   Protection de la sauvegarde à l’aide du contrôle d’accès et du chiffrement des données

Remarque : Votre approche de sauvegarde et de récupération pour le cloud et en local peut être différente en fonction de plusieurs facteurs, comme la redondance de l’infrastructure, le modèle d’hébergement/service d’application et les exigences de conformité.

- [Benchmark de sécurité Azure - Sauvegarde et récupération](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Azure Well-Architecture Framework - Sauvegarde et récupération d’urgence pour les applications Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Cadre d’adoption d’Azure - Continuité d'activité et reprise d'activité](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilité** : Customer

**Parties prenantes de la sécurité des clients** :

- [Toutes les parties prenantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

