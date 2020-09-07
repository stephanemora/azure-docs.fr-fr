---
title: Préparer des machines virtuelles VMware pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des machines virtuelles VMware avec Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8d4d6ac1149c397442a8ca7dd01f46f04ffc89b4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927304"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure

Cet article vous aide à préparer l’évaluation de machines virtuelles VMware locales et leur migration vers Azure en utilisant [Azure Migrate](migrate-services-overview.md).

Ce tutoriel est le premier d’une série qui explique comment évaluer et migrer des machines virtuelles VMware. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparez Azure pour qu’il fonctionne avec Azure Migrate.
> * Préparer l’évaluation de machines virtuelles VMware avec l’outil Azure Migrate : Évaluation de serveur.
> * Préparer la migration de machines virtuelles VMware à l’aide de l’outil Azure Migrate : Migration de serveur. 

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils sont utiles pour valider rapidement la faisabilité. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

Le tableau récapitule les tâches que vous devez effectuer dans Azure. Les instructions pour chaque tâche sont fournies après le tableau.

**Tâche** | **Détails** | **autorisations**
--- | --- | ---
**Créer un projet Azure Migrate** | Un projet Azure Migrate fournit un emplacement central pratique pour orchestrer et gérer les évaluations et les migrations à l’aide des outils Azure Migrate, des outils Microsoft et des outils tiers. | Votre compte Azure doit avoir des autorisations de Contributeur ou de Propriétaire dans le groupe de ressources où le projet réside.
**Inscrire l’appliance** | Azure Migrate utilise une appliance Azure Migrate légère pour détecter les machines virtuelles, les évaluer avec l’outil Évaluation de serveur et les migrer en utilisant la migration sans agent de l’outil Migration de serveur. [En savoir plus](migrate-appliance-architecture.md#appliance-registration) sur l’inscription. | Pour inscrire l’appliance, votre compte Azure doit avoir des autorisations de Contributeur ou de Propriétaire sur l’abonnement Azure.
**Créer des applications Azure AD** | Lors de l’inscription d’une appliance, Azure Migrate crée deux applications Azure Active Directory (Azure AD). <br/><br/> - La première application est utilisée pour la communication entre les agents exécutés sur l’appliance et Azure Migrate. <br/><br/> - La deuxième application est exclusivement utilisée pour accéder au coffre de clés créé dans l’abonnement de l’utilisateur pour la migration de machines virtuelles VMware sans agent.   | Votre compte Azure a besoin de ces [autorisations](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware#assign-permissions-to-create-azure-ad-apps) pour créer des applications Azure AD.
**Créer un coffre de clés** | - Le premier coffre Key Vault est créé dans le cadre de l’inscription de l’appliance et est utilisé pour la gestion du certificat téléchargé sur l’appliance lors de sa configuration. <br/><br/> \- Pour migrer des machines virtuelles VMware avec la méthode de migration sans agent, Azure Migrate crée un autre coffre Key Vault pour gérer les clés d’accès au compte de réplication dans votre abonnement.| Pour permettre à Azure Migrate de créer le coffre Key Vault, vous devez définir des autorisations (Propriétaire ou Contributeur et Administrateur de l’accès utilisateur) sur le groupe de ressources où réside le projet Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Attribuer des autorisations pour créer des applications Azure AD

Pour inscrire l’appliance, votre compte Azure doit avoir les autorisations pour créer des applications Azure AD. Attribuez les autorisations en choisissant l’une de ces deux méthodes :

- **Méthode 1 : Accorder les autorisations au compte** : l’administrateur général ou le locataire peuvent accorder des autorisations aux comptes d’utilisateur du locataire pour créer et inscrire des applications Azure AD.
- **Méthode 2 : Attribuer un rôle avec les autorisations à un compte d’utilisateur** : l’administrateur général ou le locataire peuvent attribuer au compte d’utilisateur le rôle Développeur d’applications (qui dispose des autorisations appropriées).

> [!NOTE]
> Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="method-1-grant-permissions-to-the-account"></a>Méthode 1 : Accorder les autorisations au compte

Accordez les autorisations au compte de la manière suivante :

1. Vérifiez que vous êtes administrateur général ou locataire. Ensuite, dans Azure AD, accédez à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. Définissez **Inscriptions d’applications** sur **Oui**. Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)

    ![Autorisations Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Méthode 2 : Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent également attribuer à un compte le rôle Développeur d’applications. [Découvrez-en plus](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) sur l’affectation d’un rôle.

### <a name="assign-permissions-to-create-a-key-vault"></a>Attribuer des autorisations pour créer un coffre Key Vault

Pour permettre à Azure Migrate de créer un coffre Key Vault, attribuez les autorisations suivantes :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.

    - Pour exécuter l’outil Server Assessment, les autorisations de **Contributeur** sont suffisantes.
    - Pour exécuter la migration de serveur sans agent, vous devez disposer des autorisations de **Propriétaire** (ou de **Contributeur** et d’**Administrateur de l’accès utilisateur**).

3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources.

## <a name="prepare-for-assessment"></a>Préparer pour l’évaluation

Pour préparer l’évaluation des machines virtuelles VMware, vous devez :

1. **Vérifier les paramètres VMware**. Vérifiez que vCenter Server et les machines virtuelles que vous voulez migrer répondent aux exigences.
2. **Configurez les autorisations pour l’évaluation**. Azure Migrate utilise un compte vCenter pour accéder à vCenter Server en vue de détecter et d’évaluer les machines virtuelles.
3. **Vérifiez la configuration requise de l’appliance**. Vérifiez les exigences relatives au déploiement de l’appliance Azure Migrate, avant de la déployer dans le cadre du tutoriel suivant.

### <a name="verify-vmware-settings"></a>Vérifier les paramètres VMware

1. [Vérifiez la configuration requise de VMware](migrate-support-matrix-vmware.md#vmware-requirements) pour l’évaluation.
2. [Vérifiez](migrate-support-matrix-vmware.md#port-access-requirements) que les ports nécessaires sont ouverts sur vCenter Server.
3. Sur vCenter Server, vérifiez que votre compte dispose des autorisations pour créer une machine virtuelle au moyen d’un fichier OVA. Cela est nécessaire quand vous déployez l’appliance Azure Migrate en tant que machine virtuelle VMware à l’aide d’un fichier OVA.
4. Certaines modifications doivent être apportées aux machines virtuelles afin qu’elles puissent être migrées vers Azure.

    - Pour certains systèmes d’exploitation, Azure Migrate effectue ces modifications automatiquement. 
    - Il est important d’effectuer ces modifications avant de commencer la migration. Si vous migrez la machine virtuelle avant d’effectuer la modification, elle risque de ne pas démarrer dans Azure.
    - Examinez les modifications à apporter aux machines [Windows](prepare-for-migration.md#windows-machines) et [Linux](prepare-for-migration.md#linux-machines).


### <a name="set-up-permissions-for-assessment"></a>Configurer les autorisations pour l’évaluation

Azure Migrate doit accéder à vCenter Server pour permettre à l’appliance Azure Migrate de détecter les machines virtuelles avant l’évaluation et la migration sans agent. Configurez un compte comme suit :

1. Dans le client web vSphere, ouvrez **Administration** > **Access** > **SSO Users and Groups** (Administration/Accès/Utilisateurs et groupes SSO).
2. Dans **Users** (Utilisateurs), cliquez sur l’icône **New User** (Nouvel utilisateur).
3. Entrez les informations relatives au nouvel utilisateur.
4. Sélectionnez les autorisations conformément aux valeurs du tableau.

    **Fonctionnalité** | **Autorisations du compte**
    --- | ---
    [Évaluer les machines virtuelles](tutorial-assess-vmware.md) | Pour l’évaluation, le compte a besoin d’un accès en lecture seule.
    [Détecter les applications, rôles et fonctionnalités des machines virtuelles](how-to-discover-applications.md) | Si vous souhaitez utiliser la détection d’application, le compte en lecture seule utilisé pour l’évaluation doit avoir des privilèges activés pour **Virtual machines** > **Guest Operations** (Machines virtuelles/Opérations d’invité).
    [Analyser les dépendances sur les machines virtuelles (sans agent)](how-to-create-group-machine-dependencies-agentless.md) | Si vous souhaitez analyser les dépendances, le compte en lecture seule utilisé pour l’évaluation doit avoir des privilèges activés pour **Virtual machines** > **Guest Operations** (Machines virtuelles/Opérations d’invité).
    
> [!NOTE]
> Si vous souhaitez limiter l’étendue de la détection des machines virtuelles pour l’évaluation, consultez [cet article](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>Vérifier les paramètres de l’appliance pour l’évaluation

Dans le [tutoriel suivant](tutorial-assess-vmware.md), vous configurerez l’appliance Azure Migrate et commencerez l’évaluation. Avant cela, vous devez vérifier la configuration requise de l’appliance comme suit : 

1. [Examinez la configuration requise](migrate-appliance.md#appliance---vmware) pour le déploiement de l’appliance Azure Migrate.
2. Passez en revue les URL Azure auxquelles l’appliance doit accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
3. [Identifiez](migrate-support-matrix-vmware.md#port-access-requirements) les ports utilisés par l’appliance.
4. [Examinez les données](migrate-appliance.md#collected-data---vmware) que l’appliance collecte pendant la détection et l’évaluation.

## <a name="prepare-for-agentless-vmware-migration"></a>Préparer une migration VMware sans agent

Vous pouvez migrer des machines virtuelles VMware à l’aide de la [migration sans agent ou migration basée sur un agent](server-migrate-overview.md). Cette section récapitule la configuration requise pour la migration sans agent.

1. [Déterminez](server-migrate-overview.md#compare-migration-methods) si la migration sans agent est appropriée dans votre cas.
2. [Vérifiez](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) la configuration requise de l’hyperviseur pour les machines que vous souhaitez migrer.
3. [Vérifiez](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) la configuration requise des machines virtuelles VMware à migrer à l’aide de la migration sans agent.
4. [Vérifiez](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) la configuration requise de l’appliance Azure Migrate pour la migration sans agent.
5. Notez l’accès URL requis pour les clouds [publics](migrate-appliance.md#public-cloud-urls) et du [secteur public](migrate-appliance.md#government-cloud-urls).
6. Passez en revue les conditions d’[accès aux ports](migrate-support-matrix-vmware-migration.md#port-requirements-agentless).
7. Configurez les autorisations pour la migration sans agent, comme cela est décrit dans la procédure suivante.


### <a name="assign-permissions-to-an-account"></a>Attribuer des autorisations à un compte

L’appliance Azure Migrate se connecte à vCenter Server pour détecter et migrer les machines virtuelles en utilisant la migration sans agent. Vous pouvez attribuer les autorisations requises par l’appliance au compte d’utilisateur, ou créer un rôle avec les autorisations et affecter ce rôle à un compte d’utilisateur.

1. Dans le client web vSphere, ouvrez **Administration** > **Access** > **SSO Users and Groups** (Administration/Accès/Utilisateurs et groupes SSO).
2. Dans **Users** (Utilisateurs), cliquez sur l’icône **New User** (Nouvel utilisateur).
3. Entrez les informations relatives au nouvel utilisateur.
4. Attribuez [ces autorisations](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless).

#### <a name="create-a-role-and-assign-to-an-account"></a>Créer un rôle et l’affecter à un compte

Vous pouvez également créer un compte. Ensuite, créez un rôle et affectez-le au compte de la manière suivante :

1. Connectez-vous au client web vSphere en tant qu’administrateur vCenter Server.
2. Sélectionnez l’instance du serveur vCenter >  **Créer un rôle**.
3. Spécifiez un nom de rôle, par exemple <em>Azure_Migrate</em>, puis affectez les [autorisations nécessaires ](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) au rôle.

    ![Privilèges du compte vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Maintenant, créez un compte et affectez-lui le rôle.

> [!NOTE]
> Si vous souhaitez limiter l’étendue de la détection des machines virtuelles pour la migration sans agent, consultez [cet article](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Préparer une migration VMware basée sur un agent

Vous pouvez migrer des machines virtuelles VMware à l’aide de la [migration sans agent ou migration basée sur un agent](server-migrate-overview.md). Cette section récapitule la configuration requise pour la migration basée sur un agent.

1. [Déterminez](server-migrate-overview.md#compare-migration-methods) si la migration basée sur un agent est appropriée dans votre cas.
1. [Vérifiez](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) la configuration requise de l’hyperviseur pour les machines que vous souhaitez migrer.
2. [Passez en revue](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) les exigences des machines virtuelles VMware, y compris l’installation du service Mobility sur chaque machine virtuelle que vous souhaitez migrer.
3. La migration basée sur un agent utilise une appliance de réplication :
    - [Passez en revue](migrate-replication-appliance.md#appliance-requirements) les exigences de déploiement pour l’appliance de réplication.
    - [Passez en revue les options](migrate-replication-appliance.md#mysql-installation) d’installation de MySQL sur l’appliance.
    - Notez l’accès URL requis pour les clouds [publics](migrate-replication-appliance.md#url-access) et du [secteur public](migrate-replication-appliance.md#azure-government-url-access).
    - Examinez les conditions d’[accès aux ports](migrate-replication-appliance.md#port-access) pour l’appliance de réplication.
4. Certaines modifications doivent être apportées aux machines virtuelles afin qu’elles puissent être migrées vers Azure. Examinez les modifications à apporter aux machines [Windows](prepare-for-migration.md#windows-machines) et [Linux](prepare-for-migration.md#linux-machines).



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations Azure
> * Préparé VMware à l’évaluation et la migration


Passez au deuxième tutoriel afin de configurer un projet Azure Migrate et d’évaluer les machines virtuelles VMware pour une migration vers Azure.

> [!div class="nextstepaction"]
> [Évaluer les machines virtuelles VMware](./tutorial-assess-vmware.md)
