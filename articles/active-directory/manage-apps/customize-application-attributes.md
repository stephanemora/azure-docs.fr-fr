---
title: Personnalisation des mappages d’attributs Azure AD | Microsoft Docs
description: Découvrez ce que sont les mappages d’attributs pour les applications SaaS dans Azure Active Directory et comment les modifier pour répondre aux besoins de votre entreprise.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1926849c8ec63b4240d951e46b1341f31f7c5bd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170344"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personnalisation des mappages d’attributs d’attribution d’utilisateurs pour les applications SaaS dans Azure Active Directory
Microsoft Azure AD prend en charge l’approvisionnement d’utilisateurs pour les applications SaaS tierces telles que Salesforce, Google Apps et autres. Si vous avez activé l’attribution d’utilisateurs pour une application SaaS tierce, le portail Azure contrôle ses valeurs d’attributs sous forme de mappages d’attributs.

Il existe un ensemble préconfiguré d’attributs et de mappages d’attributs entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets en plus des utilisateurs, tels que des groupes. <br> 
 Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre organisation. Cela signifie que vous pouvez modifier ou supprimer des mappages d’attributs existants ou en créer de nouveaux.
 
## <a name="editing-user-attribute-mappings"></a>Modification des mappages d’attributs utilisateur

Dans le portail Azure AD, vous pouvez accéder à cette fonctionnalité en cliquant sur une configuration **Mappages** sous **Approvisionnement**, dans la section **Gérer** d’une **Application d’entreprise**.


![Salesforce](./media/customize-application-attributes/21.png) 

En cliquant sur la configuration **Mappages**, vous ouvrez l’écran **Mappage d’attributs** associé. Il existe des applications SaaS qui nécessitent certains mappages d’attributs pour fonctionner correctement. Pour les attributs requis, la fonctionnalité **Supprimer** n’est pas disponible.


![Salesforce](./media/customize-application-attributes/22.png)

Dans l’exemple ci-dessus, vous pouvez voir que l’attribut **Nom d’utilisateur** d’un objet géré dans Salesforce est renseigné avec la valeur **userPrincipalName** de l’objet Azure Active Directory lié.

Vous pouvez personnaliser les **mappages d’attributs** existants en cliquant dessus. Cette opération ouvre l’écran **Modifier l’attribut**.

![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Présentation des types de mappages d’attributs
Avec les mappages d’attributs, vous contrôlez la façon dont les attributs sont renseignés dans une application SaaS tierce. Quatre différents types de mappages sont pris en charge :

* **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Azure AD.
* **Constant** : l’attribut cible est renseigné avec une chaîne spécifique que vous avez spécifiée.
* **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression semblable à un script. 
  Pour plus d’informations, consultez l’article [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](functions-for-customizing-application-data.md).
* **Aucun** : l’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il est renseigné avec la valeur par défaut que vous spécifiez.

Outre ces quatre types de base, les mappages d’attributs personnalisés prennent en charge le concept d’affectation de valeur **par défaut** facultative. L’affectation de valeur par défaut garantit qu’un attribut cible est renseigné avec une valeur s’il n’existe aucune valeur ni dans Azure AD, ni sur l’objet cible. La configuration la plus courante consiste à laisser ce champ vide.


### <a name="understanding-attribute-mapping-properties"></a>Présentation des propriétés de mappage d’attributs

La section précédente vous a présenté la propriété de type de mappage d’attributs.
Outre cette propriété, les mappages d’attributs prennent en charge les attributs suivants :

- **Attribut source** : attribut utilisateur du système source (par exemple, Azure Active Directory).
- **Attribut cible** : attribut utilisateur dans le système cible (par exemple, ServiceNow).
- **Trouver les objets utilisant cet attribut** : indique si ce mappage doit être utilisé ou pas pour identifier les utilisateurs de manière unique entre les systèmes source et cible. Ce champ est généralement défini sur l’attribut userPrincipalName ou mail dans Azure AD, qui est généralement mappé à un champ de nom d’utilisateur dans une application cible.
- **Priorité de correspondance** : vous pouvez définir plusieurs attributs de correspondance. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.
- **Appliquer ce mappage**
    - **Toujours** : applique ce mappage à la création de l’utilisateur et des actions de mise à jour.
    - **Lors de la création uniquement** : applique ce mappage uniquement aux actions de création d’utilisateur.


## <a name="editing-group-attribute-mappings"></a>Modification des mappages d’attributs de groupe

Un certain nombre d’applications, telles que ServiceNow, Box et Google Apps, prennent en charge la possibilité d’approvisionner des objets de groupe en plus des objets utilisateur. Les objets de groupe peuvent contenir des propriétés de groupe telles que les noms d’affichage et les alias d’e-mail en plus des membres du groupe.

![ServiceNow](./media/customize-application-attributes/24.png)

Le provisionnement du groupe peut éventuellement être activé ou désactivé en sélectionnant le mappage de groupe sous **Mappages** et en définissant le paramètre **Activé** sur l’option souhaitée dans l’écran **Mappage d’attributs**.

Les attributs approvisionnés en tant que partie d’objets de groupe peuvent être personnalisés de la même manière que les objets utilisateur décrits précédemment. 

>[!TIP]
>L’approvisionnement des objets de groupe (propriétés et membres) est un concept distinct issu de l’[affectation de groupes](assign-user-or-group-access-portal.md) à une application. Il est possible d’affecter un groupe à une application, mais de n’approvisionner que les objets utilisateur contenus dans le groupe. L’approvisionnement des objets de groupe complet n’est pas nécessaire d’utiliser des groupes dans les attributions.


## <a name="editing-the-list-of-supported-attributes"></a>Modification de la liste des attributs pris en charge

Les attributs utilisateur pris en charge pour une application donnée sont préconfigurés. La plupart des API de gestion des utilisateurs d’applications ne prennent pas en charge la découverte de schéma. Pour cette raison, le service d’approvisionnement d’Azure AD n’est pas en mesure de générer dynamiquement la liste des attributs pris en charge via des appels à l’application. 

Toutefois, certaines applications prennent en charge des attributs personnalisés. Pour que le service de provisionnement Azure AD puisse lire et écrire dans les attributs personnalisés, leurs définitions doivent être entrées dans le portail Azure à l’aide de la case à cocher **Afficher les options avancées** située au bas de l’écran **Mappage d’attributs**.

Les applications et les systèmes qui prennent en charge la personnalisation de la liste d’attributs sont les suivantes :

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory (les [attributs par défaut de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) et les extensions d’annuaire personnalisées sont pris en charge)
* Les applications qui prennent en charge [SCIM 2.0](https://tools.ietf.org/html/rfc7643), où les attributs définis dans le [schéma principal](https://tools.ietf.org/html/rfc7643) doivent être ajoutés

>[!NOTE]
>La modification de la liste des attributs pris en charge n’est recommandée que pour les administrateurs qui ont personnalisé le schéma de leurs applications et systèmes et ont connaissance de première main de la façon dont leurs attributs personnalisés ont été définis. Ceci nécessite parfois de connaître les API et les outils de développement fournis par une application ou un système. 

![Éditeur](./media/customize-application-attributes/25.png) 

Lorsque vous modifiez la liste des attributs pris en charge, les propriétés suivantes sont fournies :

* **Name** : le nom du système de l’attribut, tel que défini dans le schéma de l’objet cible. 
* **Type** : le type de données stockées par l’attribut, tel que défini dans le schéma de l’objet cible. Il peut s’agir de l’une des propriétés suivantes :
   * *Binary* : l’attribut contient des données binaires.
   * *Boolean* : l’attribut contient une valeur True ou False.
   * *DateTime* : l’attribut contient une chaîne de date.
   * *Integer* : l’attribut contient un entier.
   * *Référence* : l’attribut contient un ID qui fait référence à une valeur stockée dans une autre table de l’application cible.
   * *String* : l’attribut contient une chaîne de texte. 
* **Primary Key?** - L’attribut est défini ou pas comme un champ de clé primaire dans le schéma de l’objet cible.
* **Obligatoire ?** - L’attribut doit ou non être obligatoirement renseigné dans l’application ou le système cible.
* **Multi-value?** - L’attribut prend en charge ou pas plusieurs valeurs.
* **Exact case?** - Les valeurs d’attribut sont ou non évaluées de façon sensible à la casse.
* **API Expression** : ne pas utiliser, sauf si vous êtes invité à le faire dans la documentation relative à un connecteur d’approvisionnement spécifique (tel que Workday).
* **Referenced Object Attribute** : s’il s’agit d’un attribut de type référence, ce menu vous permet de sélectionner la table et l’attribut dans l’application cible qui contient la valeur associée à l’attribut. Par exemple, si vous avez un attribut nommé « Department » dont la valeur stockée fait référence à un objet dans une table « Departments » distincte, sélectionnez « Departments.Name ». Notez que les tables de référence et les champs d’ID primaires pris en charge pour une application donnée sont préconfigurés et ne peuvent actuellement pas être modifiés via le portail Azure, mais qu’ils peuvent être modifiés à l’aide de l’[API Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Pour ajouter un nouvel attribut, faites défiler jusqu'à la fin de la liste des attributs pris en charge, remplissez les champs ci-dessus à l’aide des entrées fournies, puis sélectionnez **ajouter un attribut**. Sélectionnez **Enregistrer** lorsque vous avez fini d’ajouter des attributs. Vous devez alors recharger l’onglet **Approvisionnement** pour que les nouveaux attributs soient disponibles dans l’éditeur de mappage d’attributs.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restauration des attributs par défaut et des mappages d’attributs

Si vous devez recommencer et réinitialiser vos mappages existants à leur état par défaut, vous pouvez cocher la case **Restaurer les mappages par défaut** et enregistrer la configuration. Cette opération définit tous les mappages comme si l’application venait d’être ajoutée à votre locataire Azure AD à partir de la galerie d’applications. 

Cette option entraînera une nouvelle synchronisation forcée de tous les utilisateurs pendant l’exécution du service d’approvisionnement. 

>[!IMPORTANT]
>Il est fortement recommandé que l’**État de l’approvisionnement** soit défini sur **Désactivé** avant d’appeler cette option.


## <a name="what-you-should-know"></a>Ce que vous devez savoir

* Microsoft Azure AD fournit une implémentation efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités pendant un cycle de synchronisation. 

* La mise à jour des mappages d’attributs impacte les performances d’un cycle de synchronisation. Une mise à jour de la configuration des mappages d’attributs nécessite une réévaluation de tous les objets gérés. 

* Il est recommandé d’apporter le moins de modifications consécutives possible à vos mappages d’attributs.


## <a name="next-steps"></a>Étapes suivantes

* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](user-provisioning.md)
* [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](use-scim-to-provision-users-and-groups.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)


