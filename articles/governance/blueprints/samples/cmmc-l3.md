---
title: Exemple de blueprint CMMC niveau 3
description: Vue d’ensemble de l’exemple de blueprint CMMC niveau 3 Cet exemple de blueprint aide les clients à évaluer des contrôles spécifiques.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572112"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Exemple de blueprint CMMC niveau 3

L’exemple de blueprint CMMC niveau 3 fournit des garde-fous de gouvernance avec [Azure Policy](../../policy/overview.md) qui vous aident à évaluer des contrôles spécifiques du [framework CMMC (Cybersecurity Maturity Model Certification)](https://www.acq.osd.mil/cmmc/index.html). Avec ce blueprint, les clients peuvent déployer un ensemble de stratégies pour toute architecture déployée par Azure devant implémenter des contrôles pour CMMC niveau 3.

## <a name="control-mapping"></a>Correspondance des contrôles

Le [mappage de contrôles Azure Policy](../../policy/samples/cmmc-l3.md) fournit des détails sur les définitions de stratégie incluses dans ce blueprint et sur la façon dont ces définitions de stratégie sont mappées aux **contrôles** dans le framework CMMC. Quand des ressources sont attribuées à une architecture, elles sont évaluées par Azure Policy pour savoir si elles sont en conformité avec les stratégies affectées. Pour plus d’informations, consultez [Présentation d’Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Déployer

Pour déployer l’exemple de blueprint CMMC niveau 3 pour Azure Blueprints, vous devez effectuer les étapes suivantes :

> [!div class="checklist"]
> - Créer un blueprint à partir de l’exemple
> - Marquer la copie de l’exemple en tant que **Publié**
> - Affecter votre copie du blueprint à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

### <a name="create-blueprint-from-sample"></a>Créer un blueprint à partir de l’exemple

Commencez par implémenter l’exemple de blueprint. Pour cela, créez un blueprint dans votre environnement en vous servant de l’exemple comme point de départ.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Dans la page **Démarrage** à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Recherchez l’exemple de blueprint **CMMC niveau 3** sous _Autres exemples_, puis sélectionnez **Utiliser cet exemple**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :

   - **Nom du blueprint** : Donnez un nom à votre copie de l’exemple de blueprint CMMC niveau 3.
   - **Emplacement de la définition** : utilisez les points de suspension et sélectionnez le groupe d’administration dans lequel vous souhaitez enregistrer votre copie de l’exemple.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Passez en revue la liste des artefacts qui sont inclus dans l’exemple de blueprint. De nombreux artefacts ont des paramètres que nous définirons plus tard. Sélectionnez **Enregistrer comme brouillon** lorsque vous avez terminé de passer en revue l’exemple de blueprint.

### <a name="publish-the-sample-copy"></a>Publier la copie de l’exemple

Votre copie de l’exemple de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée. Vous pouvez personnaliser la copie de l’exemple de blueprint en fonction de votre environnement et de vos besoins, mais ces modifications peuvent l’éloigner de son alignement avec les contrôles CMMC niveau 3.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans la nouvelle page à droite, indiquez la **Version** de votre copie de l’exemple de blueprint. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Dans **Notes de changement**, indiquez par exemple « Première version publiée à partir de l’exemple de blueprint CMMC niveau 3 ». Sélectionnez ensuite **Publier** en bas de la page.

### <a name="assign-the-sample-copy"></a>Affecter la copie de l’exemple

Une fois que la copie de l’exemple de blueprint a été **publiée**, elle peut être affectée à un abonnement dans le groupe d’administration où elle a été enregistrée. C’est à cette étape que les paramètres sont fournis pour que chaque déploiement de la copie de l’exemple de blueprint soit unique.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Affecter le blueprint** dans le haut de la page.

1. Indiquez les valeurs des paramètres pour l’affectation du blueprint :

   - Concepts de base

     - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui font partie du groupe d’administration où vous avez enregistré votre copie de l’exemple de blueprint. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chacun d’eux à l’aide des paramètres saisis.
     - **Nom de l’affectation** : Le nom est prérempli en fonction du nom du blueprint.
       Changez-le si nécessaire ou laissez-le tel quel.
     - **Emplacement** : Sélectionnez une région dans laquelle créer l’identité managée. Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté. Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version de définition du blueprint** : Choisissez une version **publiée** de votre copie de l’exemple de blueprint.

   - Verrouiller l'affectation

     Sélectionnez le paramètre de verrouillage de blueprint pour votre environnement. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../concepts/resource-locking.md).

   - Identité managée

     Conservez l’option par défaut, _Affecté(e) par le système_, pour l’identité managée.

   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont des [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters) puisqu’ils sont définis lors de l’affectation du blueprint. Pour obtenir la liste complète des paramètres d’artefact et leur description, consultez le [tableau des paramètres d’artefact](#artifact-parameters-table).

1. Une fois tous les paramètres entrés, sélectionnez **Affecter** au bas de la page. L’affectation du blueprint est créée et le déploiement de l’artefact démarre. Le déploiement prend environ une heure. Pour vérifier l’état du déploiement, ouvrez l’affectation du blueprint.

> [!WARNING]
> Le service Azure Blueprints et les exemples de blueprint intégrés sont **gratuits**. Les ressources Azure sont [facturées par produit](https://azure.microsoft.com/pricing/). Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût d’exécution des ressources déployées par cet exemple de blueprint.

### <a name="artifact-parameters-table"></a>Tableau des paramètres d’artefact

Le tableau suivant fournit la liste des paramètres d’artefact de blueprint :

|Nom de l’artefact|Type d’artefact|Nom du paramètre|Description|
|-|-|-|-|
|CMMC niveau 3|Affectation de rôle|Inclure les serveurs connectés à Arc pendant l’évaluation des stratégies Guest Configuration|En sélectionnant « true », vous acceptez d’être facturé tous les mois pour chacune de vos machines connectées à Arc. Pour plus d’informations, consultez https://aka.ms/policy-pricing.|
|CMMC niveau 3|Affectation de rôle|Liste des utilisateurs qui doivent être exclus du groupe Administrateurs de machines virtuelles Windows|Liste séparée par des points-virgules des utilisateurs qui doivent être exclus du groupe Administrateurs local. Ex. : Administrator; myUser1; myUser2|
|CMMC niveau 3|Affectation de rôle|Liste des utilisateurs à inclure dans le groupe Administrateurs de machine virtuelle Windows|Liste séparée par des points-virgules des utilisateurs qui doivent être inclus dans le groupe Administrateurs local. Ex. : Administrator; myUser1; myUser2|
|CMMC niveau 3|Affectation de rôle|ID d’espace de travail Log Analytics pour les rapports d’agent de machine virtuelle|ID (GUID) de l’espace de travail Log Analytics où les agents de machine virtuelle doivent envoyer leur rapport|
|CMMC niveau 3|Affectation de rôle|Noms de courbe elliptique autorisés|Liste des noms de courbe autorisés pour les certificats de chiffrement à courbe elliptique.|
|CMMC niveau 3|Affectation de rôle|Types de clé autorisés|Liste des types de clé autorisés|
|CMMC niveau 3|Affectation de rôle|Autoriser l’utilisation du réseau hôte pour les pods de cluster Kubernetes|Définissez cette valeur sur true si le pod est autorisé à utiliser un réseau hôte, sinon sur false.|
|CMMC niveau 3|Affectation de rôle|Auditer la modification de la stratégie d’authentification|Spécifie si des événements d’audit sont générés quand des modifications sont apportées à la stratégie d’authentification. Ce paramètre est utile pour le suivi des modifications apportées aux approbations et privilèges au niveau du domaine et de la forêt qui sont accordés aux comptes ou groupes d’utilisateurs.|
|CMMC niveau 3|Affectation de rôle|Auditer la modification de la stratégie d’autorisation|Spécifie si des événements d’audit sont générés pour l’attribution et la suppression des droits d’utilisateur dans les stratégies de droits d’utilisateur, pour les changements d’autorisation de l’objet de jeton de sécurité, les changements des attributs de ressource et les changements de la stratégie d’accès centralisé pour les objets de système de fichiers.|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : La sauvegarde Azure doit être activée pour les machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les comptes Cognitive Services doivent restreindre l’accès réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les instances managées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : API Azure pour FHIR doit utiliser une clé gérée par le client (CMK) pour chiffrer les données au repos|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Web Application Firewall (WAF) doit être activé pour Azure Front Door Service|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les paramètres d’évaluation des vulnérabilités de SQL Server doivent contenir une adresse e-mail pour recevoir les rapports d’analyse|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’accès réseau public doit être désactivé pour les comptes Cognitive Services|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : CORS ne doit pas autoriser toutes les ressources à accéder à vos applications de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les recommandations de durcissement du réseau adaptatif doivent être appliquées sur les machines virtuelles connectées à Internet|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Plusieurs propriétaires doivent être affectés à votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le chiffrement de disque doit être appliqué sur les machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La notification par e-mail au propriétaire de l’abonnement pour les alertes à gravité élevée doit être activée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La protection contre la suppression définitive doit être activée sur le coffre de clés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le débogage à distance doit être désactivé pour les applications de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour Key Vault doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : la sauvegarde géoredondante doit être activée pour Azure Database for MariaDB|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : CORS ne doit pas autoriser tous les domaines à accéder à votre API pour FHIR|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Windows doivent répondre aux exigences de « Options de sécurité - Sécurité réseau »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les règles de liste d’autorisation dans votre stratégie de contrôle d’application adaptatif doivent être mises à jour|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Web Application Firewall (WAF) doit utiliser le mode spécifié pour Application Gateway|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les clés doivent avoir des dates d’expiration définies|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La technologie Transparent Data Encryption doit être activée sur les bases de données SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le profil de journal Azure Monitor doit collecter les journaux des catégories « écrire », « supprimer » et « action »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : l’évaluation des vulnérabilités doit être activée sur l’instance managée SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : La suppression réversible doit être activée pour le coffre de clés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Seules les connexions sécurisées à votre instance Azure Cache pour Redis doivent être activées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le chiffrement d’infrastructure doit être activé sur les serveurs Azure Database pour PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La solution de protection des points de terminaison doit être installée sur les groupes de machines virtuelles identiques|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour App Service doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Windows doivent répondre aux exigences de « Stratégies d’audit du système - Changement de stratégie »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les comptes Cognitive Services doivent activer le chiffrement des données|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’accès SSH à partir d’Internet doit être bloqué|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les disques détachés doivent être chiffrés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour le stockage doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes de stockage doivent restreindre l’accès réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : CORS ne doit pas autoriser toutes les ressources à accéder à votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Déployer Advanced Threat Protection sur les comptes de stockage|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les variables du compte Automation doivent être chiffrées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans IoT Hub doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le chiffrement d’infrastructure doit être activé sur les serveurs Azure Database pour MySQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations de sécurité spécifiques (Microsoft.Security/securitySolutions/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les vulnérabilités détectées dans la configuration de la sécurité de vos groupes de machines virtuelles identiques doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Windows doivent répondre aux exigences de « Options de sécurité - Accès réseau »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La sécurisation du transfert vers des comptes de stockage doit être activée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Azure Monitor doit collecter les journaux d’activité de toutes les régions|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Web Application Firewall (WAF) doit utiliser le mode spécifié pour Azure Front Door Service|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les comptes de stockage doivent avoir un chiffrement d’infrastructure|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les contrôles d’application adaptatifs pour définir les applications sécurisées doivent être activés sur vos machines|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La sauvegarde géoredondante doit être activée pour Azure Database pour PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Windows doivent répondre aux exigences de « Options de sécurité - Contrôle de compte d’utilisateur »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour les serveurs doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Trois propriétaires au plus doivent être désignés pour votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les abonnements doivent avoir l’adresse e-mail d’un contact pour le signalement des problèmes de sécurité|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’accès public au compte de stockage doit être interdit|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Une solution d’évaluation des vulnérabilités doit être activée sur vos machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour Kubernetes doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le pare-feu doit être activé sur Key Vault|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Web Application Firewall (WAF) doit être activé pour Application Gateway|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : CORS ne doit pas autoriser toutes les ressources à accéder à vos applications web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Auditer les machines Windows qui autorisent la réutilisation des 24 mots de passe précédents|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les registres de conteneurs doivent être chiffrés avec une clé gérée par le client (CMK)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’accès réseau public doit être désactivé pour les serveurs PostgreSQL flexibles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les vulnérabilités dans les images Azure Container Registry doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : La propriété ClusterProtectionLevel des clusters Service Fabric doivent être définie sur EncryptAndSign|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour les serveurs SQL sur des machines doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les comptes Cognitive Services doivent activer le chiffrement des données avec une clé gérée par le client|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes déconseillés doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Function App ne doit pas être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La notification par e-mail pour les alertes de gravité élevée doit être activée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le compte de stockage doit utiliser une clé gérée par le client pour le chiffrement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les clés doivent avoir le type de chiffrement spécifié, RSA ou EC|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les abonnements Azure doivent avoir un profil de journal pour le journal d’activité|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les disques de système d’exploitation et de données dans les clusters Azure Kubernetes Service doivent être chiffrés par des clés gérées par le client|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour les serveurs Azure SQL Database doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le chiffrement Azure Data Explorer au repos doit utiliser une clé gérée par le client|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les clés qui utilisent le chiffrement RSA doivent avoir une taille de clé minimale spécifiée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La sauvegarde géoredondante doit être activée pour Azure Database pour MySQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les pods de cluster Kubernetes doivent uniquement utiliser un réseau hôte et une plage de ports approuvés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Des mises à jour système doivent être installées sur vos machines|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Windows doivent répondre aux exigences de « Stratégies d’audit du système - Utilisation de privilège »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les travaux Azure Stream Analytics doivent utiliser des clés gérées par le client pour chiffrer les données|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’extension Microsoft IaaSAntimalware doit être déployée sur les serveurs Windows|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Tous les ports réseau doivent être restreints sur les groupes de sécurité réseau associés à votre machine virtuelle|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le niveau tarifaire standard Security Center doit être sélectionné|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Auditer les machines Windows qui ne limitent pas la longueur minimale du mot de passe à 14 caractères|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Auditer l’utilisation de règles personnalisées RBAC|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L'application web ne doit pas être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’audit sur SQL Server doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’agent Log Analytics doit être installé sur les machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Advanced Data Security doit être activé sur vos serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Advanced Data Security doit être activé sur l’instance managée SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sur les services Kubernetes|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines virtuelles doivent avoir l’extension Guest Configuration|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Superviser les agents Endpoint Protection manquants dans Azure Security Center|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le journal d’activité doit être conservé pendant au moins un an|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les ports de gestion des machines virtuelles doivent être protégés avec le contrôle d’accès réseau juste-à-temps|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’accès réseau public doit être désactivé pour les serveurs PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Déployer Advanced Threat Protection pour les comptes Cosmos DB|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans App Services doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L'application API doit uniquement être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.ClassicNetwork/networkSecurityGroups/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Network/networkSecurityGroups/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Network/networkSecurityGroups/securityRules/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Sql/servers/firewallRules/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines virtuelles non connectées à Internet doivent être protégées avec des groupes de sécurité réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Auditer les machines Windows qui n’ont pas le paramètre de complexité de mot de passe activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Azure Defender pour les registres de conteneurs doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les travaux Azure Data Box doivent activer le chiffrement double pour les données au repos sur l’appareil|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les mises à jour système doivent être installées sur les groupes de machines virtuelles identiques|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Microsoft Antimalware pour Azure doit être configuré pour mettre à jour automatiquement les signatures de protection|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations de stratégie spécifiques (Microsoft.Authorization/policyAssignments/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’accès réseau public doit être désactivé pour les serveurs MySQL flexibles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes de stockage doivent autoriser l’accès des services Microsoft approuvés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le débogage à distance doit être désactivé pour les applications web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les certificats qui utilisent le chiffrement RSA doivent avoir la taille de clé minimale spécifiée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les registres de conteneurs ne doivent pas autoriser un accès illimité au réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La mise en œuvre de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’extension Guest Configuration doit être déployée sur des machines virtuelles Azure avec une identité managée affectée par le système|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La sauvegarde géoredondante à long terme doit être activée pour les bases de données Azure SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’accès réseau public doit être désactivé pour les serveurs MySQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Auditer les machines Windows qui ne stockent pas les mots de passe à l’aide du chiffrement réversible|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Windows doivent répondre aux exigences de « Attribution de droits d’utilisateur »|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’accès RDP à partir d’Internet doit être bloqué|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Auditer les machines Linux qui n’ont pas les autorisations de fichier passwd définies sur 0644|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les sous-réseaux doivent être associés à un groupe de sécurité réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La mise en œuvre de la connexion SSL doit être activée pour les serveurs de base de données MySQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les vulnérabilités dans les configurations de la sécurité des conteneurs doivent être corrigées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Le débogage à distance doit être désactivé pour les applications API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Auditer les machines Linux qui autorisent les connexions à distance des comptes sans mot de passe|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes dépréciés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le chiffrement double doit être activé sur Azure Data Explorer|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : L’agent Log Analytics doit être installé sur les groupes identiques de machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Le chiffrement de disque doit être activé sur Azure Data Explorer|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les machines virtuelles connectées à Internet doivent être protégées avec des groupes de sécurité réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Auditer les machines Linux qui ont des comptes sans mot de passe|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les espaces de travail Azure Synapse doivent utiliser des clés gérées par le client pour chiffrer les données au repos|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Kubernetes Services doit être mis à niveau avec une version non vulnérable de Kubernetes|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Tout le trafic Internet doit être routé par le biais de votre pare-feu Azure déployé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les machines Linux doivent répondre aux conditions de la base de référence de sécurité Azure|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : L’accès réseau public doit être désactivé pour les serveurs MariaDB|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de la stratégie : Les vulnérabilités de vos bases de données SQL doivent être éliminées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Effet de stratégie : Les clés utilisant le chiffrement à courbe elliptique doivent avoir les noms de courbe spécifiés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](https://aka.ms/policyeffects).|
|CMMC niveau 3|Affectation de rôle|Espaces de noms exclus de l’évaluation de la stratégie : Les pods de cluster Kubernetes doivent utiliser uniquement un réseau hôte et une plage de ports approuvés|Liste des espaces de noms Kubernetes à exclure de l’évaluation de la stratégie.|
|CMMC niveau 3|Affectation de rôle|Dernière version de Java pour App Services|Dernière version de Java prise en charge pour App Services|
|CMMC niveau 3|Affectation de rôle|Dernière version de Python pour Linux pour App Services|Dernière version de Python prise en charge pour App Services|
|CMMC niveau 3|Affectation de rôle|Facultatif : Liste d’images VM avec un système d’exploitation Linux pris en charge à ajouter à l’étendue pendant l’audit du déploiement d’agent Log Analytics|Exemple de valeur : « /subscriptions/<<subscriptionId>>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage »|
|CMMC niveau 3|Affectation de rôle|Facultatif : Liste d’images VM avec un système d’exploitation Windows pris en charge à ajouter à l’étendue pendant l’audit du déploiement d’agent Log Analytics|Exemple de valeur : « /subscriptions/<<subscriptionId>>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage »|
|CMMC niveau 3|Affectation de rôle|Liste des régions où Network Watcher doit être activé|Vérifiez si Network Watcher n’est pas activé pour une ou plusieurs régions.|
|CMMC niveau 3|Affectation de rôle|Liste des types de ressources devant avoir les journaux de diagnostic activés||
|CMMC niveau 3|Affectation de rôle|Valeur maximale dans la plage de ports d’hôte autorisée que les pods peuvent utiliser dans l’espace de noms de réseau hôte|Valeur maximale de la plage de ports hôtes autorisée que les pods peuvent utiliser dans l’espace de noms du réseau hôte.|
|CMMC niveau 3|Affectation de rôle|Taille de clé RSA minimale pour les clés|Taille de clé minimale pour les clés RSA.|
|CMMC niveau 3|Affectation de rôle|Certificats de taille de clé RSA minimale|Taille minimale de clé pour les certificats RSA.|
|CMMC niveau 3|Affectation de rôle|Version TLS minimale pour les serveurs web Windows|Les serveurs web Windows avec des versions de TLS inférieures seront évalués comme non conformes|
|CMMC niveau 3|Affectation de rôle|Valeur minimale dans la plage de ports d’hôte autorisée que les pods peuvent utiliser dans l’espace de noms de réseau hôte|Valeur minimale de la plage de ports hôtes autorisée que les pods peuvent utiliser dans l’espace de noms du réseau hôte.|
|CMMC niveau 3|Affectation de rôle|Condition du mode|Mode obligatoire pour toutes les stratégies WAF|
|CMMC niveau 3|Affectation de rôle|Condition du mode|Mode obligatoire pour toutes les stratégies WAF|
|CMMC niveau 3|Affectation de rôle|Chemins d’hôte autorisés pour les volumes hostPath de pod|Chemins d’hôte autorisés pour les volumes hostPath de pod à utiliser. Fournissez une liste de chemins vide pour bloquer tous les chemins d’hôte.|
|CMMC niveau 3|Affectation de rôle|Accès réseau : les chemins de Registre accessibles à distance|Spécifie les chemins de Registre accessibles sur le réseau, quels que soient les utilisateurs ou groupes listés dans la liste ACL (liste de contrôle d’accès) de la clé de Registre `winreg`.|
|CMMC niveau 3|Affectation de rôle|Accès réseau : chemins et sous-chemins de Registre accessibles à distance|Spécifie les chemins et sous-chemins de Registre accessibles sur le réseau, quels que soient les utilisateurs ou groupes listés dans la liste ACL de la clé de Registre `winreg`.|
|CMMC niveau 3|Affectation de rôle|Accès réseau : les partages qui sont accessibles de manière anonyme|Spécifie les partages réseau accessibles aux utilisateurs anonymes. La configuration par défaut de ce paramètre de stratégie a peu d’effet, car tous les utilisateurs doivent être authentifiés avant de pouvoir accéder aux ressources partagées sur le serveur.|
|CMMC niveau 3|Affectation de rôle|Sécurité réseau : Configurer les types de chiffrement autorisés pour Kerberos|Spécifie les types de chiffrement que Kerberos est autorisé à utiliser.|
|CMMC niveau 3|Affectation de rôle|Sécurité réseau : niveau d’authentification LAN Manager|Spécifiez le protocole d’authentification par stimulation/réponse utilisé pour les ouvertures de session réseau. Ce choix affecte le niveau du protocole d’authentification utilisé par les clients, le niveau de sécurité de session négocié et le niveau d’authentification accepté par les serveurs.|
|CMMC niveau 3|Affectation de rôle|Sécurité réseau : conditions requises pour la signature de client LDAP|Spécifiez le niveau de signature des données demandé pour le compte des clients qui émettent des demandes LDAP BIND.|
|CMMC niveau 3|Affectation de rôle|Sécurité réseau : sécurité de session minimale pour les clients basés sur NTLM SSP (y compris RPC sécurisé)|Spécifie les comportements qui sont autorisés par les clients pour les applications utilisant le fournisseur SSP (Security Support Provider) NTLM. L’interface SSPI (SSP) est utilisée par les applications qui ont besoin de services d’authentification. Pour plus d’informations, voir [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers).|
|CMMC niveau 3|Affectation de rôle|Sécurité réseau : sécurité de session minimale pour les serveurs basés sur NTLM SSP (y compris RPC sécurisé)|Spécifie les comportements qui sont autorisés par les clients pour les applications utilisant le fournisseur SSP (Security Support Provider) NTLM. L’interface SSPI (SSP) est utilisée par les applications qui ont besoin de services d’authentification.|
|CMMC niveau 3|Affectation de rôle|Dernière version de PHP pour App Services|Dernière version de PHP prise en charge pour App Services|
|CMMC niveau 3|Affectation de rôle|Période de conservation obligatoire (en jours) pour les journaux de diagnostic IoT Hub||
|CMMC niveau 3|Affectation de rôle|Nom du groupe de ressources pour Network Watcher|Nom du groupe de ressources de NetworkWatcher, par exemple NetworkWatcherRG. Il s’agit du groupe de ressources où se trouvent les Network Watchers.|
|CMMC niveau 3|Affectation de rôle|Paramètre d’audit requis pour les serveurs SQL||
|CMMC niveau 3|Affectation de rôle|Références SKU Azure Data Box qui prennent en charge le chiffrement double logiciel|Liste des références SKU Azure Data Box qui prennent en charge le chiffrement double logiciel|
|CMMC niveau 3|Affectation de rôle|UAC : mode d’approbation Administrateur pour le compte Administrateur intégré|Spécifie le comportement du mode d’approbation Administrateur pour le compte Administrateur intégré.|
|CMMC niveau 3|Affectation de rôle|UAC : comportement de l’invite d’élévation pour les administrateurs dans le mode d’approbation Administrateur|Spécifie le comportement de l’invite d’élévation pour les administrateurs.|
|CMMC niveau 3|Affectation de rôle|UAC : détecter les installations d’application et demander une élévation|Spécifie le comportement de la détection d’installation d’application pour l’ordinateur.|
|CMMC niveau 3|Affectation de rôle|UAC : exécuter tous les administrateurs en mode d’approbation Administrateur|Spécifie le comportement de tous les paramètres de stratégie de contrôle de compte d’utilisateur (UAC) pour l’ordinateur.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui peuvent forcer l’arrêt à partir d’un système distant|Spécifie les utilisateurs et groupes autorisés à arrêter l’ordinateur à partir d’un emplacement distant sur le réseau.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui ne sont pas autorisés à accéder à cet ordinateur à partir du réseau|Spécifie les utilisateurs ou groupes qui sont explicitement non autorisés à se connecter à l’ordinateur sur le réseau.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui ne sont pas autorisés à ouvrir une session locale|Spécifie les utilisateurs et groupes qui sont explicitement non autorisés à ouvrir une session sur l’ordinateur.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui ne sont pas autorisés à ouvrir une session avec un programme de traitement par lots|Spécifie les utilisateurs et groupes qui ne sont pas explicitement autorisés à ouvrir une session sur l’ordinateur avec un programme de traitement par lots (tâche planifiée, par exemple).|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui ne sont pas autorisés à ouvrir une session avec un service|Spécifie les comptes de service qui ne sont pas explicitement autorisés à inscrire un processus comme service.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui ne sont pas autorisés à ouvrir une session via les Services Bureau à distance|Spécifie les utilisateurs et groupes qui sont explicitement non autorisés à ouvrir une session sur l’ordinateur par le biais des services Terminal Server/Client Bureau à distance.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui peuvent restaurer des fichiers et des répertoires|Spécifie les utilisateurs et groupes autorisés à contourner les autorisations de fichier, de répertoire, de Registre et autres autorisations d’objet persistantes pendant la restauration des fichiers et répertoires sauvegardés.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs et groupes qui peuvent arrêter le système|Spécifie les utilisateurs et groupes ayant ouvert une session locale sur les ordinateurs de votre environnement qui sont autorisés à arrêter le système d’exploitation avec la commande Arrêter.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes pouvant ouvrir une session locale|Spécifie les utilisateurs distants sur le réseau qui sont autorisés à se connecter à l’ordinateur. Cela n’inclut pas les Connexion Bureau à distance.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes qui peuvent sauvegarder des fichiers et des répertoires|Spécifie les utilisateurs et groupes autorisés à contourner les autorisations de fichier et de répertoire pour sauvegarder le système.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes qui peuvent changer l’heure système|Spécifie les utilisateurs et groupes autorisés à changer la date et l’heure de l’horloge interne de l’ordinateur.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes qui peuvent changer le fuseau horaire|Spécifie les utilisateurs et groupes autorisés à changer le fuseau horaire de l’ordinateur.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes qui peuvent créer un objet jeton|Spécifie les utilisateurs et groupes autorisés à créer un jeton d’accès pouvant fournir des droits élevés pour accéder aux données sensibles.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes pouvant ouvrir une session locale|Spécifie les utilisateurs ou groupes qui peuvent ouvrir une session de manière interactive sur l’ordinateur. Les utilisateurs qui essaient d’ouvrir une session avec Connexion Bureau à distance ou IIS ont également besoin de ce droit d’utilisateur.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs du Bureau à distance|Utilisateurs ou groupes qui peuvent ouvrir une session par le biais des Services Bureau à distance|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes qui peuvent gérer le journal d’audit et de sécurité|Spécifie les utilisateurs et groupes autorisés à changer les options d’audit pour les fichiers et les répertoires, et à effacer le journal de sécurité.|
|CMMC niveau 3|Affectation de rôle|Utilisateurs ou groupes qui peuvent s’approprier des fichiers ou d’autres objets|Spécifie les utilisateurs et groupes autorisés à s’approprier des fichiers, des dossiers, des clés de Registre, des processus ou des threads. Ce droit d’utilisateur contourne les autorisations en place pour protéger les objets afin d’accorder la propriété à l’utilisateur spécifié.|

## <a name="next-steps"></a>Étapes suivantes

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).