---
title: Guide de configuration accélérée de compte de laboratoire pour Azure Lab Services
description: Ce guide aide les administrateurs à configurer rapidement un compte de laboratoire pour une utilisation au sein de leur école.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0855e59aced37e50c088cfe89ffeb3d0af9fcdca
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148279"
---
# <a name="lab-account-setup-guide"></a>Guide de configuration de compte de laboratoire

Dans un premier temps, les administrateurs doivent configurer un compte de laboratoire au sein de votre abonnement Azure. Un compte de laboratoire est un conteneur pour vos laboratoires de classe. Cette opération ne prend que quelques minutes.

## <a name="understand-your-schools-lab-account-requirements"></a>Comprendre les exigences du compte de laboratoire de votre établissement scolaire

Pour comprendre comment configurer votre compte de laboratoire en fonction des besoins de votre établissement scolaire, vous devez prendre en compte ces questions suivantes.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Ai-je accès à un abonnement Azure ?

Pour créer un compte de laboratoire, vous devez avoir accès à un abonnement Azure configuré pour votre établissement scolaire. Votre établissement scolaire peut avoir un ou plusieurs abonnements. Un abonnement permet de gérer la facturation et la sécurité de l’ensemble de vos ressources et services Azure qui y sont utilisés, notamment les comptes de laboratoire.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Combien de comptes de laboratoire doivent être créés ?

Pour une prise en main rapide, créez un seul compte de laboratoire, puis créez des comptes de laboratoire supplémentaires en fonction des besoins. Par exemple, vous pouvez éventuellement disposer d’un compte de laboratoire par service.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Qui doivent être les propriétaires et les contributeurs du compte de laboratoire ?

Les administrateurs sont généralement les propriétaires et les contributeurs d’un compte de laboratoire. Ils sont responsables de la gestion des stratégies qui s’appliquent à tous les laboratoires contenus dans le compte de laboratoire. La personne qui crée le compte de laboratoire en est automatiquement le propriétaire. Vous pouvez ajouter d’autres propriétaires et contributeurs, en général à partir du locataire Azure Active Directory (Azure AD) associé à votre abonnement. Cela peut être utile pour faciliter la gestion d’un compte de laboratoire en affectant le rôle propriétaire ou contributeur au niveau du compte de laboratoire.

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Les utilisateurs du labo ne voient qu’une seule liste des machines virtuelles auxquelles ils ont accès dans les locataires dans Azure Lab Services.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Qui sera autorisé à créer et gérer les laboratoires ?

Vous pouvez choisir de demander aux administrateurs et enseignants de créer et gérer des laboratoires. Ces utilisateurs (généralement issus du locataire Azure AD associé à votre abonnement) sont affectés au rôle Créateur de laboratoire dans le compte de laboratoire.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Souhaitez-vous autoriser les créateurs de laboratoires à enregistrer des images qui peuvent être partagées entre les laboratoires ?

Une galerie d’images partagées est un référentiel que vous pouvez utiliser pour enregistrer et partager des images. Si vous avez plusieurs classes qui ont besoin des mêmes images, les créateurs de laboratoire peuvent créer l’image une seule fois et la partager entre les laboratoires. Toutefois, pour commencer, vous n’avez pas nécessairement besoin d’une galerie d’images partagées, car vous pouvez toujours en ajouter une ultérieurement.

Si vous avez répondu « Oui » à cette question, vous devez créer ou attacher une galerie d’images partagées à votre compte de laboratoire. Si vous avez répondu « Je ne sais pas », vous pouvez repousser cette décision jusqu’à une date ultérieure.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Quelles images de la Place de marché Azure vos laboratoires de classe utiliseront-ils ?

La Place de marché Azure fournit des centaines d’images que vous pouvez activer afin que les créateurs de laboratoires puissent les utiliser pour créer leur laboratoire. Certaines images peuvent inclure tout ce dont un laboratoire a déjà besoin. Dans d’autres cas, vous pouvez utiliser une image comme point de départ, puis le créateur de laboratoire peut la personnaliser en installant des applications ou outils supplémentaires.

Si vous ne savez pas quelles images vous devrez utiliser, vous pouvez toujours y revenir ultérieurement pour les activer. En outre, la meilleure façon de voir quelles images sont disponibles consiste à créer d’abord un compte de laboratoire. Cela vous donne l’accès, afin que vous puissiez consulter la liste des images disponibles et leur contenu.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Les machines virtuelles du laboratoire doivent-elles avoir accès à d’autres ressources Azure ou locales ?

Quand vous configurez un compte de laboratoire, vous avez également la possibilité de le jumeler avec un réseau virtuel. Pour déterminer si cela est nécessaire, posez-vous les questions suivantes :

- **Avez-vous besoin de fournir l’accès à un serveur de licences ?**
  
   Si vous envisagez d’utiliser des images de la Place de marché Azure, le coût de la licence du système d’exploitation est intégré à la tarification de Lab Services. Vous n’avez donc pas besoin de fournir des licences pour le système d’exploitation lui-même. Toutefois, vous devrez fournir une licence appropriée pour les logiciels et applications supplémentaires installés.

- **Les machines virtuelles du laboratoire doivent-elles avoir accès à d’autres ressources locales, telles qu’un partage de fichiers ou une base de données ?**

   Vous créez un réseau virtuel pour fournir l’accès aux ressources locales, généralement à l’aide d’une passerelle de réseau virtuel de site à site. Si vous n’avez pas de réseau virtuel configuré, vous devez consacrer du temps supplémentaire à cette configuration.

- **Les machines virtuelles du laboratoire doivent-elles avoir accès à d’autres ressources Azure qui se trouvent sur un réseau virtuel ?**

   Si vous avez besoin d’accéder à des ressources Azure qui ne sont *pas* sécurisées au sein d’un réseau virtuel, vous pouvez accéder à ces ressources via l’Internet public sans effectuer de peering.

Si vous avez répondu « Oui » à une ou plusieurs questions, vous devez jumeler le compte de laboratoire à un réseau virtuel. Si vous avez répondu « Je ne sais pas », vous pouvez repousser cette décision jusqu’à une date ultérieure. Vous pouvez toujours choisir de jumeler un réseau virtuel après avoir créé le compte de laboratoire.

## <a name="set-up-your-lab-account"></a>Configurer votre compte de laboratoire

Une fois que vous comprenez les conditions requises pour votre compte de laboratoire, vous êtes prêt à le configurer.

1. **Créez votre compte de laboratoire.** Pour obtenir des instructions, reportez-vous au tutoriel sur la [création d’un compte de laboratoire](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account).

   Lorsque vous créez un compte de laboratoire, il peut s’avérer utile de vous familiariser avec les ressources Azure impliquées. Pour plus d’informations, consultez les articles suivants :

   - [Abonnement](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Groupe de ressources](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Compte de laboratoire](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratoire de classe](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Sélection d’une région et d’un emplacement](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Recommandations en matière d’affectation de noms pour ressources](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Ajoutez des utilisateurs au rôle Créateur de laboratoire.** Pour obtenir des instructions, consultez [Ajout d’utilisateurs au rôle Créateur de laboratoire](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   En outre, pour plus d’informations sur les différents rôles qui peuvent être attribués aux utilisateurs qui géreront les laboratoires et les comptes de laboratoire, consultez le [guide sur la gestion des identités](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Connectez-vous à un réseau virtuel de pair.** Pour plus d’instructions, consultez [Connecter le réseau de votre labo avec un réseau virtuel pair](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Vous devrez peut-être également consulter les instructions sur [la configuration de la plage d’adresses des machines virtuelles de laboratoire](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Activez et vérifiez les images.** Pour plus d’instructions, consultez [Activation d’images de la Place de marché Azure pour les créateurs de laboratoires](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Pour examiner le contenu de chaque image de la Place de marché Azure, sélectionnez le nom de l’image. Par exemple, reportez-vous à la capture d’écran suivante qui montre les détails de l’image Ubuntu Data Science VM :

   ![Capture d’écran de révision des images de la Place de marché Azure](./media/setup-guide/review-marketplace-images.png)

   Si une galerie d’images partagées est attachée à votre compte de laboratoire et que vous souhaitez activer le partage d’images personnalisées par les créateurs de laboratoire, effectuez les mêmes étapes que celles indiquées dans la capture d’écran suivante :

   ![Capture d’écran de l’activation des images personnalisées dans la galerie d’images partagées](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les comptes de laboratoire](how-to-manage-lab-accounts.md)

- [Guide de configuration du laboratoire de classe](setup-guide.md)
