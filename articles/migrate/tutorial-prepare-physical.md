---
title: Préparer des serveurs physiques pour les évaluer et les migrer avec Azure Migrate
description: Découvrez comment préparer l’évaluation/la migration des serveurs physiques avec Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 363549662a17a87513c8426347909142ee405cae
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196395"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Préparer les serveurs physiques à une évaluation et à une migration vers Azure

Cet article explique comment préparer les serveurs physiques locaux à une évaluation et à une migration avec [Azure Migrate](migrate-services-overview.md).

[Azure Migrate](migrate-overview.md) fournit un hub d’outils qui vous permettent de découvrir, d’évaluer et de migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. Le hub comprend des outils Azure Migrate et des offres d’ISV (fournisseurs de logiciels indépendants) tiers. 

Ce tutoriel est le premier d’une série qui montre comment évaluer les serveurs physiques avec Azure Migrate. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparez Azure. Configurez des autorisations pour permettre à votre compte et vos ressources Azure de fonctionner avec Azure Migrate.
> * Préparez les serveurs physiques locaux à une évaluation.


> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les guides pratiques concernant l’évaluation des serveurs physiques.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure"></a>Préparer Azure

### <a name="azure-permissions"></a>Autorisations Azure

Vous devez définir des autorisations pour le déploiement Azure Migrate.

- Autorisations pour permettre à votre compte Azure de créer un projet Azure Migrate.
- Autorisations pour permettre à votre compte d’inscrire l’appliance Azure Migrate. L’appliance est utilisée pour la détection et la migration Hyper-V. Lors de l’inscription de l’appliance, Azure Migrate crée deux applications Azure Active Directory (Azure AD) qui identifient de façon unique l’appliance :
    - La première application communique avec les points de terminaison de service Azure Migrate.
    - La seconde application accède à un coffre de clés Azure Key Vault créé pendant l’inscription pour stocker les informations Azure AD App et les paramètres de configuration de l’appliance.



### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

Vérifiez que vous disposez des autorisations nécessaires pour créer un projet Azure Migrate.

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.


### <a name="assign-permissions-to-register-the-appliance"></a>Affecter des autorisations pour inscrire l’appliance

Vous pouvez affecter des autorisations pour permettre à Azure Migrate de créer les applications Azure AD durant l’inscription d’appliance, à l’aide de l’une des méthodes suivantes :

- L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

Il est intéressant de noter que :

- Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
- Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance. Vous pouvez supprimer les autorisations, une fois l’appliance configurée.


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général/locataire peut accorder des autorisations comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)


## <a name="prepare-for-physical-server-assessment"></a>Préparer les serveurs physiques à une évaluation

Pour préparer l’évaluation des serveurs physiques, vous devez vérifier les paramètres des serveurs physiques et les paramètres de déploiement des appliances :

### <a name="verify-physical-server-settings"></a>Vérifier les paramètres des serveurs physiques

1. Vérifiez la [configuration requise des serveurs physiques](migrate-support-matrix-physical.md#assessment-physical-server-requirements) pour l’évaluation.
2. Vérifiez que les [ports obligatoires](migrate-support-matrix-physical.md#assessment-port-requirements) sont ouverts sur les serveurs physiques.


### <a name="verify-appliance-settings"></a>Vérifier les paramètres de l’appliance

Avant de configurer l’appliance Azure Migrate et de commencer l’évaluation dans le tutoriel suivant, préparez le déploiement de l’appliance.

1. [Vérifiez](migrate-support-matrix-physical.md#assessment-appliance-requirements) la configuration requise de l’appliance.
2. [Passez en revue](migrate-support-matrix-physical.md#assessment-appliance-url-access) les URL Azure auxquelles l’appliance devra accéder.
3. Passez en revue les données que l’appliance va collecter pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-physical.md#assessment-port-requirements) les conditions d’accès aux ports pour l’appliance.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Configurer un compte pour la découverte des serveurs physiques

Azure Migrate a besoin d’autorisations pour découvrir les serveurs locaux.

- **Windows :** Configurez un compte d’utilisateur local sur tous les serveurs Windows que vous souhaitez inclure dans la détection. Le compte d’utilisateur doit être ajouté aux groupes suivants :       - Utilisateurs de gestion à distance       - Utilisateurs de l’Analyseur de performances       - Utilisateurs du journal de performances
- **Linux :** Vous devez disposer d’un compte racine sur les serveurs Linux que vous souhaitez découvrir.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Configuré les autorisations de compte Azure.
> * Serveurs physiques préparés pour l’évaluation.

Passez au tutoriel suivant pour créer un projet Azure Migrate et évaluer les serveurs physiques qui doivent faire l’objet d’une migration vers Azure.

> [!div class="nextstepaction"]
> [Évaluer des serveurs physiques](./tutorial-assess-physical.md)
