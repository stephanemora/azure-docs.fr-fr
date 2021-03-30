---
title: Guide de configuration accélérée de compte de laboratoire pour Azure Lab Services
description: Ce guide aide les administrateurs à configurer rapidement un compte de laboratoire pour une utilisation au sein de leur école.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669387"
---
# <a name="lab-account-setup-guide"></a>Guide de configuration de compte de laboratoire
Si vous êtes un administrateur, avant de configurer votre environnement Azure Lab Services, vous devez commencer par créer un *compte de laboratoire* au sein de votre abonnement Azure. Un compte de laboratoire est un conteneur pour laboratoires. Sa configuration ne prend que quelques minutes.

Ce guide comprend trois sections :
-  Prérequis
-  Planifier les paramètres de votre compte de laboratoire
-  Configurer votre compte de laboratoire

## <a name="prerequisites"></a>Prérequis
Les sections suivantes décrivent ce que vous devez faire avant de configurer un compte de laboratoire.


### <a name="access-your-azure-subscription"></a>Accéder à votre abonnement Azure
Pour créer un compte de laboratoire, vous devez avoir accès à un abonnement Azure configuré pour votre établissement scolaire. Votre établissement scolaire peut avoir un ou plusieurs abonnements. Un abonnement permet de gérer la facturation et la sécurité de l’ensemble de vos ressources et services Azure qui y sont utilisés, notamment les comptes de laboratoire.  Les abonnements Azure sont généralement gérés par votre service informatique.  Pour plus d’informations, consultez la section « Abonnement » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Estimer le nombre de machines virtuelles et les tailles de machines virtuelles dont vous avez besoin
Il est important de connaître le nombre de [machines virtuelles et les tailles de machines virtuelles](./administrator-guide.md#vm-sizing) dont votre laboratoire scolaire a besoin. 

Pour obtenir des conseils sur la structuration de vos laboratoires et images, consultez le billet de blog [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) (Passer d’un laboratoire physique à Azure Lab Services).

Pour obtenir des conseils supplémentaires sur la façon de structurer les laboratoires, consultez la section « Laboratoire » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Comprendre les limites de l’abonnement en termes de machines virtuelles et la capacité régionale en termes de machines virtuelles
Après avoir estimé le nombre de machines virtuelles et les tailles de machines virtuelles pour vos laboratoires, vous devez procéder comme suit :

- Assurez-vous que la limite de capacité de votre abonnement Azure autorise le nombre de machines virtuelles et la taille de machine virtuelle que vous prévoyez d’utiliser dans vos laboratoires.
- Créez votre compte de laboratoire dans une région disposant d’une capacité de machine virtuelle suffisante.

Pour plus d’informations, consultez [VM Subscription Limits and Regional Capacity](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553) (Limites de l’abonnement en termes de machines virtuelle et capacité régionale).

### <a name="decide-how-many-lab-accounts-to-create"></a>Déterminer le nombre de comptes de laboratoire à créer

Pour commencer rapidement, créez un seul compte de laboratoire dans son propre groupe de ressources.  Plus tard, vous pourrez créer des comptes de laboratoire et des groupes de ressources supplémentaires en fonction des besoins. Par exemple, vous pouvez avoir un compte de laboratoire et un groupe de ressources par service afin de clairement séparer les coûts. 

Pour plus d’informations sur les comptes de laboratoire, les groupes de ressources et la séparation des coûts, consultez les informations suivantes :
- section « Groupe de ressources » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#resource-group) ;
- section « Compte de laboratoire » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#lab-account) ; 
- [Gestion des coûts pour Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planifier les paramètres de votre compte de laboratoire

Pour planifier les paramètres de votre compte de laboratoire, posez-vous les questions suivantes.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Qui doivent être les propriétaires et contributeurs du compte de laboratoire ?

Les administrateurs informatiques de votre établissement scolaire endossent habituellement les rôles de propriétaire et de contributeur pour un compte de laboratoire. Ces rôles sont responsables de la gestion des stratégies qui s’appliquent à tous les laboratoires inclus dans le compte de laboratoire. La personne qui crée le compte de laboratoire est automatiquement un propriétaire. Vous pouvez ajouter des propriétaires et contributeurs supplémentaires à partir du locataire Azure Active Directory (AD) associé à votre abonnement. 

Pour plus d’informations sur les rôles propriétaire et contributeur de compte de laboratoire, consultez la section « Gérer l’identité » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Les utilisateurs du laboratoire ne voient qu’une liste des machines virtuelles auxquelles ils ont accès dans les locataires Azure AD dans Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Qui sera autorisé à créer les laboratoires ?

Vous pouvez décider de permettre à votre équipe informatique ou aux membres du corps enseignant de créer des laboratoires. Pour créer des laboratoires, vous leur attribuez le rôle Créateur de laboratoire dans le compte de laboratoire. Vous attribuez généralement ce rôle à partir du locataire Azure AD associé à votre abonnement scolaire. Quiconque crée un laboratoire est automatiquement désigné en tant que Propriétaire de celui-ci.  

Pour plus d’informations sur le rôle Créateur de laboratoire, consultez la section « Gérer l’identité » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Qui sera autorisé à posséder et à gérer les laboratoires ?

Vous pouvez également décider que votre service informatique et les enseignants possèdent/gèrent les laboratoires *sans* leur donner la possibilité de créer des laboratoires.  Dans ce cas, les utilisateurs issus du locataire Azure AD de votre abonnement se voient attribuer le rôle Propriétaire ou Contributeur pour les laboratoires existants.  

Pour plus d’informations sur les rôles Propriétaire et Contributeur de laboratoire, consultez la section « Gérer l’identité » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Voulez-vous enregistrer des images et les partager entre les laboratoires ?

Une galerie d’images partagées est un service que vous pouvez utiliser pour enregistrer et partager des images. Pour les classes qui ont besoin d’utiliser la même image, les créateurs de laboratoire peuvent créer l’image, puis l’exporter vers une galerie d’images partagées.  Une fois qu’une image est exportée vers la galerie d’images partagées, elle est utilisable pour créer de nouveaux laboratoires.

Vous pouvez créer vos images dans votre environnement physique, puis les importer dans une galerie d’images partagées. Pour plus d’informations, consultez le billet de blog [Importer une image personnalisée dans une galerie d’images partagées](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Si vous décidez d’utiliser le service Shared Image Gallery, vous devez créer ou attacher une galerie d’images partagées à votre compte de laboratoire. Vous pouvez différer cette décision pour l’instant, car vous pouvez attacher une galerie d’images partagées à un compte de laboratoire à tout moment.  

Pour plus d'informations, consultez les pages suivantes :
- section « Galerie d’images partagées » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#shared-image-gallery) ;
- section « Tarification » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#pricing).

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Quelles images de la Place de marché Azure vos laboratoires utiliseront-ils ?

La Place de marché Azure fournit des centaines d’images que vous pouvez activer afin que les créateurs de laboratoires puissent les utiliser pour créer leurs laboratoires. Certaines images peuvent inclure tout ce dont un laboratoire a déjà besoin. Dans d’autres cas, vous pouvez utiliser comme point de départ une image que le créateur de laboratoire pourra personnaliser en installant des applications ou outils supplémentaires.

Si vous ne savez pas de quelles images vous avez besoin, vous pouvez revenir ultérieurement pour les activer. La meilleure façon de voir quelles images sont disponibles consiste à commencer par créer un compte de laboratoire. Cela vous donne l’accès, afin que vous puissiez consulter la liste des images disponibles et leur contenu.  

Pour plus d’informations, consultez [Spécifier les images de la Place de Marché accessibles aux créateurs de laboratoire](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Les machines virtuelles du laboratoire doivent-elles avoir accès à d’autres ressources Azure ou locales ?

Quand vous configurez un compte de laboratoire, vous pouvez également l’appairer avec un réseau virtuel.  N’oubliez pas que votre réseau virtuel et votre compte de laboratoire doivent se trouver dans la même région.  Pour déterminer si vous devez appairer le compte avec un réseau virtuel, prenez en compte les scénarios suivants :

- **Accès à un serveur de licences**
  
   Lorsque vous utilisez des images de la Place de marché Azure, le coût de la licence du système d’exploitation est intégré à la tarification de Lab Services. Toutefois, vous n’avez pas besoin de fournir des licences pour le système d’exploitation lui-même. Pour les logiciels et applications supplémentaires installés, vous devez fournir une licence appropriée .  Pour accéder à un serveur de licences :
   - Vous pouvez choisir de vous connecter à un serveur de licences local.  La connexion à un serveur de licences local nécessite une configuration supplémentaire.
   - Une autre option, plus rapide à configurer, consiste à créer un serveur de licences que vous hébergez sur une machine virtuelle Azure.  La machine virtuelle Azure se trouve dans un réseau virtuel que vous appairez avec votre compte de laboratoire.

- **Accès à d’autres ressources locales, telles qu’un partage de fichiers ou une base de données**

   Vous créez généralement un réseau virtuel pour fournir l’accès à des ressources locales à l’aide d’une passerelle de réseau virtuel site à site. La configuration de ce type d’environnement prendra davantage de temps.

- **Accès à d’autres ressources Azure situées en dehors d’un réseau virtuel**

   Si vous avez besoin d’accéder à des ressources Azure qui ne sont *pas* sécurisées au sein d’un réseau virtuel, vous pouvez y accéder via l’Internet public sans devoir effectuer un peering.

   Pour plus d’informations sur les réseaux virtuels, consultez les informations suivantes :
   - section « Réseau virtuel » de [Notions de base de l’architecture dans Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network) ;
   - [Connecter votre réseau de laboratoire avec un réseau virtuel pair dans Azure Lab Services](./how-to-connect-peer-virtual-network.md) ;
   - [Créer un laboratoire avec une ressource partagée dans Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md).

## <a name="set-up-your-lab-account"></a>Configurer votre compte de laboratoire

Une fois que vous avez terminé la planification, vous êtes prêt à configurer votre compte de laboratoire. Vous pouvez appliquer les mêmes étapes à la configuration d’[Azure Lab Services dans Teams](./lab-services-within-teams-overview.md).

1. **Créez votre compte de laboratoire**. Pour obtenir des instructions, consultez [Créer un compte de laboratoire](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Pour plus d’informations sur les conventions d’affectation de noms, consultez la section « Affectation de noms » de [Azure Lab Services – Guide de l’administrateur](./administrator-guide.md#naming).

1. **Ajoutez des utilisateurs au rôle Créateur de laboratoire**. Pour obtenir des instructions, consultez [Ajouter des utilisateurs au rôle Créateur de laboratoire](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Connectez-vous à un réseau virtuel pair**. Pour plus d’instructions, consultez [Connecter votre réseau de laboratoire avec un réseau virtuel pair](./how-to-connect-peer-virtual-network.md).

   Vous devrez peut-être également consulter les instructions relatives à la [configuration de la plage d’adresses des machines virtuelles de laboratoire](./how-to-configure-lab-accounts.md).

1. **Activez et examinez les images**. Pour obtenir des instructions, consultez [Spécifier les images de la Place de marché Azure à la disposition des créateurs de laboratoire](./specify-marketplace-images.md).

   Pour examiner le contenu de chaque image de la Place de marché Azure, sélectionnez le nom de l’image. Par exemple, la capture d’écran suivante présente les détails de l’image Data Science Virtual Machine :

   ![Capture d’écran d’une liste d’images disponibles pour examen dans la Place de marché Azure.](./media/setup-guide/review-marketplace-images.png)

   Si une galerie d’images partagées est attachée à votre compte de laboratoire et que vous souhaitez activer le partage d’images personnalisées par les créateurs de laboratoire, procédez de la manière illustrée par la capture d’écran suivante :

   ![Capture d’écran d’une liste d’images personnalisées activées dans une galerie d’images partagées](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la configuration et la gestion des labos, consultez :

- [Gérer les comptes de laboratoire](how-to-manage-lab-accounts.md)  
- [Guide de configuration d’un laboratoire](setup-guide.md)
