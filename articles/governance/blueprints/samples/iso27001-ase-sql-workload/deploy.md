---
title: Exemple - plan de charge de travail ISO 27001 App Service environnement/SQL Database - étapes de déploiement
description: Déployer des étapes de l’exemple de plan de charge de travail ISO 27001 App Service environnement/SQL Database.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 80c98170fc136c20d8489cec5d145f96e207bc9f
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201906"
---
# <a name="deploy-the-azure-blueprints-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Déployer l’exemple de plan de charge de travail Azure plans ISO 27001 App Service environnement/base de données SQL

Pour déployer l’exemple de plan de charge de travail Azure plans ISO 27001 App Service environnement/base de données SQL, les étapes suivantes doivent être prises :

> [!div class="checklist"]
> - Déployer le [ISO 27001 Shared Services](../iso27001-shared/index.md) exemple de plan
> - Créer un nouveau plan à partir de l’exemple
> - Marquer votre copie de l’exemple en tant que **publié**
> - Affectez votre copie du plan à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Déployer l’exemple de solution blueprint ISO 27001 Shared Services

Avant de pouvoir déployer cet exemple de plan, le [ISO 27001 Shared Services](../iso27001-shared/index.md) exemple de plan doit être déployé à l’abonnement cible. Sans un déploiement réussi de l’exemple de solution blueprint ISO 27001 Shared Services, cet exemple de solution blueprint est absentes de dépendances d’infrastructure et échouer au cours du déploiement.

> [!IMPORTANT]
> Cet exemple de plan doit être affecté dans le même abonnement que le [ISO 27001 Shared Services](../iso27001-shared/index.md) exemple de plan.

## <a name="create-blueprint-from-sample"></a>Créer un plan à partir de l’exemple

Tout d’abord, implémentez l’exemple de plan en créant un nouveau plan dans votre environnement à l’aide de l’exemple comme un starter.

1. Sélectionnez **tous les services** et recherchez et sélectionnez **stratégie** dans le volet gauche. Sur le **stratégie** page, sélectionnez **plans**.

1. À partir de la **mise en route** page sur la gauche, sélectionnez le **créer** bouton sous _créer un plan_.

1. Rechercher le **ISO 27001 : Charge de travail ASE/SQL** exemple de plan sous _autres exemples_ et sélectionnez **utiliser cet exemple**.

1. Entrez le _notions de base_ de l’exemple de plan :

   - **Nom du plan**: Fournissez un nom pour votre copie de l’exemple de plan de charge de travail ISO 27001 ASE/SQL.
   - **Emplacement de définition**: Utiliser les points de suspension, puis sélectionnez le groupe d’administration pour enregistrer votre copie de l’échantillon.

1. Sélectionnez le _artefacts_ onglet en haut de la page ou **suivant : Artefacts** en bas de la page.

1. Passez en revue la liste des artefacts qui composent l’exemple de plan. La plupart des artefacts ont des paramètres que nous allons définir ultérieurement. Sélectionnez **enregistrer le brouillon** lorsque vous avez terminé d’examiner l’exemple de plan.

## <a name="publish-the-sample-copy"></a>Publier l’exemple de copie

Votre copie de l’exemple de plan a maintenant été créée dans votre environnement. Il est créé dans **Draft** mode et doit être **publié** avant qu’il peut être affectée et déployée. La copie de l’exemple de solution blueprint peut être personnalisée à votre environnement et les besoins, mais cette modification peut le déplacer en dehors de la norme ISO 27001.

1. Sélectionnez **tous les services** et recherchez et sélectionnez **stratégie** dans le volet gauche. Sur le **stratégie** page, sélectionnez **plans**.

1. Sélectionnez le **Blueprint définitions** page sur la gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de plan, puis sélectionnez-le.

1. Sélectionnez **publier blueprint** en haut de la page. Dans la nouvelle page sur la droite, fournissez un **Version** pour votre copie de l’exemple de plan. Cette propriété est utile pour, si vous apportez une modification ultérieurement. Fournir **modifier les remarques** telles que « première version publiés à partir de l’exemple de solution blueprint 27001 ISO ». Puis sélectionnez **publier** en bas de la page.

## <a name="assign-the-sample-copy"></a>Affecter l’exemple de copie

Une fois que la copie de l’exemple de plan a été correctement **publié**, il peut être affecté à un abonnement dans le groupe d’administration qu’il a été enregistré dans. Cette étape est où les paramètres sont fournis pour rendre chaque déploiement de la copie de l’exemple de plan unique.

1. Sélectionnez **tous les services** et recherchez et sélectionnez **stratégie** dans le volet gauche. Sur le **stratégie** page, sélectionnez **plans**.

1. Sélectionnez le **Blueprint définitions** page sur la gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de plan, puis sélectionnez-le.

1. Sélectionnez **Assign blueprint** en haut de la page de définition de plan.

1. Indiquez les valeurs de paramètre pour l’affectation de plan :

   - Concepts de base

     - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui se trouvent dans le groupe d’administration vous enregistrement votre copie de l’exemple de plan à. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chaque en utilisant les paramètres d’entrée.
     - **Nom de l’affectation**: Le nom est prérempli pour vous en fonction du nom de la solution blueprint.
       Modifier en fonction des besoins, ou laissez la valeur actuelle.
     - **Emplacement** : Sélectionnez une région pour l’identité gérée à créer dans. Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté. Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version de définition de plan**: Choisir un **publié** version de votre copie de l’exemple de plan.

   - Verrouiller l'affectation

     Sélectionnez le paramètre pour votre environnement de verrouillage de plan. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../../concepts/resource-locking.md).

   - Identité managée

     Laissez la valeur par défaut _système affecté_ gérés option d’identité.

   - Paramètres de blueprint

     Les paramètres définis dans cette section sont utilisés par la plupart des artefacts dans la définition de plan pour assurer la cohérence.

     - **Nom de l’organisation**: Entrez un nom court pour votre organisation. Cette propriété est principalement utilisée pour nommer des ressources.
     - **Partagé des ID d’abonnement de Service**: ID d’abonnement dans lequel le [ISO 27001 Shared Services](../iso27001-shared/index.md) échantillon de plan est affecté.
     - **Préfixe d’adresse par défaut sous-réseau**: La notation CIDR pour le sous-réseau du réseau virtuel par défaut.
       Valeur par défaut est _10.1.0.0/16_.
     - **Emplacement de la charge de travail**: Détermine que les artefacts sont déployés à l’emplacement. Tous les services sont disponibles dans tous les emplacements. Artefacts de déploiement de ces services constituent une option de paramètre pour l’emplacement de déploiement de cet artefact à.

   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont [paramètres dynamiques](../../concepts/parameters.md#dynamic-parameters) dans la mesure où elles sont définies lors de l’affectation de la solution blueprint. Pour obtenir la liste complète ou paramètres d’artefact et leurs descriptions, consultez [table de paramètres d’artefact](#artifact-parameters-table).

1. Une fois que tous les paramètres ont été entrées, sélectionnez **affecter** en bas de la page. L’affectation de plan est créée et le début du déploiement de l’artefact. Le déploiement prend environ une heure. Pour vérifier l’état du déploiement, ouvrez l’affectation de plan.

> [!WARNING]
> Le service de plans d’Azure et les exemples de plan intégrés sont **gratuitement**. Ressources Azure sont [facturé par produit](https://azure.microsoft.com/pricing/). Utilisez le [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût d’exécution des ressources déployées par cet exemple de plan.

## <a name="artifact-parameters-table"></a>Tableau de paramètres d’artefact

Le tableau suivant fournit une liste du plan en paramètres d’artefact :

|Nom de l'artefact|Type d'artefact|Nom du paramètre|Description|
|-|-|-|-|
|Groupe de ressources Analytique de journal|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-workload-log-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources Analytique de journal|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle Log Analytics|Modèle Resource Manager|Niveau de service|Définit le niveau de l’espace de travail Analytique de journal. Valeur par défaut est _PerNode_.|
|Modèle Log Analytics|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle Log Analytics|Modèle Resource Manager|Lieu|Région utilisée pour la création de l’espace de travail Analytique de journal. Valeur par défaut est _ouest des États-Unis 2_.|
|Groupe de ressources réseau|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-workload-net-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources réseau|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle de groupe de sécurité réseau|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Adresse IP privée de pare-feu Azure|Configure l’adresse IP privée de le [pare-feu Azure](../../../../firewall/overview.md). Doit faire partie de la notation CIDR définie dans _ISO 27001 : Services partagés_ paramètre d’artefact **préfixe d’adresse de sous-réseau de pare-feu Azure**. Valeur par défaut est _10.0.4.4_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|ID d’abonnement de services partagés|Valeur utilisée pour activer l’homologation entre une charge de travail et les Services partagés.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d'adresse de réseau virtuel|La notation CIDR pour le réseau virtuel. Valeur par défaut est _10.1.0.0/16_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d’adresse de sous-réseau de valeur par défaut|La notation CIDR pour le sous-réseau du réseau virtuel par défaut. Valeur par défaut est _10.1.0.0/16_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Adresse IP d’AD DS|Adresse IP de la première machine virtuelle d’ajoute. Cette valeur est utilisée en tant que DNS VNET personnalisé.|
|Groupe de ressources Key Vault|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-workload-kv-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources Key Vault|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle Key Vault|Modèle Resource Manager|ID d'objet AAD|L’identificateur d’objet AAD du compte qui requiert l’accès à l’instance Key Vault. Sans valeur par défaut de valeur et ne peut pas être vide. Pour rechercher cette valeur à partir du portail Azure, recherchez et sélectionnez « Utilisateurs » sous _Services_. Utilisez le _nom_ boîte à filtrer pour le nom du compte, puis sélectionnez ce compte. Sur le _profil utilisateur_ , sélectionnez l’icône « Cliquez pour copier » à côté du _ID d’objet_.|
|Modèle Key Vault|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle Key Vault|Modèle Resource Manager|Référence SKU du coffre de clés|Spécifie la référence SKU du coffre de clés est créé. Valeur par défaut est _Premium_.|
|Modèle Key Vault|Modèle Resource Manager|Nom de l’utilisateur administrateur d’Azure SQL Server|Le nom d’utilisateur utilisé pour accéder à Azure SQL Server. Doit correspondre à même valeur de propriété dans **modèle de base de données SQL Azure**. Valeur par défaut est _sql-admin-user_.|
|Groupe de ressources de base de données SQL Azure|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-workload-azsql-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources de base de données SQL Azure|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle de base de données SQL Azure|Modèle Resource Manager|Nom de l’utilisateur administrateur d’Azure SQL Server|Nom d’utilisateur pour le serveur SQL Azure. Doit correspondre à même valeur de propriété dans **modèle de coffre de clés**. Valeur par défaut est _sql-admin-user_.|
|Modèle de base de données SQL Azure|Modèle Resource Manager|Azure SQL Server mot de passe administrateur (ID de ressource de coffre de clé)|L’ID de ressource du coffre de clés. Utilisez « / subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv » et remplacez `{subscriptionId}` avec votre ID d’abonnement et `{orgName}` avec la  **Nom de l’organisation** blueprint de paramètre.|
|Modèle de base de données SQL Azure|Modèle Resource Manager|Azure SQL Server mot de passe administrateur (nom de Secret de coffre de clé)|Nom d’utilisateur de l’instruction SQL administrateur du serveur. Doit correspondre à la valeur dans **modèle de coffre de clés** propriété **nom d’utilisateur administrateur de serveur SQL Azure**.|
|Modèle de base de données SQL Azure|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle de base de données SQL Azure|Modèle Resource Manager|ID d’objet administrateur AAD|ID d’objet AAD de l’utilisateur qui leur est affectée en tant qu’administrateur Active Directory. Sans valeur par défaut de valeur et ne peut pas être vide. Pour rechercher cette valeur à partir du portail Azure, recherchez et sélectionnez « Utilisateurs » sous _Services_. Utilisez le _nom_ boîte à filtrer pour le nom du compte, puis sélectionnez ce compte. Sur le _profil utilisateur_ , sélectionnez l’icône « Cliquez pour copier » à côté du _ID d’objet_.|
|Modèle de base de données SQL Azure|Modèle Resource Manager|Connexion d’administrateur AAD|Actuellement, les comptes Microsoft (comme live.com ou outlook.com) ne peut pas être définies en tant qu’administrateur. Seuls les utilisateurs et groupes de sécurité au sein de votre organisation peuvent être définies en tant qu’administrateur. Sans valeur par défaut de valeur et ne peut pas être vide. Pour rechercher cette valeur à partir du portail Azure, recherchez et sélectionnez « Utilisateurs » sous _Services_. Utilisez le _nom_ boîte à filtrer pour le nom du compte, puis sélectionnez ce compte. Sur le _profil utilisateur_ page, copiez le _nom d’utilisateur_.|
|Groupe de ressources App Service Environment|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-workload-ase-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources App Service Environment|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle d’application Service Environment|Modèle Resource Manager|Nom de domaine|Nom de l’annuaire Active Directory créé par l’exemple. Valeur par défaut est _contoso.com_.|
|Modèle d’application Service Environment|Modèle Resource Manager|Emplacement de l’ASE|Emplacement de l’environnement du Service application. Valeur par défaut est _ouest des États-Unis 2_.|
|Modèle d’application Service Environment|Modèle Resource Manager|Rétention des journaux Application Gateway en jours|Conservation des données en jours. Valeur par défaut est _365_.|

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez passé en revue les étapes pour déployer l’exemple de plan de charge de travail ISO 27001 App Service environnement/SQL Database, consultez les articles suivants pour en savoir plus sur l’architecture et le mappage de contrôle :

> [!div class="nextstepaction"]
> [Plan de charge de travail ISO 27001 App Service environnement/SQL Database - vue d’ensemble](./index.md)
> [plan de charge de travail ISO 27001 App Service environnement/SQL Database - mappage de contrôle](./control-mapping.md)

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).