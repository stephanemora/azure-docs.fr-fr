---
title: Exemple - ISO 27001 Shared Services blueprint - déployer des étapes
description: Déployer des étapes de l’exemple de solution blueprint ISO 27001 Shared Services.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f49951d0a2ec738df9946edc7f44820c2cde975f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804330"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Déployer l’exemple de solution blueprint ISO 27001 Shared Services

Pour déployer l’exemple de plan Azure plans ISO 27001 Shared Services, les étapes suivantes doivent être prises :

> [!div class="checklist"]
> - Créer un nouveau plan à partir de l’exemple
> - Marquez la copie de l’exemple en tant que **Publié**
> - Affecter votre copie du blueprint à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="create-blueprint-from-sample"></a>Créer un plan à partir de l’exemple

Tout d’abord, implémentez l’exemple de plan en créant un nouveau plan dans votre environnement à l’aide de l’exemple comme un starter.

1. Sélectionnez **tous les services** et recherchez et sélectionnez **stratégie** dans le volet gauche. Sur le **stratégie** page, sélectionnez **plans**.

1. Dans la page **Démarrage**à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Rechercher le **ISO 27001 : Services partagés** exemple de plan sous _autres exemples_ et sélectionnez **utiliser cet exemple**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :

   - **Nom du blueprint** : Fournissez un nom pour votre copie de l’exemple de solution blueprint ISO 27001 Shared Services.
   - **Emplacement de la définition** : Utiliser les points de suspension, puis sélectionnez le groupe d’administration pour enregistrer votre copie de l’échantillon.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Passez en revue la liste des artefacts qui composent l’exemple de blueprint. La plupart des artefacts ont des paramètres que nous allons définir ultérieurement. Sélectionnez **Enregistrer comme brouillon** lorsque vous avez terminé de passer en revue l’exemple de blueprint.

## <a name="publish-the-sample-copy"></a>Publier la copie de l’exemple

Votre copie de l’exemple de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée. La copie de l’exemple de solution blueprint peut être personnalisée à votre environnement et les besoins, mais cette modification peut le déplacer en dehors de la norme ISO 27001.

1. Sélectionnez **tous les services** et recherchez et sélectionnez **stratégie** dans le volet gauche. Sur le **stratégie** page, sélectionnez **plans**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de plan, puis sélectionnez-le.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans la nouvelle page sur la droite, fournissez un **Version** pour votre copie de l’exemple de plan. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Fournir **modifier les remarques** telles que « première version publiés à partir de l’exemple de solution blueprint 27001 ISO ». Sélectionnez ensuite **Publier** en bas de la page.

## <a name="assign-the-sample-copy"></a>Affecter la copie de l’exemple

Une fois que la copie de l’exemple de blueprint a été **publiée**, elle peut être affectée à un abonnement dans le groupe d’administration où elle a été enregistrée. C’est à cette étape que les paramètres sont fournis pour que chaque déploiement de la copie de l’exemple de blueprint soit unique.

1. Sélectionnez **tous les services** et recherchez et sélectionnez **stratégie** dans le volet gauche. Sur le **stratégie** page, sélectionnez **plans**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de plan, puis sélectionnez-le.

1. Sélectionnez **Affecter le blueprint** dans le haut de la page.

1. Indiquez les valeurs des paramètres pour l’affectation du blueprint :

   - Concepts de base

     - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui font partie du groupe d’administration où vous avez enregistré votre copie de l’exemple de blueprint. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chacun d’eux à l’aide des paramètres saisis.
     - **Nom de l’affectation** : Le nom est prérempli pour vous en fonction du nom de la solution blueprint.
       Modifier en fonction des besoins, ou laissez la valeur actuelle.
     - **Emplacement** : Sélectionnez une région dans laquelle créer l’identité managée. Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté. Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version de définition du blueprint** : Choisir un **publié** version de votre copie de l’exemple de plan.

   - Verrouiller l'affectation

     Sélectionnez le paramètre pour votre environnement de verrouillage de plan. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../../concepts/resource-locking.md).

   - Identité managée

     Laissez la valeur par défaut _système affecté_ gérés option d’identité.

   - Paramètres de blueprint

     Les paramètres définis dans cette section sont utilisés par la plupart des artefacts dans la définition de plan pour assurer la cohérence.

     - **Nom de l’organisation**: Entrez un nom court pour votre organisation. Cette propriété est principalement utilisée pour nommer des ressources.
     - **Préfixe d’adresse de sous-réseau de services partagés**: Pour la mise en réseau les ressources déployées ensemble, fournissent la valeur de la notation CIDR.
     - **Emplacement de services partagé**: Détermine que les artefacts sont déployés à l’emplacement. Tous les services sont disponibles dans tous les emplacements. Artefacts de déploiement de ces services constituent une option de paramètre pour l’emplacement de déploiement de cet artefact à.
     - **Emplacement autorisé (stratégie : Solution Blueprint initiative de la norme ISO 27001)**: Valeur qui indique les emplacements autorisés pour les groupes de ressources.
     - **Ouvrez une session espace de travail Analytique pour les agents de machine virtuelle (stratégie : Solution Blueprint initiative de la norme ISO 27001)**: Spécifie l’ID de ressource d’un espace de travail. Ce paramètre utilise un `concat` fonction permettant de construire l’ID de ressource.

   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont des [paramètres dynamiques](../../concepts/parameters.md#dynamic-parameters) puisqu’ils sont définis lors de l’affectation du blueprint. Pour obtenir la liste complète ou paramètres d’artefact et leurs descriptions, consultez [table de paramètres d’artefact](#artifact-parameters-table).

1. Une fois tous les paramètres entrés, sélectionnez **Affecter** au bas de la page. L’affectation de plan est créée et le début du déploiement de l’artefact. Le déploiement prend environ une heure. Pour vérifier l’état du déploiement, ouvrez l’affectation de plan.

> [!WARNING]
> Le service de plans d’Azure et les exemples de plan intégrés sont **gratuitement**. Ressources Azure sont [facturé par produit](https://azure.microsoft.com/en-us/pricing/). Utilisez le [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût d’exécution des ressources déployées par cet exemple de plan.

## <a name="artifact-parameters-table"></a>Tableau de paramètres d’artefact

Le tableau suivant fournit une liste du plan en paramètres d’artefact :

|Nom de l’artefact|Type d’artefact|Nom du paramètre|Description|
|-|-|-|-|
|[Préversion] : Déployer l’Agent d’Analytique de journal pour Linux VM Scale Sets (VMSS)|Affectation de rôle|Facultatif : Liste des images de machine virtuelle pris en charge du système d’exploitation Linux pour ajouter à la portée|(Facultatif) Valeur par défaut est _[« none »]_.|
|[Préversion] : Déployer Log Analytics Agent pour les machines virtuelles Linux|Affectation de rôle|Facultatif : Liste des images de machine virtuelle pris en charge du système d’exploitation Linux pour ajouter à la portée|(Facultatif) Valeur par défaut est _[« none »]_.|
|[Préversion] : Déployer l’Agent d’Analytique de journal pour les machines virtuelles Windows identiques (VMSS)|Affectation de rôle|Facultatif : Liste des images de machine virtuelle pris en charge du système d’exploitation Windows à ajouter à l’étendue|(Facultatif) Valeur par défaut est _[« none »]_.|
|[Préversion] : Déployer Log Analytics Agent pour les machines virtuelles Windows|Affectation de rôle|Facultatif : Liste des images de machine virtuelle pris en charge du système d’exploitation Windows à ajouter à l’étendue|(Facultatif) Valeur par défaut est _[« none »]_.|
|Types de ressources autorisés|Affectation de rôle|Types de ressources autorisés|Liste des types de ressources autorisés à être déployé. Cette liste est composée de tous les types de ressources déployées dans les Services partagés.|
|Références SKU de compte de stockage autorisées|Affectation de rôle|Autorisée de références (SKU) de stockage|Liste de diagnostic consigne autorisé de références SKU de compte de stockage. Valeur par défaut est _[« Standard_LRS »]_.|
|Références SKU de machine virtuelle autorisées|Affectation de rôle|Liste des références SKU de machine virtuelle autorisés à être déployé. Valeur par défaut est _[« Standard_DS1_v2 », « Standard_DS2_v2 »]_.|
|Initiative de blueprint pour ISO 27001|Affectation de rôle|Types de ressources pour auditer les journaux de diagnostic|Liste des types de ressources pour vérifier si le journal de diagnostic n’est pas activé. Vous trouverez les valeurs acceptables dans [schémas de journaux de diagnostic d’Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Groupe de ressources Analytique de journal|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-sharedsvsc-log-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources Analytique de journal|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle Log Analytics|Modèle Resource Manager|Niveau de service|Définit le niveau de l’espace de travail Analytique de journal. Valeur par défaut est _PerNode_.|
|Modèle Log Analytics|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle Log Analytics|Modèle Resource Manager|Lieu|Région utilisée pour la création de l’espace de travail Analytique de journal. Valeur par défaut est _ouest des États-Unis 2_.|
|Groupe de ressources réseau|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-sharedsvcs-net-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources réseau|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle de Pare-feu Azure|Modèle Resource Manager|Adresse IP privée de pare-feu Azure|Configure l’adresse IP privée de le [pare-feu Azure](../../../../firewall/overview.md). Cette valeur est également utilisée comme table de routage par défaut sur le sous-réseau de services partagés. Doit faire partie de la notation CIDR définie dans **préfixe d’adresse de sous-réseau de pare-feu Azure**. Valeur par défaut est _10.0.4.4_.|
|Modèle de Pare-feu Azure|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle de groupe de sécurité réseau|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d'adresse de réseau virtuel|La notation CIDR pour le réseau virtuel. Valeur par défaut est _10.0.0.0/16_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Activer la protection DDoS de réseau virtuel|Configure le service protection DDoS pour le réseau virtuel. Valeur par défaut est _true_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Partagé le préfixe d’adresse de sous-réseau de Services|La notation CIDR pour le sous-réseau de Services partagés. Valeur par défaut est _10.0.0.0/24_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d'adresse de sous-réseau DMZ|La notation CIDR pour le sous-réseau de réseau de périmètre. Valeur par défaut est _10.0.1.0/24_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d'adresse de sous-réseau Application Gateway|La notation CIDR pour le sous-réseau de passerelle d’application. Valeur par défaut est _10.0.2.0/24_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d'adresse de sous-réseau de passerelle de réseau virtuel|La notation CIDR pour le sous-réseau de passerelle de réseau virtuel. Valeur par défaut est _10.0.3.0/24_.|
|Modèle de réseau virtuel et de table de routage|Modèle Resource Manager|Préfixe d'adresse de sous-réseau de Pare-feu Azure|La notation CIDR pour le [pare-feu Azure](../../../../firewall/overview.md) sous-réseau. Doit inclure le **adresse IP privée du pare-feu Azure** paramètre.|
|Groupe de ressources Key Vault|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-sharedsvcs-kv-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources Key Vault|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle Key Vault|Modèle Resource Manager|Nom d'utilisateur de l'administrateur Jumpbox|Nom d’utilisateur pour le serveur de rebond. Doit correspondre à même valeur de propriété dans **modèle de serveur de rebond**. Valeur par défaut est _jb-admin-user_.|
|Modèle Key Vault|Modèle Resource Manager|Mot de passe ou clé SSH de l'administrateur Jumpbox|Clé ou mot de passe pour le compte sur le serveur de rebond. Doit correspondre à même valeur de propriété dans **modèle de serveur de rebond**. Sans valeur par défaut de valeur et ne peut pas être vide.|
|Modèle Key Vault|Modèle Resource Manager|Nom d'utilisateur de l'administrateur de domaine|Nom d’utilisateur utilisé pour accéder à Active Directory machine virtuelle et pour joindre d’autres machines virtuelles à un domaine. Doit correspondre à **utilisateur administrateur de domaine** valeur de propriété dans **modèle de Services de domaine Active Directory**. Valeur par défaut est _-utilisateur administrateur de domaine_.|
|Modèle Key Vault|Modèle Resource Manager|Mot de passe d'administrateur de domaine|Mot de passe de l’utilisateur administrateur de domaine. Sans valeur par défaut de valeur et ne peut pas être vide.|
|Modèle Key Vault|Modèle Resource Manager|ID d'objet AAD|L’identificateur d’objet AAD du compte qui requiert l’accès à l’instance Key Vault. Sans valeur par défaut de valeur et ne peut pas être vide. Pour rechercher cette valeur à partir du portail Azure, recherchez et sélectionnez « Utilisateurs » sous _Services_. Utilisez le _nom_ boîte à filtrer pour le nom du compte, puis sélectionnez ce compte. Sur le _profil utilisateur_ , sélectionnez l’icône « Cliquez pour copier » à côté du _ID d’objet_.  |
|Modèle Key Vault|Modèle Resource Manager|Conservation du journal en jours|Conservation des données en jours. Valeur par défaut est _365_.|
|Modèle Key Vault|Modèle Resource Manager|Référence SKU du coffre de clés|Spécifie la référence SKU du coffre de clés est créé. Valeur par défaut est _Premium_.|
|Groupe de ressources de serveur de rebond|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-sharedsvcs-jb-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources de serveur de rebond|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle Jumpbox|Modèle Resource Manager|Nom d'utilisateur de l'administrateur Jumpbox|Le nom d’utilisateur permettant d’accéder aux machines virtuelles du serveur de rebond. Doit correspondre à même valeur de propriété dans **modèle de coffre de clés**. Valeur par défaut est _jb-admin-user_.|
|Modèle Jumpbox|Modèle Resource Manager|Mot de passe administrateur du serveur de rebond (ID de ressource de coffre de clé)|L’ID de ressource du coffre de clés. Utilisez « / subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv » et remplacez `{subscriptionId}` avec votre ID d’abonnement et `{orgName}` avec la  **Nom de l’organisation** blueprint de paramètre.|
|Modèle Jumpbox|Modèle Resource Manager|Mot de passe administrateur du serveur de rebond (nom de Secret de coffre de clé)|Nom d’utilisateur de l’administrateur de serveur de rebond. Doit correspondre à la valeur dans **modèle de coffre de clés** propriété **nom d’utilisateur administrateur de serveur de rebond**.|
|Modèle Jumpbox|Modèle Resource Manager|Système d'exploitation Jumpbox|Détermine le système d’exploitation de la machine virtuelle jumpbox. Valeur par défaut est _Windows_.|
|Groupe de ressources Active Directory Domain Services|Groupe de ressources|Nom|**Verrouillé** -concatène le **Nom_organisation** avec `-sharedsvcs-adds-rg` pour rendre le groupe de ressources unique.|
|Groupe de ressources Active Directory Domain Services|Groupe de ressources|Lieu|**Verrouillé** -utilise le paramètre de plan.|
|Modèle Active Directory Domain Services|Modèle Resource Manager|Nom d'utilisateur de l'administrateur de domaine|Nom d’utilisateur pour le serveur de rebond ADDS. Doit correspondre à même valeur de propriété dans **modèle de coffre de clés**. Valeur par défaut est _utilisateur administrateur ajoute_.|
|Modèle Active Directory Domain Services|Modèle Resource Manager|Mot de passe administrateur domaine (ID de ressource de coffre de clé)|L’ID de ressource du coffre de clés. Utilisez « / subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv » et remplacez `{subscriptionId}` avec votre ID d’abonnement et `{orgName}` avec la  **Nom de l’organisation** blueprint de paramètre.|
|Modèle Active Directory Domain Services|Modèle Resource Manager|Mot de passe administrateur domaine (nom de Secret de coffre de clé)|Nom d’utilisateur de l’administrateur de domaine. Doit correspondre à la valeur dans **modèle de coffre de clés** propriété **nom d’utilisateur administrateur de domaine**.|
|Modèle Active Directory Domain Services|Modèle Resource Manager|Nom de domaine|Nom de l’annuaire Active Directory créé par l’exemple. Valeur par défaut est _contoso.com_.|
|Modèle Active Directory Domain Services|Modèle Resource Manager|Utilisateur administrateur de domaine|Nom d’utilisateur pour le compte d’administrateur AD et de jonction des périphériques au domaine AD. Doit correspondre à **AD admin username** valeur de propriété dans **modèle de coffre de clés**. Valeur par défaut est _-utilisateur administrateur de domaine_.|
|Modèle Active Directory Domain Services|Modèle Resource Manager|Mot de passe d'administrateur de domaine|Définissez les détails de Key Vault pour stocker le mot de passe. Sans valeur par défaut de valeur et ne peut pas être vide.|

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez passé en revue les étapes pour déployer l’exemple de solution blueprint ISO 27001 Shared Services, consultez les articles suivants pour en savoir plus sur l’architecture et le mappage de contrôle :

> [!div class="nextstepaction"]
> [Plan de ISO 27001 Shared Services - vue d’ensemble](./index.md)
> [plan de la norme ISO 27001 Shared Services - mappage de contrôle](./control-mapping.md)

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).