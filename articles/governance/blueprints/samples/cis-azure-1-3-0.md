---
title: Exemple de blueprint CIS Microsoft Azure Foundations Benchmark v1.3.0
description: Vue d’ensemble de l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark v1.3.0 Cet exemple de blueprint aide les clients à évaluer des contrôles spécifiques.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565994"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Exemple de blueprint CIS Microsoft Azure Foundations Benchmark v1.3.0

L’exemple de blueprint CIS Microsoft Azure Foundations Benchmark v1.3.0 fournit des garde-fou de gouvernance avec [Azure Policy](../../policy/overview.md), qui vous aident à évaluer les recommandations spécifiques de CIS Microsoft Azure Foundations Benchmark v1.3.0. Avec ce blueprint, les clients peuvent déployer un ensemble de stratégies pour toute architecture déployée par Azure devant implémenter des recommandations de CIS Microsoft Azure Foundations Benchmark v1.3.0.

## <a name="recommendation-mapping"></a>Correspondances des recommandations

Le [mappage de recommandations Azure Policy](../../policy/samples/cis-azure-1-3-0.md) fournit des détails sur les définitions de stratégie incluses dans ce blueprint et sur la façon dont ces définitions de stratégie sont mappées aux **recommandations** dans CIS Microsoft Azure Foundations Benchmark v1.3.0. Quand des ressources sont attribuées à une architecture, elles sont évaluées par Azure Policy pour savoir si elles sont en conformité avec les stratégies affectées. Pour plus d’informations, consultez [Présentation d’Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Déployer

Pour déployer l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark v1.3.0 d’Azure Blueprints, vous devez effectuer les étapes suivantes :

> [!div class="checklist"]
> - Créer un blueprint à partir de l’exemple
> - Marquer la copie de l’exemple en tant que **Publié**
> - Affecter votre copie du blueprint à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

### <a name="create-blueprint-from-sample"></a>Créer un blueprint à partir de l’exemple

Commencez par implémenter l’exemple de blueprint. Pour cela, créez un blueprint dans votre environnement en vous servant de l’exemple comme point de départ.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Dans la page **Démarrage** à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Recherchez l’exemple de blueprint **CIS Microsoft Azure Foundations Benchmark v1.3.0** sous _Autres exemples_, puis sélectionnez **Utiliser cet exemple**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :

   - **Nom du blueprint** : Nommez votre copie de l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark.
   - **Emplacement de la définition** : utilisez les points de suspension et sélectionnez le groupe d’administration dans lequel vous souhaitez enregistrer votre copie de l’exemple.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Passez en revue la liste des artefacts qui sont inclus dans l’exemple de blueprint. De nombreux artefacts ont des paramètres que nous définirons plus tard. Sélectionnez **Enregistrer comme brouillon** lorsque vous avez terminé de passer en revue l’exemple de blueprint.

### <a name="publish-the-sample-copy"></a>Publier la copie de l’exemple

Votre copie de l’exemple de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée. Vous pouvez personnaliser la copie de l’exemple de blueprint en fonction de votre environnement et de vos besoins, mais ces modifications peuvent l’éloigner de son alignement avec les recommandations de CIS Microsoft Azure Foundations Benchmark v1.3.0.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans la nouvelle page à droite, indiquez la **Version** de votre copie de l’exemple de blueprint. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Dans **Notes de changement**, indiquez par exemple « Première version publiée à partir de l’exemple de blueprint CIS Microsoft Azure Foundations Benchmark ». Sélectionnez ensuite **Publier** en bas de la page.

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
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Liste des extensions de machine virtuelle approuvées pour utilisation|Liste d’extensions de machine virtuelle séparées par des points-virgules. Pour voir la liste complète des extensions, utilisez la commande Azure PowerShell Get-AzVMExtensionImage.|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les instances managées SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les paramètres d’évaluation des vulnérabilités de SQL Server doivent contenir une adresse e-mail pour recevoir les rapports d’analyse|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Azure Data Lake Store doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Le chiffrement de disque doit être appliqué sur les machines virtuelles|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La protection contre la suppression définitive doit être activée sur le coffre de clés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que « Certificats client (certificats client entrants) » est activé pour l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les serveurs SQL doivent utiliser des clés gérées par le client pour chiffrer les données au repos|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une identité managée doit être utilisée dans votre application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour Key Vault doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les rôles de propriétaire d’abonnement personnalisé ne doivent pas exister|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les clés doivent avoir des dates d’expiration définies|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La technologie Transparent Data Encryption doit être activée sur les bases de données SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : l’évaluation des vulnérabilités doit être activée sur l’instance managée SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour App Service doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les comptes de stockage doivent restreindre l’accès réseau à l’aide de règles de réseau virtuel|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une identité managée doit être utilisée dans votre application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’accès SSH à partir d’Internet doit être bloqué|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les disques détachés doivent être chiffrés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour le stockage doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les comptes de stockage doivent restreindre l’accès réseau|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Logic Apps doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans IoT Hub doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Seul FTPS doit être obligatoire dans votre application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations de sécurité spécifiques (Microsoft.Security/securitySolutions/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations de sécurité spécifiques (Microsoft.Security/securitySolutions/write)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La sécurisation du transfert vers des comptes de stockage doit être activée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans les comptes Batch doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Le provisionnement automatique de l’agent Log Analytics doit être activé sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : FTPS doit être obligatoire dans votre application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour les serveurs doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les abonnements doivent avoir l’adresse e-mail d’un contact pour le signalement des problèmes de sécurité|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’accès public au compte de stockage doit être interdit|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour Kubernetes doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La limitation de connexion doit être activée pour les serveurs de base de données PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que « Certificats client (certificats client entrants) » est activé pour l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour les serveurs SQL sur des machines doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La notification par e-mail pour les alertes de gravité élevée doit être activée|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Le compte de stockage doit utiliser une clé gérée par le client pour le chiffrement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de PHP est la plus récente, si elle est utilisée dans le cadre de l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Python est la plus récente, si elle est utilisée dans le cadre de l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans les groupes de machines virtuelles identiques doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour les serveurs Azure SQL Database doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Event Hub doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Des mises à jour système doivent être installées sur vos machines|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les serveurs SQL doivent être configurés avec une conservation d’audit de 90 jours ou plus.|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’authentification doit être activée sur votre application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les secrets doivent avoir des dates d’expiration définies|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Seul FTPS doit être obligatoire dans votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de Java est la plus récente, si elle est utilisée dans le cadre de l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L'application web ne doit pas être accessible via HTTPS|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’audit sur SQL Server doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Advanced Data Security doit être activé sur vos serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Advanced Data Security doit être activé sur l’instance managée SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Le contrôle d’accès en fonction du rôle (RBAC) doit être utilisé sur les services Kubernetes|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Superviser les agents Endpoint Protection manquants dans Azure Security Center|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans les services de recherche doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans App Services doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Network/networkSecurityGroups/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Network/networkSecurityGroups/securityRules/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Network/networkSecurityGroups/securityRules/write)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Network/networkSecurityGroups/write)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Sql/servers/firewallRules/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations d’administration spécifiques (Microsoft.Sql/servers/firewallRules/write)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Seules les extensions de machine virtuelle approuvées doivent être installées|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Azure Defender pour les registres de conteneurs doit être activé|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une identité managée doit être utilisée dans votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’authentification doit être activée sur votre application API|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations de stratégie spécifiques (Microsoft.Authorization/policyAssignments/delete)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Une alerte de journal d’activité doit exister pour des opérations de stratégie spécifiques (Microsoft.Authorization/policyAssignments/write)|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’authentification doit être activée sur votre application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Data Lake Analytics doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les comptes de stockage doivent autoriser l’accès des services Microsoft approuvés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Key Vault doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La mise en œuvre de la connexion SSL doit être activée pour les serveurs de base de données PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que la version de HTTP est la plus récente, si elle est utilisée pour exécuter l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L'authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’accès RDP à partir d’Internet doit être bloqué|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La mise en œuvre de la connexion SSL doit être activée pour les serveurs de base de données MySQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Vérifier que « Certificats client (certificats client entrants) » est activé pour l’application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les points de contrôle de journal doivent être activés pour les serveurs de base de données PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les connexions de journal doivent être activées pour les serveurs de base de données PostgreSQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les déconnexions doivent être journalisées pour les serveurs de base de données PostgreSQL.|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : L’évaluation des vulnérabilités doit être activée sur vos serveurs SQL|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application web|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Service Bus doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Les journaux de diagnostic dans Azure Stream Analytics doivent être activés|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : La dernière version de TLS doit être utilisée dans votre application de fonction|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Effet de la stratégie : Le compte de stockage où se trouve le conteneur des journaux d’activité doit être chiffré avec BYOK|Pour plus d’informations sur les effets, consultez [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Inclure les clusters AKS lors de l’audit si les journaux de diagnostic du groupe de machines virtuelles identiques sont activés||
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Dernière version de Java pour App Services|Dernière version de Java prise en charge pour App Services|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Dernière version de Python pour Linux pour App Services|Dernière version de Python prise en charge pour App Services|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Liste des régions où Network Watcher doit être activé|Pour voir la liste complète des régions, exécutez la commande PowerShell Get-AzLocation|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Dernière version de PHP pour App Services|Dernière version de PHP prise en charge pour App Services|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Période de conservation obligatoire (en jours) pour les journaux de ressource|Pour plus d’informations sur les journaux de ressource, rendez-vous sur [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) : |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Nom du groupe de ressources pour Network Watcher|Nom du groupe de ressources où se trouvent les observateurs réseau|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Affectation de rôle|Paramètre d’audit requis pour les serveurs SQL||

## <a name="next-steps"></a>Étapes suivantes

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).