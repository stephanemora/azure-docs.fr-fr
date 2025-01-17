---
title: 'Azure Defender pour les serveurs : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour les serveurs.
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1a7b5d1e7ed3c4c1de2c535fb126b67f3ffadecd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711714"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Présentation d’Azure Defender pour les serveurs

Azure Defender pour les serveurs ajoute la détection des menaces et des défenses avancées pour vos machines Windows et Linux.

Sous Windows, Azure Defender s’intègre aux services Azure pour superviser et protéger vos machines Windows. Security Center présente les alertes et les suggestions de correction de l’ensemble de ces services dans un format facile à utiliser.

Sous Linux, Azure Defender collecte les enregistrements d’audit à partir des machines Linux à l’aide d’auditd, l’un des frameworks d’audit Linux les plus courants.


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Quels sont les avantages d’Azure Defender pour les serveurs ?

Les fonctionnalités de détection et de protection des menaces fournies avec Azure Defender pour les serveurs sont les suivantes :

- **Licence intégrée pour Microsoft Defender pour point de terminaison** : Azure Defender pour les serveurs comprend [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Ensemble, ils offrent des fonctionnalités EDR (protection évolutive des points de terminaison) complètes. Pour plus d’informations, consultez [Protéger vos points de terminaison](security-center-wdatp.md).

    Quand Microsoft Defender pour point de terminaison détecte une menace, il déclenche une alerte. L’alerte s’affiche dans Security Center. À partir de Security Center, vous pouvez également accéder à la console Microsoft Defender pour point de terminaison et effectuer un examen détaillé pour découvrir l’étendue de l’attaque. Découvrez-en plus sur Microsoft Defender pour point de terminaison.

    > [!IMPORTANT]
    > L’intégration de Security Center à Microsoft Defender pour point de terminaison est activée par défaut. Ainsi, quand vous activez Azure Defender, vous consentez à ce qu’Azure Defender pour les serveurs accède aux données Microsoft Defender pour point de terminaison liées aux vulnérabilités, aux logiciels installés et aux alertes de vos points de terminaison.
    >
    > Nous proposons actuellement le capteur pour les machines Linux en préversion. En savoir plus dans [Protéger vos points de terminaison avec la solution EDR intégrée de Security Center : Microsoft Defender for Endpoint](security-center-wdatp.md).

- **Outils d’évaluation des vulnérabilités pour les machines** - Azure Defender pour les serveurs comprend un choix d’outils de découverte et de gestion des vulnérabilités pour vos machines. Dans les pages de paramètres de Security Center, vous pouvez sélectionner les outils à déployer sur vos machines. Les vulnérabilités découvertes s’affichent alors au sein d’une recommandation de sécurité.

    - **Gestion des menaces et des vulnérabilités Microsoft** - Découvrez les vulnérabilités et les erreurs de configuration en temps réel avec Microsoft Defender pour point de terminaison, sans avoir besoin d’agents supplémentaires ou d’analyses périodiques. La fonctionnalité [Gestion des menaces et des vulnérabilités](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt) hiérarchise les vulnérabilités en fonction du paysage des menaces, des détections effectuées dans votre organisation, des informations sensibles sur les appareils vulnérables et du contexte métier. Découvrez davantage d’informations dans [Investiguer les faiblesses avec la gestion des menaces et des vulnérabilités de Microsoft Defender pour point de terminaison](deploy-vulnerability-assessment-tvm.md)

    - **Analyseur de vulnérabilité avec Qualys** - L’analyseur de Qualys est l’un des principaux outils d’identification en temps réel des vulnérabilités de vos machines virtuelles Azure et hybrides. Vous n’avez pas besoin d’une licence Qualys ni même de compte Qualys : tout est traité de manière fluide dans Security Center. Découvrez davantage d’informations dans [Analyseur Qualys intégré d’Azure Defender pour les machines Azure et hybrides](deploy-vulnerability-assessment-vm.md).

- **Accès juste-à-temps (JAT) des machines virtuelles** : les acteurs des menaces repèrent activement les machines accessibles avec des ports de gestion ouverts, par exemple RDP ou SSH. Toutes vos machines virtuelles sont des cibles potentielles pour une attaque. Lorsqu’une machine virtuelle est compromise, elle est utilisée comme point d’entrée pour attaquer d’autres ressources au sein de votre environnement.

    Lorsque vous activez Azure Defender pour les serveurs, vous pouvez utiliser l’accès juste-à-temps aux machines virtuelles pour verrouiller le trafic entrant vers vos machines virtuelles, ce qui réduit l’exposition aux attaques et facilite la connexion aux machines virtuelles en cas de besoin. Pour plus d’informations, consultez [Fonctionnement de l’accès aux machines virtuelles juste-à-temps](just-in-time-explained.md).

- **Monitoring d’intégrité de fichier (FIM)**  : le Monitoring d’intégrité de fichier (FIM), également appelé Monitoring des modifications, recherche les modifications qui sont apportées aux fichiers et registres du système d’exploitation, ainsi qu’aux logiciels d’application et autres systèmes, et qui peuvent indiquer une attaque. Une méthode de comparaison est utilisée pour déterminer si l’état actuel du fichier est différent de la dernière analyse du fichier. Vous pouvez utiliser cette comparaison pour vérifier si des modifications suspectes ou valides ont été apportées à vos fichiers.

    Lorsque vous activez Azure Defender pour les serveurs, vous pouvez utiliser FIM pour valider l’intégrité des fichiers Windows, des registres Windows et des fichiers Linux. Pour plus d’informations, consultez [Monitoring d’intégrité de fichier dans Azure Security Center](security-center-file-integrity-monitoring.md).

- **Contrôles d’application adaptatifs (AAC)**  les contrôles d’application adaptatifs sont une solution intelligente et automatisée permettant la définition de listes vertes d’applications réputées sécurisées pour vos ordinateurs.

    Lorsque vous avez activé et configuré des contrôles d’application adaptatifs, vous obtenez des alertes de sécurité si une autre application que celles que vous avez définies comme sécurisées s’exécute. Pour plus d’informations, consultez [Utiliser des contrôles d’application adaptatifs pour réduire les surfaces d’attaque de vos machines](security-center-adaptive-application.md).

- **Sécurisation adaptative du réseau (ANH)**  : l’application de groupes de sécurité réseau (NSG) pour filtrer le trafic vers et depuis des ressources améliore votre posture de sécurité réseau. Il peut toutefois rester des cas dans lesquels le trafic réel qui transite via le groupe de sécurité réseau est un sous-ensemble des règles NSG définies. Dans ces cas, une amélioration supplémentaire de la posture de sécurité est possible en renforçant les règles NSG en fonction des modèles de trafic réel.

    Le renforcement du réseau adaptatif fournit des suggestions visant à renforcer encore les règles NSG. Il utilise un algorithme de Machine Learning factorisé dans le trafic réel, appelé une configuration approuvée, l’intelligence des menaces et d’autres indicateurs de compromis, puis fournit des suggestions pour autoriser uniquement le trafic provenant de tuples IP/port spécifiques. Pour plus d’informations, consultez [Améliorer votre posture de sécurité réseau avec le renforcement du réseau adaptatif](security-center-adaptive-network-hardening.md).


- **Sécurisation de l’hôte Docker** : Azure Security Center identifie les conteneurs non managés qui sont hébergés sur des machines virtuelles IaaS Linux, ou d’autres machines Linux exécutant des conteneurs Docker. Security Center évalue en continu les configurations de ces conteneurs. Il les compare ensuite au document de référence Center for Internet Security (CIS) Docker Benchmark. Security Center inclut la totalité des règles définies dans le CIS Docker Benchmark et vous envoie une alerte si vos conteneurs ne satisfont pas à tous les contrôles. Pour plus d’informations, consultez [Renforcer vos hôtes Docker](harden-docker-hosts.md).

- **Détection d’attaques sans fichier** : les attaques sans fichier injectent des charges utiles malveillantes en mémoire pour ne pas être détectées par les techniques d’analyse sur disque. La charge utile de l’attaquant est alors conservée dans la mémoire des processus compromis et effectue un large éventail d’activités malveillantes.

  Avec la détection des attaques sans fichier, les techniques d’investigation automatique de la mémoire identifient les comportements, les techniques et les kits de ressources des attaques sans fichier. Cette solution analyse régulièrement votre machine au moment de l’exécution et extrait des insights directement de la mémoire des processus. Des insights spécifiques incluent l’identification des éléments suivants : 

  - Trousses à outils et logiciels d’exploration de données de chiffrement connus 

  - Code d’interpréteur de commandes, qui est un petit morceau de code généralement utilisé comme charge utile dans l’exploitation d’une vulnérabilité logicielle

  - Injection d’un exécutable malveillant dans la mémoire du processus

  La détection d’attaques sans fichier génère des alertes de sécurité détaillées contenant les descriptions accompagnées des métadonnées de processus, telles que l’activité réseau. Ces détails accélèrent le triage des alertes, la corrélation et le temps de réponse en aval. Cette approche complète les solutions EDR basées sur les événements et offre une couverture de détection accrue.

  Pour obtenir des détails sur les alertes de détection des attaques sans fichier, consultez la [table de référence des alertes](alerts-reference.md#alerts-windows).

- **Intégration des alertes auditd Linux et de l’agent Log Analytics (Linux uniquement)**  : le système auditd se compose d’un sous-système au niveau du noyau, qui est responsable de la supervision des appels système. Il filtre les appels par un ensemble de règles donné et écrit les messages pour ces derniers dans un socket. Security Center intègre les fonctionnalités du package auditd à l’agent Log Analytics. Cette intégration permet la collecte des événements auditd dans toutes les distributions Linux prises en charge, sans prérequis.

    L’agent Log Analytics pour Linux collecte les enregistrements d’audit, les enrichit et les agrège dans des événements. Security Center enrichit continuellement l’analytique, qui utilise les signaux Linux pour détecter les comportements malveillants sur les machines Linux locales ou dans le cloud. À l’image des fonctionnalités Windows, cette analytique englobe les processus suspects, les tentatives de connexion douteuses, le chargement de modules de noyau et diverses autres activités. Ces activités peuvent signaler qu’une machine fait l’objet d’une attaque ou qu’elle a subi une violation de la sécurité.  

    Pour obtenir la liste des alertes Linux, consultez la [table de référence des alertes](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulation d’alertes

Vous pouvez simuler des alertes en téléchargeant l’un des playbooks suivants :

- Pour Windows : [Playbook Azure Security Center : alertes de sécurité](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Pour Linux : [Playbook Azure Security Center : Détections Linux](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Azure Defender pour les serveurs. 

> [!div class="nextstepaction"]
> [Activer Azure Defender](enable-azure-defender.md)

Pour obtenir des informations connexes, consultez la page suivante :

- Qu’une alerte soit générée par Security Center ou reçue par Security Center à partir d’un autre produit de sécurité, vous pouvez l’exporter. Pour exporter vos alertes vers Azure Sentinel (ou un système SIEM tiers) ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers SIEM](continuous-export.md).