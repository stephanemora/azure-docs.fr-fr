---
author: baanders
description: Fichier include relatif à la vue d’ensemble des étapes et prérequis des autorisations dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009644"
---
>[!NOTE]
>Ces opérations sont destinées à être effectuées par un utilisateur ayant le pouvoir de gérer à la fois les ressources et l’accès des utilisateurs sur l’abonnement Azure. Bien que certaines étapes puissent être effectuées avec des autorisations inférieures, la collaboration d’une personne disposant de ces autorisations sera nécessaire pour configurer complètement une instance utilisable. Pour plus d’informations à ce sujet, consultez la section [*Conditions préalables : autorisations requises*](#prerequisites-permission-requirements) section ci-dessous.

La configuration complète d’une nouvelle instance Azure Digital Twins se déroule en trois phases :
1. **Création de l’instance**
2. **Configuration d’autorisations d’accès utilisateur** : les utilisateurs Azure doivent avoir le rôle *Propriétaire Azure Digital Twins (préversion)* sur l’instance Azure Digital Twins pour pouvoir gérer celle-ci et ses données. Au cours de cette étape, en tant que Propriétaire/Administrateur de l’abonnement Azure, vous allez attribuer ce rôle à la personne qui doit gérer votre instance Azure Digital Twins. Il peut s’agir de vous-même ou d’une autre personne au sein de votre organisation.
3. **Configuration des autorisations d’accès pour les applications clientes** : Il est courant d’écrire une application cliente qui sera utilisée pour interagir avec votre instance. Pour que l’application cliente puisse accéder à votre service Azure Digital Twins, vous devez configurer une *inscription d’application* dans [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), que l’application cliente utilisera pour s’authentifier auprès de l’instance.

Pour poursuivre, vous allez avoir besoin d’un abonnement Azure. Vous pouvez en configurer un gratuitement [ici](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites-permission-requirements"></a>Configuration requise : Spécifications relatives aux autorisations

Pour être en mesure d’effectuer toutes les étapes décrites dans cet article, vous devez disposer d’un [rôle dans votre abonnement](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) qui dispose des autorisations suivantes :
* Créer et gérer des ressources Azure
* Gérer l’accès des utilisateurs aux ressources Azure (y compris l’octroi et la délégation des autorisations)

Les rôles communs qui répondent à cette exigence sont *Propriétaire*, *Administrateur de compte* ou la combinaison des rôles *Administrateur de l’accès utilisateur* et *Contributeur*. Pour obtenir une explication complète des rôles et des autorisations, notamment les autorisations qui sont incluses avec d’autres rôles, consultez l’article [*Rôles d’administrateur d’abonnement classique, rôles Azure et rôles Azure AD*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) dans la documentation relative à Azure RBAC.

Pour consulter votre rôle dans l’abonnement, accédez à la page [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure (vous pouvez utiliser ce lien ou rechercher *Abonnements* dans la barre de recherche du portail). Recherchez le nom de l’abonnement que vous utilisez et affichez votre rôle dans la colonne *Mon rôle* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Vue de la page Abonnements dans le portail Azure, affichant l’utilisateur en tant que propriétaire" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Si vous constatez que la valeur est *Contributeur*ou un autre rôle qui ne dispose pas des autorisations requises décrites ci-dessus, vous pouvez contacter l’utilisateur de votre abonnement qui *en dispose* (par exemple, le propriétaire de l’abonnement ou l’administrateur du compte) et procéder de l’une des façons suivantes :
* Demandez-lui d’effectuer les étapes de cet article en votre nom.
* Demandez qu’il élève votre rôle sur l’abonnement pour que vous disposiez des autorisations nécessaires pour continuer la procédure. Le fait que cela soit approprié dépend de votre organisation et de votre rôle au sein de celle-ci.