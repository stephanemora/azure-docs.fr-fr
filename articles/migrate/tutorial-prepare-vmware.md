---
title: Préparer des machines virtuelles VMware pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des machines virtuelles VMware avec Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 34bc62a9cb7e5d1358322500a8929b6f8b36d422
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454548"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure

Cet article vous aide à préparer l’évaluation de machines virtuelles VMware locales et/ou leur migration vers Azure à l’aide d’[Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers.


Ce tutoriel est le premier d’une série qui explique comment évaluer et migrer des machines virtuelles VMware. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure pour qu’il fonctionne avec Azure Migrate.
> * Préparez VMware pour l’évaluation des machines virtuelles.
> * Préparez VMware pour la migration des machines virtuelles.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils sont utiles lorsque vous apprenez à configurer un déploiement et comme preuve de concept rapide. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, passez en revue les procédures d’évaluation et de migration relatives à VMware.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

Vous devez disposer des autorisations suivantes.

**Tâche** | **autorisations**
--- | ---
**Créer un projet Azure Migrate** | Votre compte Azure doit être autorisé à créer un projet.
**Inscrire l’appliance Azure Migrate** | Azure Migrate utilise une appliance Azure Migrate légère pour évaluer les machines virtuelles VMware avec Azure Migrate Server Assessment et pour exécuter la [migration sans agent](server-migrate-overview.md) des machines virtuelles VMware avec Azure Migrate Server Migration. Cette appliance effectue la découverte des machines virtuelles et envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate.<br/><br/>Durant l’inscription, Azure Migrate crée deux applications Azure AD (Azure Active Directory) qui identifient cette appliance de façon unique et doit être autorisé à créer ces applications.<br/> - La première application communique avec les points de terminaison de service Azure Migrate.<br/> - La seconde application accède à un coffre de clés Azure Key Vault créé pendant l’inscription pour stocker les informations sur l’application Azure AD et les paramètres de configuration de l’appliance.
**Créer un coffre de clés** | Pour migrer des machines virtuelles VMware avec Azure Migrate Server Migration, Azure Migrate crée un coffre de clés afin de gérer les clés d’accès au compte de stockage de réplication de votre abonnement. Pour créer le coffre, vous devez disposer d’autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Pour inscrire l’appliance, vous affectez des autorisations pour permettre à Azure Migrate de créer les applications Azure AD durant l’inscription d’appliance. Les autorisations peuvent être affectées à l’aide de l’une des méthodes suivantes :

- L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

> [!NOTE]
> - Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
> - Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général ou le locataire peuvent octroyer des autorisations de la façon suivante :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**. Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorisations Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

### <a name="assign-role-assignment-permissions"></a>Affecter des autorisations d’attribution de rôle

Pour permettre à Azure Migrate de créer un coffre de clés, affectez des autorisations d’affectation de rôles comme suit :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.

    - Pour exécuter l’outil Server Assessment, les autorisations de **Contributeur** sont suffisantes.
    - Pour exécuter la migration de serveur sans agent, vous devez disposer des autorisations de **Propriétaire** (ou de **Contributeur** et d’**Administrateur de l’accès utilisateur**).

3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources.



## <a name="prepare-for-vmware-vm-assessment"></a>Préparer l’évaluation des machines virtuelles VMware

Pour préparer l’évaluation des machines virtuelles VMware, vous devez :

- **Vérifier les paramètres VMware**. Vérifiez que vCenter Server et les machines virtuelles que vous voulez migrer répondent aux exigences.
- **Configurer un compte d’évaluation**. Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation.
- **Vérifiez la configuration requise de l’appliance**. Vérifiez les exigences relatives au déploiement pour l’appliance Azure Migrate utilisée dans le cadre de l’évaluation.

### <a name="verify-vmware-settings"></a>Vérifier les paramètres VMware

1. [Vérifiez](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) les exigences relatives au serveur VMware pour l’évaluation.
2. [Vérifiez](migrate-support-matrix-vmware.md#assessment-port-requirements) que les ports nécessaires sont ouverts sur vCenter Server.


### <a name="set-up-an-account-for-assessment"></a>Configurer un compte pour l’évaluation

Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation et de migration sans agent.

- Si vous envisagez de découvrir des applications ou de visualiser des dépendances sans utiliser d’agent, créez un compte vCenter Server avec un accès en lecture seule, ainsi que des privilèges activés pour **Machines virtuelles** > **Opérations d’invité**.

  ![Privilèges du compte vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Si vous n’envisagez pas de découvrir des applications ni de visualiser des dépendances sans utiliser d’agent, configurez un compte en lecture seule pour vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Vérifier les paramètres de l’appliance pour l’évaluation

Vérifiez les exigences relatives à l’appliance avant de la déployer.

1. [Vérifiez](migrate-support-matrix-vmware.md#assessment-appliance-requirements) les exigences et les limitations relatives à l’appliance.
2. Si vous utilisez un proxy de pare-feu basé sur une URL, [passez en revue](migrate-support-matrix-vmware.md#assessment-url-access-requirements) les URL Azure auxquelles l’appliance doit accéder. Vérifiez que le proxy résout tous les enregistrements CNAME reçus durant la recherche des URL.
3. Passez en revue les [données de performance](migrate-appliance.md#collected-performance-data-vmware) et les [métadonnées](migrate-appliance.md#collected-metadata-vmware) que l’appliance va collecter pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-vmware.md#assessment-port-requirements) les ports auxquels l’appliance a accès.
5. Sur vCenter Server, vérifiez que votre compte dispose des autorisations nécessaires pour créer une machine virtuelle à l’aide d’un fichier OVA. Vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware à l’aide d’un fichier OVA.

Si vous utilisez un proxy de pare-feu basé sur des URL, autorisez l’accès aux [URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements) nécessaires.




## <a name="prepare-for-agentless-vmware-migration"></a>Préparer une migration VMware sans agent

Passez en revue les exigences relatives à la migration sans agent des machines virtuelles VMware.

1. [Vérifiez](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) les exigences des serveurs VMware.
2. Configurez un compte avec les [autorisations requises](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), de sorte qu’Azure Migrate puisse accéder à vCenter Server pour la migration sans agent à l’aide d’Azure Migrate Server Migration.
3. [Passez en revue](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) les exigences relatives aux machines virtuelles VMware à migrer vers Azure à l’aide de la migration sans agent.
4. [Passez en revue](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) les exigences relatives à l’utilisation de l’appliance Azure Migrate pour la migration sans agent.
5. Notez l’[accès aux URL](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) et l’[accès aux ports](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) dont l’appliance Azure Migrate a besoin pour la migration sans agent.


## <a name="prepare-for-agent-based-vmware-migration"></a>Préparer une migration VMware basée sur un agent

Passez en revue les exigences relatives à la [migration basée sur un agent](server-migrate-overview.md) des machines virtuelles VMware.

1. [Vérifiez](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) les exigences des serveurs VMware.
2. Configurez un compte avec les [autorisations requises](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions), de sorte qu’Azure Migrate puisse accéder à vCenter Server pour la migration basée sur un agent à l’aide d’Azure Migrate Server Migration.
3. [Passez en revue](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) les exigences relatives aux machines virtuelles VMware à migrer vers Azure à l’aide de la migration basée sur un agent, notamment l’installation de Mobility Service sur chaque machine virtuelle à migrer.
4. Notez l’[accès aux URL](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Passez en revue l’[accès aux ports](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) dont les composants Azure Migrate ont besoin pour l’accès basé sur un agent.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations Azure
> * Préparé VMware à l’évaluation et la migration


Passez au deuxième tutoriel afin de configurer un projet Azure Migrate et d’évaluer les machines virtuelles VMware pour une migration vers Azure.

> [!div class="nextstepaction"]
> [Évaluer les machines virtuelles VMware](./tutorial-assess-vmware.md)
