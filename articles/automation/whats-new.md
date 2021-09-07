---
title: Nouveautés d’Azure Automation
description: Chaque mois, Azure Automation fait l’objet de mises à jour importantes.
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 84644759a3f2f887662faae686814f032410266b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123107930"
---
# <a name="whats-new-in-azure-automation"></a>Nouveautés d’Azure Automation

Azure Automation bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues

Cette page est mise à jour tous les mois. Donc, consultez-la régulièrement. Si vous recherchez des éléments antérieurs aux six derniers mois, vous les trouverez dans l’[Archive des Nouveautés d’Azure Automation](whats-new-archive.md).

## <a name="august-2021"></a>Août 2021

### <a name="azure-policy-guest-configuration"></a>Azure Policy Guest Configuration

**Type :** Modification planifiée

Les clients doivent évaluer et planifier la migration depuis Azure Automation State Configuration vers la configuration d’invité d’Azure Policy. Pour plus d’informations, consultez [Configuration d’invité d’Azure Policy](../governance/policy/concepts/guest-configuration.md).

## <a name="july-2021"></a>Juillet 2021

### <a name="preview-support-for-user-assigned-managed-identity"></a>Prise en charge en préversion d’une identité managée affectée par l’utilisateur

**Type :** Nouvelle fonctionnalité

Azure Automation prend désormais en charge les [identités managées affectées par l’utilisateur](automation-secure-asset-encryption.md) pour les travaux cloud dans les régions Azure globales, Azure Government et Azure Chine. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/) pour plus d’informations.

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Disponibilité générale des clés gérées par le client pour Azure Automation

**Type :** Nouvelle fonctionnalité

Les clients peuvent gérer et sécuriser le chiffrement des ressources Azure Automation à l’aide de clés qu’ils gèrent eux-mêmes. Grâce à l’introduction des clés gérées par le client, vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide de clés que vous créez et gérez dans Azure Key Vault. Ce chiffrement supplémentaire doit vous aider à répondre aux besoins de votre organisation en matière de réglementation ou de conformité.

Pour plus d’informations, consultez [Utilisation de clés gérées par le client](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

## <a name="june-2021"></a>Juin 2021

### <a name="security-update-for-log-analytics-contributor-role"></a>Mise à jour de sécurité pour le rôle Contributeur Log Analytics

**Type :** Modification planifiée

Microsoft envisage de supprimer les droits de compte Automation du rôle Contributeur Log Analytics. Actuellement, le rôle [Contributeur Log Analytics](./automation-role-based-access-control.md#log-analytics-contributor) intégré peut élever les privilèges du rôle [Contributeur](./../role-based-access-control/built-in-roles.md#contributor) d’abonnement. Les comptes d’identification de compte Automation étant initialement configurés avec des droits Contributeur sur l’abonnement, ils peuvent être utilisés par un attaquant pour créer de nouveaux runbooks et exécuter du code en tant que Contributeur sur l’abonnement.

Compte tenu de ce risque de sécurité, nous vous recommandons de ne pas utiliser le rôle Contributeur Log Analytics pour exécuter des tâches Automation. Créez plutôt le rôle personnalisé Contributeur Azure Automation et utilisez-le pour les actions associées au compte Automation. Pour connaître les étapes d’implémentation, consultez [Rôle Contributeur Azure Automation personnalisé](./automation-role-based-access-control.md#custom-azure-automation-contributor-role).

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>Prise en charge d’Automation et de State Configuration disponible dans la région USA Ouest 3

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez [Résidence des données dans Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) et sélectionnez votre région dans la liste déroulante.

## <a name="may-2021"></a>Mai 2021

### <a name="startstop-vms-during-off-hours-v1"></a>Start/Stop VMs during off-hours (v1)

**Type :** Modification planifiée

Start/Stop VMs during off-hours (v1) sera déprécié le 21 mai 2022. Les clients doivent évaluer et planifier la migration vers Start/Stop VMs v2 (préversion). Pour plus d’informations, consultez [Vue d’ensemble de Start/Stop VMs v2](../azure-functions/start-stop-vms/overview.md) (préversion).

## <a name="april-2021"></a>Avril 2021

### <a name="support-for-update-management-and-change-tracking"></a>Pris en charge d’Update Management et de Change Tracking

**Type :** Nouvelle fonctionnalité

Le mappage des régions a été mis à jour pour prendre en charge Update Management et Change Tracking dans les régions Norvège Est, Émirats arabes unis Nord, USA Centre Nord, Brésil Sud et Corée Centre. Pour plus d’informations, consultez [Mappages pris en charge](./how-to/region-mappings.md#supported-mappings).

### <a name="support-for-system-assigned-managed-identities"></a>Prise en charge des identités managées affectées par le système

**Type :** Nouvelle fonctionnalité

Azure Automation prend désormais en charge les [identités managées affectées par le système](./automation-security-overview.md#managed-identities-preview) pour les travaux cloud et hybrides dans les régions Azure globales et Azure Government. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/) pour plus d’informations.

## <a name="march-2021"></a>Mars 2021

### <a name="new-azure-automation-built-in-policy-definitions"></a>Nouvelles définitions de stratégie intégrées Azure Automation

**Type :** Nouvelle fonctionnalité

Azure Automation a ajouté 5 nouvelles définitions de stratégie intégrées :

- Les comptes Automation doivent désactiver l’accès au réseau public
- Les comptes Azure Automation doivent utiliser des clés gérées par le client pour chiffrer les données au repos
- Configurer des comptes Azure Automation pour désactiver l'accès réseau public
- Configurer des connexions de point de terminaison privé sur des comptes Azure Automation
- Les connexions de point de terminaison privé sur les comptes Automation doivent être activées

Pour plus d’informations, consultez [Informations de référence sur Azure Policy](./policy-reference.md).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Inde Sud

**Type :** Nouvelle fonctionnalité

Utilisez la fonctionnalité Process Automation et State Configuration dans la région Inde Sud. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) pour plus d’informations.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Royaume-Uni Ouest

**Type :** Nouvelle fonctionnalité

Utilisez la fonctionnalité Process Automation et State Configuration dans la région Royaume-Uni Ouest. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Émirats arabes unis Centre

**Type :** Nouvelle fonctionnalité

Utilisez la fonctionnalités Process Automation et State Configuration dans la région Émirats arabes unis Centre. Lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) pour plus d’informations.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>Prise en charge d’Automation et de State Configuration disponible dans les régions Australie Centre 2, Norvège Ouest et France Sud

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez la page consacrée à la [résidence des données](https://azure.microsoft.com/global-infrastructure/data-residency/) en sélectionnant la zone géographique pour chaque région.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Nouveaux scripts ajoutés pour l’installation du Worker hybride sur Windows et Linux

**Type :** Nouvelle fonctionnalité

Deux nouveaux scripts ont été ajoutés au [dépôt GitHub](https://github.com/azureautomation) Azure Automation, qui résolvent l’un des principaux scénarios d’Azure Automation de configuration d’un Runbook Worker hybride sur une machine Windows ou Linux. Le script crée une machine virtuelle ou en utilise une existante, le cas échéant il crée un espace de travail Log Analytics, installe l’agent Log Analytics pour Windows ou pour Linux et inscrit la machine sur l’espace de travail Log Analytics. Le script Windows est nommé **Create Automation Windows HybridWorker**, et le script Linux **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Appeler un runbook par le biais d’un webhook de modèle Azure Resource Manager

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez [Utiliser un webhook à partir d’un modèle ARM](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template).

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Update Management prend désormais en charge Centos 8.x, Red Hat Enterprise Linux Server 8.x et SUSE Linux Enterprise Server 15

**Type :** Nouvelle fonctionnalité

Pour plus d’informations, consultez la [liste complète](./update-management/operating-system-requirements.md) des systèmes d’exploitation Linux pris en charge.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Prise en charge de la résidence des données dans la région, pour Brésil Sud et Asie Sud-Est

**Type :** Nouvelle fonctionnalité

Dans toutes les régions, à l’exception de Brésil Sud et d’Asie Sud-Est, les données Azure Automation sont stockées dans une région différente (région jumelée Azure) pour assurer la continuité d’activité et reprise d’activité (BCDR). Pour les régions Brésil Sud et Asie Sud-Est seulement, nous stockons maintenant les données Azure Automation dans la même région, afin de répondre aux exigences de résidence des données pour ces régions. Pour plus d’informations, consultez [Géoréplication dans Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation).

## <a name="february-2021"></a>Février 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Disponibilité générale déclarée de la prise en charge d’Automation et de State Configuration dans la région Japon Ouest

**Type :** Nouvelle fonctionnalité

Disponibilité du compte Automation et de State Configuration dans la région Japon Ouest. Pour plus d'informations, lisez cette [annonce](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introduction de la conformité du service Azure Policy personnalisé pour appliquer l’exécution de runbook sur un Worker hybride

**Type :** nouvelle fonctionnalité

Vous pouvez utiliser la nouvelle règle de conformité Azure Policy pour autoriser la création de travaux, de webhooks et de planifications de travaux à exécuter seulement sur des groupes de Workers hybrides.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Disponibilité d’Update Management dans les régions USA Est, France Centre et Europe Nord

**Type :** Nouvelle fonctionnalité

La fonctionnalité Update Management d’Automation est disponible dans les régions USA Est, France Centre et Europe Nord. Pour voir les mises à jour de la documentation en lien avec ce changement, consultez [Mappage de régions prises en charge](how-to/region-mappings.md).

## <a name="next-steps"></a>Étapes suivantes

Pour contribuer à la documentation Azure Automation, consultez le [guide du contributeur Docs](/contribute/).
