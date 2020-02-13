---
title: 'Tutoriel : Configurer Zoom pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Zoom.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062754"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutoriel : Configurer Zoom pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Zoom et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Zoom.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Zoom](https://zoom.us/pricing)
* un compte d’utilisateur dans Zoom avec des autorisations d’administration

## <a name="add-zoom-from-the-gallery"></a>Ajouter Zoom à partir de la galerie

Avant de configurer Zoom pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Zoom à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Zoom à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Zoom**, sélectionnez **Zoom** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Zoom dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Affecter des utilisateurs à Zoom

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Zoom. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Zoom en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Conseils importants pour l’attribution d’utilisateurs à Zoom

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Zoom afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Zoom, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Configurer l’approvisionnement automatique d’utilisateurs sur Zoom 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs ou des groupes dans Zoom en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Zoom en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Zoom](zoom-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Configurer l’approvisionnement automatique d’utilisateurs pour Zoom dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Zoom**.

    ![Lien Zoom dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez `https://api.zoom.us/scim` dans **URL de locataire**. Pour récupérer le **jeton secret** de votre compte Zoom, suivez la procédure pas à pas, comme décrit à l’étape 6.

6. Connectez-vous à votre [Console d’administration Zoom](https://zoom.us/signin). Accédez à **Avancé > Zoom pour développeurs** dans le volet de navigation gauche.

    ![Intégrations Zoom](media/zoom-provisioning-tutorial/zoom01.png)

    Accédez à **Gérer** dans l’angle supérieur droit de la page. 

    ![Installation de Zoom](media/zoom-provisioning-tutorial/zoom02.png)

    Accédez à votre application Azure AD créée. 
    
    ![Application Zoom](media/zoom-provisioning-tutorial/zoom03.png)

    Dans le volet de navigation de gauche, sélectionnez **Informations d’identification de l’application**.

    ![Application Zoom](media/zoom-provisioning-tutorial/zoom04.png)

    Récupérez la valeur du jeton JWT indiquée ci-dessous et entrez-la dans le champ **Jeton Secret** dans Azure AD. Si vous avez besoin d’un nouveau jeton sans expiration, vous devrez reconfigurer l’expiration, ce qui générera automatiquement un nouveau jeton. 

    ![Installation de Zoom](media/zoom-provisioning-tutorial/zoom05.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Zoom. Si la connexion échoue, vérifiez que votre compte Zoom dispose des autorisations d’administrateur et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Zoom**.

    ![Zoom - Mappages d’utilisateurs](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Zoom. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Zoom pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.
    
     ![Zoom - Mappages d’utilisateurs](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Zoom, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.
    
    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous souhaitez approvisionner sur Zoom en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Zoom.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Zoom ne prend pas en charge l’attribution pour les groupes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
