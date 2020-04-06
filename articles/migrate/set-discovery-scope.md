---
title: Définir l’étendue de la découverte de machines virtuelles VMware avec Azure Migrate
description: Décrit comment définir l’étendue de la découverte pour l’évaluation et la migration de machines virtuelles VMware avec Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337351"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Définir l’étendue de la découverte des machines virtuelles VMware

Cet article explique comment limiter l’étendue de la découverte pour les machines virtuelles VMware découvertes par l’[appliance Azure Migrate](migrate-appliance-architecture.md).

L’appliance Azure Migrate découvre les machines virtuelles VMware locales dans les conditions suivantes : 

- Quand vous utilisez l’outil [Azure Migrate : Évaluation de serveurs](migrate-services-overview.md#azure-migrate-server-assessment-tool) pour évaluer les machines virtuelles VMware à migrer vers Azure.
- Quand vous utilisez l’outil [Azure Migrate : Migration de serveurs](migrate-services-overview.md#azure-migrate-server-migration-tool) pour la [migration sans agent](server-migrate-overview.md) des machines virtuelles VMware vers Azure.

## <a name="set-discovery-scope"></a>Définir l’étendue de la découverte


Une fois que vous avez configuré l’appliance Azure Migrate pour l’évaluation ou la migration de machines virtuelles VMware, l’appliance commence à découvrir les machines virtuelles et à envoyer les métadonnées des machines virtuelles à Azure. Avant de connecter l’appliance au serveur vCenter pour la découverte, vous pouvez définir l’étendue de la découverte pour limiter la détection à des centres de donnés vCenter Server, des clusters, un dossier de clusters, des hôtes, un dossier d’hôtes ou des machines virtuelles individuelles.

Pour définir l’étendue, vous affectez des autorisations sur le compte utilisé par Azure Migrate pour accéder au serveur vCenter.

## <a name="create-a-vcenter-user-account"></a>Créer un compte d’utilisateur vCenter

Si vous n’avez pas encore configuré un compte d’utilisateur vCenter utilisé par l’appliance Azure Migrate pour découvrir, évaluer et migrer les machines virtuelles VMware, faites-le en premier.

1.    Connectez-vous au client web vSphere en tant qu’administrateur de serveur vCenter.
2.    Sélectionnez **Administration** > **Utilisateurs et groupes d’authentification unique**, puis cliquez sur l’onglet **Utilisateurs**.
3.    Sélectionnez l’icône **Nouvel utilisateur**.
4.    Renseignez les informations relatives au nouvel utilisateur > **OK**.

Les autorisations de compte dépendent de ce que vous déployez.

**Fonctionnalité** | **Autorisations du compte**
--- | ---
[Évaluer](tutorial-assess-vmware.md)| Le compte a besoin d’un accès en lecture seule.
[Découvrir des applications/rôles/fonctionnalités](how-to-discover-applications.md) | Le compte a besoin d’un accès en lecture seule, avec des privilèges activés pour Machines virtuelles > Opérations d’invité.
[Analyser les dépendances (sans agent)](how-to-create-group-machine-dependencies-agentless.md) | Le compte a besoin d’un accès en lecture seule, avec des privilèges activés pour Machines virtuelles > Opérations d’invité.
[Migrer (sans agent)](tutorial-migrate-vmware.md) | Vous avez besoin d’un rôle auquel sont affectées les autorisations appropriées.<br/><br/> Pour créer un rôle, connectez-vous au client web vSphere en tant qu’administrateur du serveur vCenter. Sélectionnez l’instance du serveur vCenter >  **Créer un rôle**. Spécifiez un nom de rôle, par exemple <em>Azure_Migrate</em>, puis affectez les [autorisations nécessaires ](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) au rôle.


## <a name="assign-permissions-on-vcenter-objects"></a>Affecter des autorisations sur les objets vCenter

Vous pouvez affecter des autorisations sur les objets d’inventaire en utilisant une des deux méthodes suivantes :

- Attribuez un rôle avec les autorisations nécessaires au compte que vous utilisez pour tous les objets parents qui hébergent les machines virtuelles que vous voulez découvrir/migrer.
- Vous pouvez aussi attribuer le rôle et le compte d’utilisateur au niveau du centre de données et les propager aux objets enfants. Attribuez ensuite au compte un rôle **Aucun accès** pour chaque objet que vous ne voulez pas découvrir/migrer. Nous ne recommandons pas cette approche, car elle est fastidieuse et peut exposer des contrôles d’accès, chaque nouvel objet enfant créé héritant automatiquement de l’accès de son parent.

Pour attribuer un rôle au compte que vous utilisez pour tous les objets pertinents, procédez comme suit :

- **Pour l’évaluation** : Pour l’évaluation des machines virtuelles, appliquez le rôle **Lecture seule** au compte d’utilisateur vCenter pour tous les objets parents hébergeant des machines virtuelles que vous voulez découvrir. Objets parents inclus : hôte, dossier d’hôtes, cluster et dossier de clusters dans la hiérarchie, jusqu’au centre de données. Propagez ces autorisations aux objets enfants dans la hiérarchie.

    ![Affecter des autorisations](./media/tutorial-assess-vmware/assign-perms.png)

- **Pour la migration sans agent** : Pour la migration sans agent, appliquez un rôle défini par l’utilisateur avec les [autorisations nécessaires](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) au compte d’utilisateur pour tous les objets parents hébergeant des machines virtuelles que vous voulez découvrir. Vous pouvez nommer le rôle <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Prise en charge des étendues

Actuellement, l’outil Évaluation de serveurs ne peut pas découvrir les machines virtuelles si le compte vCenter s’est vu octroyer l’accès au niveau du dossier de machines virtuelles vCenter. Les dossiers d’hôtes et de clusters sont pris en charge.

Si vous voulez délimiter l’étendue de la découverte en fonction de dossiers de machines virtuelles, effectuez la procédure suivante pour vérifier que le compte vCenter dispose d’un accès en lecture seule affecté au niveau de la machine virtuelle.

1. Affectez des autorisations de lecture seule à toutes les machines virtuelles des dossiers de machines virtuelles auxquelles vous voulez délimiter la découverte.
2. Octroyez un accès en lecture seule à tous les objets parents qui hébergent des machines virtuelles.
    - Tous les objets parents (hôte, dossier d’hôtes, cluster, dossier de clusters) de la hiérarchie jusqu’au centre de données sont inclus.
    - Vous n’avez pas besoin de propager les autorisations à tous les objets enfants.
3. Utilisez les informations d’identification pour la détection en sélectionnant le centre de données en tant qu’**étendue de collection**. La configuration du contrôle d’accès en fonction du rôle garantit que le compte d’utilisateur vCenter correspondant a accès seulement aux machines virtuelles spécifiques d’un locataire.


## <a name="next-steps"></a>Étapes suivantes

[Configurez l’appliance](how-to-set-up-appliance-vmware.md) et [démarrez la découverte en continu](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
