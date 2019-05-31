---
title: Pourquoi les stations de travail sécurisées sont importantes - Azure Active Directory
description: Découvrez pourquoi les organisations doivent créer des stations de travail gérés par Azure sécurisées
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357047"
---
# <a name="building-secure-workstations"></a>Création des stations de travail sécurisées

Stations de travail isolées sécurisées sont extrêmement importantes pour la sécurité des rôles sensibles telles que les administrateurs, développeurs et opérateurs de services critiques. Nombreux autres contrôles de sécurité et des assurances échoueront ou n’ont aucun effet si la sécurité de station de travail client sous-jacent a été compromise.

Ce document explique ce qu’il faut pour créer une station de travail cliente sécurisée avec des instructions étape par étape détaillées, y compris la configuration à partir de contrôles de sécurité. Ce type de stations de travail dans certains cas est appelé une accès privilégié station de travail (PAW), qui cette référence est utilisée et exploite. Le Guide toutefois ressemble à la technologie cloud pour gérer le service et présente les fonctionnalités de sécurité introduites à compter de Windows 10RS5, Microsoft Defender ATP, Azure Active Directory et Intune.

## <a name="why-securing-workstation-access-is-important"></a>Pourquoi il est important de sécuriser l’accès de la station de travail

L’adoption rapide des services cloud et la possibilité de travailler à partir de n’importe quel endroit a créé une nouvelle méthode pour l’exploitation. Les pirates exploitent les contrôles de sécurité faible sur les appareils où les administrateurs fonctionnent et sont en mesure d’accéder à des ressources privilégiées.

Comme expliqué dans la [rapport sur les menaces Verizon](https://enterprise.verizon.com/resources/reports/dbir/), et [Security Intelligence Report](https://aka.ms/sir) abuser des privilèges, et attaques de chaîne d’approvisionnement sont parmi les mécanismes de cinq principaux utilisés pour une violation des organisations et le Deuxièmement couramment a détecté tactique dans incidents rapportés en 2018.

La plupart des intrus suivez le chemin d’accès ci-dessous :

* Démarrer avec la reconnaissance, souvent spécifique à un secteur, pour trouver un moyen dans
* Analyser les informations collectées pour identifier le meilleur moyen d’y accéder (Infiltration) d’une station de travail de faible valeur perçue
* Persistance et le coup de œil au moyen de déplacer [latéralement](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Exfiltrer des données confidentielles et sensibles

Les attaquants infiltrer fréquemment des appareils qui semblent risque faible ou sous-évaluées pour la reconnaissance. Ces appareils vulnérables sont ensuite utilisés pour localiser une opportunité d’un mouvement latéral, recherchez les utilisateurs administratifs et les appareils et identifiez les données à valeurs haute, avec succès les informations une fois qu’ils obtiennent ces rôles d’utilisateur privilégié d’exfiltrer.

![Modèle de compromission classique](./media/concept-azure-managed-workstation/typical-timeline.png)

Ce document fournit une solution pour vous aider à protéger vos appareils informatiques en isolant les services de gestion et pour vous protéger contre le mouvement latéral ou attaques à partir d’appareils de productivité moins intéressantes. La conception permet de réduire la capacité d’exécuter correctement une violation en divisant la chaîne avant d’infiltration de l’appareil utilisé pour gérer ou accéder aux ressources de cloud sensibles. La solution décrite utilisera les services Azure natifs qui font partie de la pile Microsoft 365 entreprise, notamment :

* Intune pour la gestion des appareils, y compris l’application et la liste verte d’URL
* AutoPilot pour le programme d’installation de périphérique et le déploiement et actualisation 
* Azure AD pour la gestion des utilisateurs, l’accès conditionnel et l’authentification multifacteur
* Windows 10 (version actuelle) pour l’appareil d’intégrité d’attestation et l’expérience utilisateur
* Microsoft Defender Advanced Threat Protection (ATP) pour la protection de point de terminaison, de détection et de réponse avec la gestion de cloud
* Azure AD PIM pour la gestion des autorisations, y compris de temps (JIT) un accès privilégié aux ressources

## <a name="who-benefit-from-using-a-secure-workstation"></a>Qui bénéficient à l’aide d’une station de travail sécurisée

Tous les utilisateurs et les opérateurs en bénéficier à l’aide d’une station de travail sécurisée. Une personne malveillante qui compromet un PC ou appareil peut faire plusieurs choses, y compris emprunter l’identité de tous les comptes en cache et les informations d’identification de l’utilisation et jetons utilisés sur l’appareil quand ils sont connectés. Ce risque permet de sécuriser les appareils utilisés pour n’importe quel rôle privilégié, y compris les droits d’administration si importants comme des appareils où un compte privilégié est utilisé sont des cibles de mouvement latéral et les attaques par élévation de privilège. Ces comptes peuvent être utilisés pour un large éventail de ressources telles que :

* Administrateurs de systèmes basés sur le cloud et locaux
* Stations de travail de développeur pour les systèmes critiques
* Administrateur des comptes sociaux avec exposition élevé
* Stations de travail très sensibles tels que les terminaux de paiement SWIFT
* Gestion des secrets de fabrication des stations de travail

Microsoft recommande l’implémentation des contrôles de sécurité avec élévation de privilèges pour les stations de travail privilégiées où ces comptes sont utilisés pour réduire les risques. Vous trouverez des conseils supplémentaires dans le [guide de déploiement des fonctionnalités Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [feuille de route de Office 365](https://aka.ms/o365secroadmap), et [feuille de route de sécurisation de l’accès privilégié](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Pourquoi dédié de stations de travail

Bien qu’il soit possible d’ajouter de sécurité à un périphérique existant, il est préférable de commencer avec une base solide. En commençant par un périphérique correct et un ensemble de place des contrôles de sécurité connus votre organisation dans la mieux placée pour mettre à jour qui a augmenté au niveau de sécurité. Avec le nombre croissant de vecteurs d’attaque autorisée par e-mail informel et la navigation web, il est plus en plus difficile garantir qu'un appareil est digne de confiance. Ce fonctionnement guide en supposant une station de travail dédiée séparé standard productivité, de navigation, et des tâches sont terminées. Suppression de productivité, de navigation sur le web et de messagerie à partir d’un appareil peut avoir un impact négatif sur la productivité, mais cette sécurité est généralement acceptable pour les scénarios où les tâches de travail ne nécessitent pas explicitement il et risque d’un incident de sécurité est élevé.

> [!NOTE]
> Navigation sur le Web ici fait référence à l’accès général aux sites Web arbitraires, ce qui est un risque élevé distincte de l’aide d’un navigateur web pour accéder à un petit nombre de bien connus sites Web d’administration des services comme Azure, Office 365, autres fournisseurs de services cloud et SaaS applications.

Stratégies de relation contenant-contenu fournissent des garanties de sécurité accrue en augmentant le nombre et le type de contrôle qu'un adversaire a à surmonter afin d’accéder aux ressources sensibles. Le modèle de développé ici fournit la relation contenant-contenu des privilèges administratifs à des appareils spécifiques à l’aide d’un modèle à plusieurs niveaux de privilège.

## <a name="supply-chain-management"></a>Gestion de la chaîne logistique

Essentielle pour une station de travail sécurisée est une solution de chaîne d’approvisionnement dans lequel la station de travail que vous utilisez est approuvée, une « racine de confiance ». Cette solution répondront à la racine de confiance à l’aide de la [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) technologie. Pour une station de travail sécurisée Microsoft Autopilot offre la possibilité de tirer parti des appareils Microsoft optimisé OEM Windows 10 qui fournissent un état correct connu auprès du fabricant. Au lieu de la réinitialisation d’un appareil qui n’est peut-être pas fiable, Microsoft Autopilot peuvent transformer un appareil Windows dans un état « prêt à l’emploi », appliquer les paramètres et les stratégies, installation d’applications, et même modifier l’édition de Windows 10 utilisé (par exemple, à partir de Windows 10 Professionnel vers Windows 10 entreprise, pour prendre en charge des fonctionnalités avancées).

![Niveaux de station de travail sécurisée](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Profils et les rôles de l’appareil

Dans le Guide, plusieurs rôles et les profils de sécurité seront traités pour obtenir une solution plus sécurisée pour les utilisateurs, les développeurs et le personnel d’exploitation. Ces profils ont été alignés pour prendre en charge les utilisateurs courants dans les organisations qui peuvent bénéficier d’une station de travail améliorée ou sécurisée, tout en équilibrant la facilité d’utilisation et les risques. Les instructions fournira la configuration des paramètres selon les normes du secteur accepté. Ce guide sert à illustrer une méthode dans la sécurisation renforcée de Windows 10 et en réduisant les risques liés à la compromission d’appareil ou un utilisateur à l’aide de la stratégie et technologie pour aider à gérer les risques et les fonctionnalités de sécurité.
![Niveaux de station de travail sécurisée](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Faible sécurité** – une station de travail standard gérée fournit un bon point de départ pour la plupart des particuliers et petites entreprises utilisent. Ces appareils sont inscrits à Azure AD et gérés par Intune. Le profil autorise les utilisateurs à exécuter toutes les applications et de parcourir un site Web. Une solution anti-programme malveillant comme [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) doit être activée.
* **Sécurité renforcée** – est une solution d’entrée de gamme protégée, valable pour les utilisateurs à domicile, les utilisateurs de petites entreprises, ainsi que les développeurs générales.
   * La station de travail amélioré fournit une stratégie basée les moyens d’améliorer la sécurité du profil de sécurité de faible. Ce profil permet un moyen sécurisé pour travailler avec les données client et être en mesure d’utiliser les outils de productivité tels que la vérification de la messagerie et la navigation sur le web. Une station de travail renforcée peut servir à auditer le comportement de l’utilisateur et l’utilisation de profil d’une station de travail par l’activation de stratégies d’audit et la journalisation à Intune. Dans ce profil, la station de travail permettra de contrôles de sécurité et de stratégies décrites dans le contenu déploiement dans l’améliorée station de travail - Windows 10 (1809) script. Le déploiement tire également parti de l’utilisation de protection avancée contre les programmes malveillants [Advanced Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Haute sécurité** – le moyen le plus efficace pour réduire la surface d’attaque d’une station de travail consiste à supprimer la possibilité d’administrer la station de travail. Suppression des droits d’administrateur local est une étape qui améliore la sécurité et peut avoir un impact sur la productivité si implémentée de manière incorrecte. Le profil de sécurité élevée s’appuie sur le profil de sécurité renforcée avec un changement considérable, la suppression de l’administrateur local. Ce profil est conçu pour aider les utilisateurs qui peut-être un profil high comme un responsable ou les utilisateurs qui peuvent avoir à contacter avec des données sensibles telles que la paie, ou l’approbation des services et processus.
   * Le profil d’utilisateur de haute sécurité exige un environnement contrôlé plus élevé tout en étant en mesure d’effectuer leur activité de productivité, telles que la messagerie et tout en conservant un simple à utiliser l’expérience de navigation web. Les utilisateurs attendent des fonctionnalités telles que les cookies, les Favoris et les autres raccourcis disponibles pour fonctionner. Toutefois ces utilisateurs ne peuvent pas nécessiter la possibilité de modifier ou de déboguer son appareil et ne seront pas nécessaire d’installer des pilotes. Le profil de sécurité élevée est déployé à l’aide de la sécurité haute - script de Windows 10 (1809).
* **Spécialisé** – les développeurs et les administrateurs informatiques sont une cible attrayante pour les attaquants, comme ces rôles peuvent modifier des systèmes présentant un intérêt pour les attaquants. La station de travail spécialisée prend l’effort déployé dans la station de travail haute sécurité, diversité supplémentaire à risque tels qu’ActiveX, sa sécurité par la gestion des applications locales, en limitant les sites web internet et en limitant les fonctionnalités de productivité sont élevées Java, navigateur plug-in et plusieurs autres contrôles d’un risque élevé sur un appareil Windows. Dans ce profil, la station de travail permettra de contrôles de sécurité et de stratégies décrites dans le contenu déploiement dans le DeviceConfiguration_NCSC - Windows 10 (1803) SecurityBaseline script.
* **Sécurisé** – un attaquant qui peut compromettre un compte d’administrateur peut généralement endommager considérable par vol de données, l’altération de données ou interruption de service. Dans cet état de sécurité renforcé, la station de travail permettra à tous les contrôles de sécurité et les stratégies qui limitent le contrôle direct de gestion des applications locales et les outils de productivité sont supprimés. Par conséquent, compromettre l’appareil est effectuée plus difficile en tant que courrier et sociaux est bloqués qui reflètent la façon la plus courante les attaques par hameçonnage peuvent réussir.  La station de travail sécurisée peut être déployée avec la station de travail sécurisée - script de Windows 10 (1809) SecurityBaseline.

   ![Station de travail sécurisée](./media/concept-azure-managed-workstation/secure-workstation.png)

   Une station de travail sécurisée fournit une station de travail renforcée qui a le contrôle d’application clair et protection d’application à un administrateur. La station de travail utilisera les informations d’identification, appareil et exploit guard pour protéger l’hôte contre les comportements malveillants. En outre local de tous les disques est chiffré avec le chiffrement Bitlocker.

* **Isolé** – ce scénario hors connexion personnalisé représente l’extrémité du spectre (aucun script d’installation n’est fournies dans ce cas). Les organisations peuvent doivent gérer une isolé critiques fonction métier telle qu’une ligne de production de valeur élevée ou des systèmes de prise en charge de durée de vie qui nécessite des systèmes d’exploitation non pris en charge/unpatched. Étant donné que la sécurité est essentielle et services cloud ne sont pas disponibles, les organisations peuvent gérer/mettre à jour ces ordinateurs soit manuellement utilisent une architecture de forêt Active Directory (comme le Enhanced Security Admin Environment (ESAE)) isolée pour les gérer. Dans ces cas de suppression de tous les accès à l’exception de base Intune et ATP la vérification d’intégrité doit être considérée.
   * [Exigence de communications réseau Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Exigence de communications réseau ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Étapes suivantes

[Déploiement d’une station de travail sécurisée gérés par Azure](howto-azure-managed-workstation.md)
