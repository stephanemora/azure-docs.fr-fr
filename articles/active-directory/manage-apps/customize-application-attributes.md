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
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a2965fecd3aca17d6c4df7e49ad466377de9762
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267206"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personnalisation des mappages d’attributs d’attribution d’utilisateurs pour les applications SaaS dans Azure Active Directory
Microsoft Azure AD prend en charge l’approvisionnement d’utilisateurs pour les applications SaaS tierces telles que Salesforce, Google Apps et autres. Si vous activez l’approvisionnement des utilisateurs pour une application SaaS, le portail Azure contrôle ses valeurs d’attribut via des mappages d’attributs.

Il existe un ensemble préconfiguré d’attributs et les mappages d’attributs entre les objets utilisateur Azure AD et les objets d’utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, ainsi que les utilisateurs, telles que les groupes.

Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre organisation. Par conséquent, vous pouvez modifier ou supprimer des mappages d’attributs existants ou créer des mappages d’attributs nouvelle.
 
## <a name="editing-user-attribute-mappings"></a>Modification des mappages d’attributs utilisateur

Suivez ces étapes pour accéder à la **mappages** fonctionnalité de l’approvisionnement utilisateur :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com).

1. Sélectionnez **applications d’entreprise** dans le volet gauche. Une liste de toutes les applications configurées est indiquée, y compris les applications qui ont été ajoutées à partir de la galerie.

1. Sélectionnez n’importe quelle application pour charger son volet de gestion d’application, où vous pouvez afficher des rapports et gérer les paramètres de l’application.

1. Sélectionnez **approvisionnement** pour gérer les paramètres de l’application sélectionnée d’approvisionnement de comptes utilisateur.

1. Développez **mappages** pour afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible. Si l’application cible le prend en charge, cette section vous permet éventuellement utiliser la configuration des groupes et comptes d’utilisateur.

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. Sélectionnez un **mappages** configuration pour ouvrir le connexes **mappage d’attributs** écran. Certains mappages d’attributs sont requis par une application SaaS pour fonctionner correctement. Pour les attributs requis, la fonctionnalité **Supprimer** n’est pas disponible.

   ![Salesforce](./media/customize-application-attributes/22.png)

   Dans cette capture d’écran, vous pouvez voir que le **nom d’utilisateur** attribut d’un objet géré dans Salesforce est rempli avec les **userPrincipalName** valeur de l’objet d’annuaire Active Azure lié.

1. Sélectionnez un **mappage d’attributs** pour ouvrir le **modifier l’attribut** écran. Ici, vous pouvez modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible.

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Présentation des types de mappages d’attributs
Avec les mappages d’attributs, vous contrôlez la façon dont les attributs sont renseignés dans une application SaaS tierce. Quatre différents types de mappages sont pris en charge :

* **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Azure AD.
* **Constante** – l’attribut cible est renseigné avec une chaîne spécifique que vous avez spécifié.
* **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression semblable à un script. 
  Pour plus d’informations, consultez l’article [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](functions-for-customizing-application-data.md).
* **Aucun** : l’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il est renseigné avec la valeur par défaut que vous spécifiez.

Avec ces quatre types de base, mappages d’attributs personnalisés prennent en charge le concept de facultative **par défaut** assignation de valeur. L’affectation de valeur par défaut garantit qu’un attribut cible est rempli avec une valeur s’il n’est pas une valeur dans Azure AD ou sur l’objet cible. La configuration la plus courante consiste à laisser ce champ vide.


### <a name="understanding-attribute-mapping-properties"></a>Présentation des propriétés de mappage d’attributs

Dans la section précédente, vous avez déjà abordé la propriété de type de mappage d’attributs.
Avec cette propriété, mappages d’attributs prennent également en charge les attributs suivants :

- **Attribut source** : attribut utilisateur du système source (par exemple, Azure Active Directory).
- **Attribut cible** : attribut utilisateur dans le système cible (par exemple, ServiceNow).
- **Faire correspondre des objets à l’aide de cet attribut** : indique si ce mappage doit être utilisé pour identifier les utilisateurs entre les systèmes source et cible. Il est généralement défini sur l’attribut userPrincipalName ou mail dans Azure AD, qui est généralement mappé à un champ de nom d’utilisateur dans une application cible.
- **Priorité des correspondances** : plusieurs attributs de correspondance peuvent être définis. S’il existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.
- **Appliquer ce mappage**
    - **Toujours** : appliquez ce mappage à la création d’utilisateur et mettre à jour des actions.
    - **Uniquement lors de la création** -applique ce mappage uniquement sur les actions de création d’utilisateur.


## <a name="editing-group-attribute-mappings"></a>Modification des mappages d’attributs de groupe

Un certain nombre d’applications, telles que ServiceNow, Box et Google Apps, prennent en charge la possibilité de configurer des objets de groupe et les objets utilisateur. Objets de groupe peuvent contenir des propriétés du groupe telles que les noms d’affichage et alias, ainsi que les membres du groupe de messagerie.

![ServiceNow](./media/customize-application-attributes/24.png)

Approvisionnement du groupe peut être éventuellement activée ou désactivée en sélectionnant le mappage de groupe sous **mappages**et le paramètre **activé** à l’option souhaitée dans le **mappaged’attributs** écran.

Les attributs approvisionnés en tant que partie d’objets de groupe peuvent être personnalisés de la même manière que les objets utilisateur décrits précédemment. 

>[!TIP]
>L’approvisionnement des objets de groupe (propriétés et membres) est un concept distinct issu de l’[affectation de groupes](assign-user-or-group-access-portal.md) à une application. Il est possible d’affecter un groupe à une application, mais de n’approvisionner que les objets utilisateur contenus dans le groupe. L’approvisionnement des objets de groupe complet n’est pas nécessaire d’utiliser des groupes dans les attributions.


## <a name="editing-the-list-of-supported-attributes"></a>Modification de la liste des attributs pris en charge

Les attributs utilisateur pris en charge pour une application donnée sont préconfigurés. Gestion des utilisateurs de la plupart des applications API ne prennent pas en charge la détection de schéma. Par conséquent, le service d’approvisionnement AD Azure n’est pas en mesure de générer dynamiquement la liste des attributs pris en charge en effectuant des appels à l’application. 

Toutefois, certaines applications prennent en charge les attributs personnalisés, et le service de provisionnement Azure AD peut lire et écrire dans les attributs personnalisés. Pour entrer leurs définitions dans le portail Azure, sélectionnez le **Show advanced options** case à cocher en bas de la **mappage d’attributs** écran, puis sélectionnez **liste d’attributs de modification pour** votre application.

Les applications et les systèmes qui prennent en charge la personnalisation de la liste d’attributs sont les suivantes :

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory (les [attributs par défaut de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) et les extensions d’annuaire personnalisées sont pris en charge)
* Les applications qui prennent en charge [SCIM 2.0](https://tools.ietf.org/html/rfc7643), où les attributs définis dans le [schéma principal](https://tools.ietf.org/html/rfc7643) doivent être ajoutés

>[!NOTE]
>La modification de la liste des attributs pris en charge n’est recommandée que pour les administrateurs qui ont personnalisé le schéma de leurs applications et systèmes et ont connaissance de première main de la façon dont leurs attributs personnalisés ont été définis. Ceci nécessite parfois de connaître les API et les outils de développement fournis par une application ou un système. 

Lorsque vous modifiez la liste des attributs pris en charge, les propriétés suivantes sont fournies :

* **Name** : le nom du système de l’attribut, tel que défini dans le schéma de l’objet cible. 
* **Type** -stockées par l’attribut du type de données, tel que défini dans le schéma de l’objet cible, qui peut être un des types suivants :
   * *Binary* : l’attribut contient des données binaires.
   * *Boolean* : l’attribut contient une valeur True ou False.
   * *DateTime* : l’attribut contient une chaîne de date.
   * *Integer* : l’attribut contient un entier.
   * *Référence* : l’attribut contient un ID qui fait référence à une valeur stockée dans une autre table de l’application cible.
   * *String* : l’attribut contient une chaîne de texte. 
* **Primary Key?** : Indique si l’attribut est défini comme un champ de clé primaire dans le schéma de l’objet cible.
* **Obligatoire ?** : Indique si l’attribut est obligatoirement être renseigné dans l’application cible ou le système.
* **Multi-value?** : Indique si l’attribut prend en charge plusieurs valeurs.
* **Exact case?** : Indique si les valeurs d’attributs sont évaluées de manière respect de la casse.
* **API Expression** -n’utilisez pas, sauf indication contraire pour ce faire, la documentation pour un connecteur d’approvisionnement spécifique (tel que Workday).
* **Referenced Object Attribute** : si elle est un attribut de type référence, puis ce menu vous permet de sélectionner la table et l’attribut dans l’application cible qui contient la valeur associée à l’attribut. Par exemple, si vous avez un attribut nommé « Department » dont la valeur stockée fait référence à un objet dans une table « Departments » distincte, sélectionnez « Departments.Name ». Les tables de référence et les champs d’ID primaires pris en charge pour une application donnée sont préconfigurés et actuellement ne peut pas être modifiés à l’aide du portail Azure, mais peut être modifiées à l’aide de la [API Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Pour ajouter un nouvel attribut, faites défiler jusqu'à la fin de la liste des attributs pris en charge, remplissez les champs ci-dessus à l’aide des entrées fournies, puis sélectionnez **ajouter un attribut**. Sélectionnez **Enregistrer** lorsque vous avez fini d’ajouter des attributs. Vous devez recharger la **approvisionnement** onglet pour les nouveaux attributs soient disponibles dans l’éditeur de mappage d’attributs.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restauration des attributs par défaut et des mappages d’attributs

Vous devez recommencer et réinitialiser vos mappages existants au leur état par défaut, vous pouvez sélectionner le **restaurer les mappages par défaut** case à cocher et enregistrer la configuration. Cela de définit tous les mappages comme si l’application vient d’être ajoutée à votre locataire Azure AD à partir de la galerie d’applications. 

Cette option sera forcée une resynchronisation de tous les utilisateurs tandis que le service d’approvisionnement est en cours d’exécution. 

>[!IMPORTANT]
>Nous vous recommandons fortement **état de provisionnement** être définie sur **hors** avant l’appel de cette option.


## <a name="what-you-should-know"></a>Ce que vous devez savoir

* Microsoft Azure AD fournit une implémentation efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités pendant un cycle de synchronisation. 

* La mise à jour des mappages d’attributs impacte les performances d’un cycle de synchronisation. Une mise à jour de la configuration des mappages d’attributs nécessite une réévaluation de tous les objets gérés. 

* Une pratique recommandée consiste à limiter le nombre de modifications consécutives de vos mappages d’attributs au minimum.


## <a name="next-steps"></a>Étapes suivantes

* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](user-provisioning.md)
* [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](use-scim-to-provision-users-and-groups.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)


