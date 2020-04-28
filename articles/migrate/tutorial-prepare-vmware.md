---
title: Préparer des machines virtuelles VMware pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des machines virtuelles VMware avec Azure Migrate.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677299"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure

Cet article vous aide à préparer l’évaluation de machines virtuelles VMware locales et/ou leur migration vers Azure à l’aide d’[Azure Migrate](migrate-services-overview.md).



Ce tutoriel est le premier d’une série qui explique comment évaluer et migrer des machines virtuelles VMware. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure pour qu’il fonctionne avec Azure Migrate.
> * Préparez VMware pour l’évaluation de machine virtuelle avec l’outil Azure Migrate : évaluation de serveur.
> * Préparez VMware pour la migration de machine virtuelle à l’aide de l’outil Azure Migrate : évaluation de serveur. 

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils sont utiles lorsque vous apprenez à configurer un déploiement et comme preuve de concept rapide. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

Pour pouvoir évaluer ou migrer des machines virtuelles VMware, vous avez besoin de ces autorisations pour ces tâches dans Azure.

**Tâche** | **Détails** 
--- | --- 
**Créer un projet Azure Migrate** | Votre compte Azure doit disposer d’autorisations Contributeur ou Propriétaire pour créer un projet. 
**Inscrire des fournisseurs de ressources** | Azure Migrate utilise une appliance Azure Migrate légère pour, d’une part, découvrir et évaluer les machines virtuelles VMware et, d’autre part, les migrer vers Azure avec Azure Migrate Server Assessment.<br/><br/> Lors de l’inscription d’appliances, les fournisseurs de ressources sont inscrits auprès de l’abonnement choisi dans l’appliance. [Plus d’informations](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Pour inscrire les fournisseurs de ressources, vous avez besoin d’un rôle Contributeur ou Propriétaire sur l’abonnement.
**Créer des applications Azure AD** | Lors de l’inscription de l’appliance, Azure Migrate crée des applications Azure AD (Azure Active Directory). <br/><br/> - La première application est utilisée pour la communication entre les agents exécutés sur l’appliance et leurs services respectifs exécutés sur Azure.<br/><br/> - La deuxième application est exclusivement utilisée pour accéder au coffre de clés créé dans l’abonnement de l’utilisateur pour la migration de machines virtuelles VMware sans agent. [Plus d’informations](migrate-appliance-architecture.md#appliance-registration)<br/><br/> Vous devez disposer d’autorisations pour créer des applications Azure AD (disponibles dans le rôle Développeur d’applications).
**Créer un coffre de clés** | Pour migrer des machines virtuelles VMware à l’aide d’une migration sans agent, Azure Migrate crée un coffre de clés pour gérer les clés d’accès au compte de stockage de réplication de votre abonnement.<br/><br/> Pour créer le coffre, vous devez disposer d’autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate.




### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Pour inscrire l’appliance, vous affectez des autorisations pour permettre à Azure Migrate de créer les applications Azure AD durant l’inscription d’appliance. Les autorisations peuvent être affectées à l’aide de l’une des méthodes suivantes :

- **Accorder des autorisations** : L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- **Attribuer le rôle de développeur d’applications** : L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

> [!NOTE]
> - Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
> - Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

Si vous souhaitez que le locataire/l’administrateur général accorde des autorisations, procédez comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**. Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorisations Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent également attribuer à un compte le rôle Développeur d’applications. [Découvrez-en plus](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) sur l’affectation d’un rôle.

### <a name="assign-permissions-to-create-a-key-vault"></a>Attribuer des autorisations pour créer un coffre Key Vault

Pour permettre à Azure Migrate de créer un coffre Key Vault, attribuez les autorisations suivantes :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.

    - Pour exécuter l’outil Server Assessment, les autorisations de **Contributeur** sont suffisantes.
    - Pour exécuter la migration de serveur sans agent, vous devez disposer des autorisations de **Propriétaire** (ou de **Contributeur** et d’**Administrateur de l’accès utilisateur**).

3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources.



## <a name="prepare-for-vmware-vm-assessment"></a>Préparer l’évaluation des machines virtuelles VMware

Pour préparer l’évaluation des machines virtuelles VMware, vous devez :

- **Vérifier les paramètres VMware**. Vérifiez que vCenter Server et les machines virtuelles que vous voulez migrer répondent aux exigences.
- **Configurer un compte pour l’évaluation**. Azure Migrate utilise ce compte pour accéder à vCenter Server afin de découvrir les machines virtuelles à des fins d’évaluation.
- **Vérifiez la configuration requise de l’appliance**. Vérifiez les exigences relatives au déploiement pour l’appliance Azure Migrate, avant de la déployer.

### <a name="verify-vmware-settings"></a>Vérifier les paramètres VMware

1. [Vérifiez](migrate-support-matrix-vmware.md#vmware-requirements) les exigences relatives au serveur VMware pour l’évaluation.
2. [Vérifiez](migrate-support-matrix-vmware.md#port-access) que les ports nécessaires sont ouverts sur vCenter Server.
3. Sur vCenter Server, vérifiez que votre compte dispose des autorisations nécessaires pour créer une machine virtuelle à l’aide d’un fichier OVA. Vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware à l’aide d’un fichier OVA.


### <a name="set-up-an-account-for-assessment"></a>Configurer un compte pour l’évaluation

Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation et de migration sans agent.

- Si vous envisagez de découvrir des applications ou de visualiser des dépendances sans utiliser d’agent, créez un compte vCenter Server avec un accès en lecture seule, ainsi que des privilèges activés pour **Machines virtuelles** > **Opérations d’invité**.

  ![Privilèges du compte vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Si vous n’envisagez pas de découvrir des applications ni de visualiser des dépendances sans utiliser d’agent, configurez un compte en lecture seule pour vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Vérifier les paramètres de l’appliance pour l’évaluation

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le prochain tutoriel, préparez le déploiement de l’appliance.

1. [Vérifiez ](migrate-appliance.md#appliance---vmware) les exigences pour l’appliance Azure Migrate.
2. Passez en revue les URL Azure auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
3. [Passez en revue les données](migrate-appliance.md#collected-data---vmware) que l’appliance collecte pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-vmware.md#port-access) les conditions d’accès aux ports pour l’appliance.




## <a name="prepare-for-agentless-vmware-migration"></a>Préparer une migration VMware sans agent

Passez en revue les exigences relatives à la [migration sans agent](server-migrate-overview.md) des machines virtuelles VMware.

1. [Vérifiez](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) les exigences des serveurs VMware.
2. [Passez en revue les autorisations](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) dont Azure Migrate a besoin pour accéder à vCenter Server.
3. [Vérifiez](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) les exigences des machines virtuelles VMware.
4. [Vérifiez](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) les exigences de l’appliance Azure Migrate.
5. Notez l’accès URL requis pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
6. Passez en revue les conditions d’[accès aux ports](migrate-support-matrix-vmware-migration.md#agentless-ports).

## <a name="prepare-for-agent-based-vmware-migration"></a>Préparer une migration VMware basée sur un agent

Passez en revue les exigences relatives à la [migration basée sur un agent](server-migrate-overview.md) des machines virtuelles VMware.

1. [Vérifiez](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) les exigences des serveurs VMware.
2. [Passez en revue les autorisations](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) dont Azure Migrate a besoin pour accéder à vCenter Server.
2. [Passez en revue](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) les exigences des machines virtuelles VMware, y compris l’installation du service Mobility sur chaque machine virtuelle que vous souhaitez migrer.
3. La migration basée sur un agent utilise une appliance de réplication :
    - [Passez en revue](migrate-replication-appliance.md#appliance-requirements) les exigences de déploiement pour l’appliance de réplication.
    - [Passez en revue les options](migrate-replication-appliance.md#mysql-installation) d’installation de MySQL sur l’appliance.
    - Notez l’accès URL requis pour les clouds [publics](migrate-replication-appliance.md#url-access) et du [secteur public](migrate-replication-appliance.md#azure-government-url-access).
    - Passez en revue les conditions d’[accès aux ports](migrate-replication-appliance.md#port-access) pour l’appliance de réplication.
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations Azure
> * Préparé VMware à l’évaluation et la migration


Passez au deuxième tutoriel afin de configurer un projet Azure Migrate et d’évaluer les machines virtuelles VMware pour une migration vers Azure.

> [!div class="nextstepaction"]
> [Évaluer les machines virtuelles VMware](./tutorial-assess-vmware.md)
