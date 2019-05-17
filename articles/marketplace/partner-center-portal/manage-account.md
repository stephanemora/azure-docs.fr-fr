---
title: Comment gérer un compte place de marché Commercial dans Partner Center
description: Découvrez comment gérer un compte place de marché Commercial dans Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: 935d2e1c96705506636c2883113a64bb70c39336
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806190"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Comment gérer votre compte de la place de marché Commercial dans Partner Center 

Une fois que vous avez [créé un compte espace partenaires](./create-account.md), vous pouvez gérer votre compte et des offres à l’aide de la [tableau de bord de la place de marché Commercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Dans cet article, nous allons approfondir comment gérer votre compte espace partenaires, notamment comment : 

- [Accéder à vos paramètres de compte de partenaires](#access-your-account-settings)
- [Trouver votre ID de l’éditeur, les ID de vendeur, les ID utilisateur et les locataires Azure AD](#account-details)
- [Mettre à jour les informations de contact](#contact-info)
- [Gérer les détails financiers (compte de paiement, profil fiscal, état de blocage de paiement)](#financial-details)
- [Configurer les GUID de suivi pour la surveillance de l’utilisation du client](#tracking-guids)
- [Utilisateurs du Gestionnaire](#manage-users)
- [Groupes de gestionnaire](#manage-groups)
- [Applications Manager Azure AD](#manage-azure-ad-applications)
- [Définir des autorisations et rôles d’utilisateur](#define-user-roles-and-permissions)
- [Gérer les locataires Azure AD (comptes professionnels)](#manage-tenants)
- [Accords de partenaires de configuration Manager](#agreements)


## <a name="access-your-account-settings"></a>Accéder à vos paramètres de compte

Si vous ne le n'avez pas déjà fait, vous (ou administrateur de votre organisation) devez accéder à la [paramètres du compte](https://partner.microsoft.com/dashboard/account/management) pour votre compte espace partenaires pour :
- vérifier l’état de vérification de compte de votre entreprise
- confirmer votre ID de vendeur, les ID MPN, les ID de l’éditeur et les informations de contact, y compris le vendeur et approbateur de contact de la société
- définir les détails financiers de votre entreprise, y compris les exemptions de taxe le cas échéant
- créer des comptes d’utilisateur pour toute personne qui utilise votre compte professionnel dans les partenaires

### <a name="open-developer-settings"></a>Ouvrez les paramètres du développeur

Paramètres de compte se trouve dans le coin supérieur droit de votre [tableau de bord de la place de marché Commercial](https://partner.microsoft.com/dashboard/commercial-marketplace) dans Partner Center. Sélectionnez l’icône d’engrenage (dans le coin supérieur droit du tableau de bord), puis **les paramètres du développeur**. 

![Menu de paramètres de compte Centre de partenaires](./media/dashboard-developer-settings.png)

À l’intérieur de **paramètres du compte**, vous serez en mesure d’afficher votre :
- **Détails de votre compte**: Type de compte et l’état du compte
- **ID du serveur de publication**: ID de vendeur, ID d’utilisateur, ID de l’éditeur, les locataires Azure AD, etc.
- **Informations de contact**: Nom de l’éditeur, nom de contact du vendeur, e-mail, phone et adresse
- **Détails financiers**: Compte de paiement, profil fiscal et l’état d’attente de paiement
- **Appareils**: Les appareils de tests associés à votre compte
- **GUID de suivi**: N’importe quel GUID associé à votre compte de suivi

### <a name="account-details"></a>Détails du compte

Dans la section Détails du compte, vous pouvez voir des informations de base, telles que votre **type de compte** (entreprise ou individuelles) et le **état de la vérification** de votre compte. Pendant le processus de vérification de votre compte, ces paramètres affiche chaque étape nécessaire, y compris la vérification par e-mail, vérification de l’emploi et la vérification de l’entreprise. Vous pouvez également mettre à jour votre adresse de messagerie et renvoyer la vérification si nécessaire. 

### <a name="publisher-ids"></a>ID du serveur de publication

Dans la section de l’ID du serveur de publication, vous pouvez voir votre **ID de vendeur**, **ID MPN**, et **ID de l’éditeur**. Ces valeurs sont assignés par Microsoft pour identifier votre compte de développeur et ne peut pas être modifiés.

### <a name="contact-info"></a>Infos de contact

Dans la section informations de Contact, vous pouvez voir votre **nom d’affichage de serveur de publication**, **les informations de contact du vendeur** (le nom du contact, e-mail, numéro de téléphone et adresse pour le vendeur d’entreprise) et le **entreprise approbateur** (nom, e-mail et numéro de téléphone de la personne avec autorité pour approuver les décisions de l’entreprise). 

### <a name="financial-details"></a>Détails financiers

Dans la section des détails financiers, vous pouvez fournir ou mettre à jour vos informations financières, si vous publiez des applications payantes, les compléments ou les services. 

Si vous envisagez uniquement à la liste des offres gratuites, vous n’avez pas besoin configurer un compte de paiement ou remplir des formulaires fiscaux. Si vous changez d’avis ultérieurement et que vous décidez de que vous ne souhaitez pas vendre via Microsoft, vous pouvez configurer votre compte de paiement et remplissez des formulaires fiscaux à ce moment-là. 

#### <a name="payout-account"></a>Compte de paiement

Un compte de paiement est le compte bancaire à laquelle les frais sont envoyés à partir de vos ventes. Ce compte doit être dans le même pays que celui où vous avez enregistré votre compte espace partenaires.

Pour configurer votre compte de paiement, vous devez **associer votre Account Microsoft**:
1. Dans **paramètres du compte**, sous le **détails financiers** section, sélectionnez **associer votre Account Microsoft**. 
2. Lorsque vous y êtes invité, connectez-vous avec votre compte Microsoft (MSA). Ce compte ne peut pas déjà être associé à un autre compte espace partenaires. 
3. Pour terminer la configuration de votre compte de paiement, journal entièrement hors de partenaires, puis reconnectez-vous avec votre Account Microsoft (plutôt que votre compte professionnel). 

Maintenant que votre Account de Microsoft est associé, pour ajouter un compte de paiement, vous devez :
- **Choisissez une méthode de paiement**: Compte bancaire ou PayPal
- **Ajouter des informations de paiement**: Cela peut inclure des choix d’un type de compte (courant ou épargne), entrer le nom du titulaire du compte, le numéro de compte et le routage nombre, adresse de facturation, numéro de téléphone ou adresse de messagerie de PayPal. * Pour plus d’informations sur l’utilisation de PayPal comme mode de paiement de compte et pour savoir si elle est prise en charge dans votre région de mise sur le marché, consultez [PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modification de votre compte de paiement peut retarder vos paiements jusqu’à un cycle de paiement. Ce retard se produit parce que nous devons vérifier la modification du compte, tout comme nous avons tout d’abord configurer le compte de paiement. Vous serez payé pour le montant total une fois que votre compte aura été vérifié. Tout paiement dû dans le cycle de paiement actuel sera ajouté au cycle suivant  

#### <a name="tax-profile"></a>Profil fiscal

Passez en revue votre état de profil de taxe actuel, confirmant le bon **type d’entité** et **les informations de certificat de taxe** s’affiche. Sélectionnez **modifier** pour mettre à jour ou effectuez l’une requises forms.

Afin d’établir votre état de taxe, vous devez spécifier votre pays de résidence et citoyenneté et terminer les formulaires fiscale appropriée associées à votre pays/région.

Quel que soit votre pays de résidence ou de citoyenneté, vous devez remplir formulaires fiscaux d’United States de vendre des offres via Microsoft. Les partenaires qui répondent à certaines exigences en matière de résidence d’United States doivent remplir un formulaire w-9 de l’IRS. Autres partenaires en dehors des États-Unis doivent remplir un formulaire IRS W-8. Vous pouvez remplir ces formulaires en ligne lorsque vous complétez votre profil fiscal.

Un United States numéro d’Identification du contribuable individuel (ou ITIN) n’est pas requis pour recevoir des paiements de Microsoft ou à revendiquer les avantages fiscaux traité.

Vous pouvez effectuer et soumettre vos formulaires de taxe par voie électronique dans partenaires ; dans la plupart des cas, vous n’avez pas besoin d’impression et de tous les formulaires de messagerie.

Différents pays et régions ont des exigences différentes fiscales. La quantité exacte que vous devez payer impôts varie selon les pays et régions où vous vendez vos offres. Microsoft renvoie la taxe de vente et de votre part dans certains pays. Dans ces pays seront identifiés en cours de référencement de votre offre. Dans d’autres pays, en fonction de l’endroit où vous êtes inscrit, vous devrez peut-être reverser des ventes et l’utilisation de taxe pour vos ventes directement à l’autorité fiscale locale. En outre, le produit de la vente que vous recevez peut être assujetti comme revenu. Nous vous encourageons vivement à contacter l’autorité pour votre pays ou région qui peut mieux vous aider à déterminer les informations de taxe approprié pour les transactions commerciales de Microsoft.

##### <a name="withholding-rates"></a>Taux de retenue
Les informations que vous envoyez dans vos formulaires fiscaux détermine le taux de retenue d’impôt approprié. Le taux de retenue s’applique uniquement aux ventes que vous apportez aux États-Unis ; ventes effectuées dans des emplacements en dehors des États-Unis ne sont pas soumis à la retenue. Taux de rétention varient, mais pour la plupart des développeurs l’inscription en dehors des États-Unis, la fréquence par défaut est 30 %. Vous avez la possibilité de réduire cette fréquence si votre pays a opté pour un traité sur le revenu avec les États-Unis.

##### <a name="tax-treaty-benefits"></a>Avantages fiscaux traité
Si vous êtes en dehors des États-Unis, vous pourrez peut-être tirer parti de taxe les avantages traité. Ces avantages varient d’un pays à l’autre et peuvent vous permettre de réduire la quantité de taxes Microsoft retenu. Vous pouvez demander les avantages fiscaux traité en effectuant la partie II du formulaire W-8ben pour ceux qui. Nous vous conseillons de communiquer avec les ressources appropriées dans votre pays ou région pour déterminer si ces avantages s’appliquent à vous.

[En savoir plus sur les informations fiscales pour les développeurs d’application/jeu de Windows et les serveurs de publication Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>État en attente de paiement

Par défaut, Microsoft envoie les paiements sur une base mensuelle. Toutefois, vous avez la possibilité de placer vos paiements en attente, ce qui empêche l’envoi de paiements à votre compte. Si vous choisissez de mettre vos paiements en attente, nous allons continuer à enregistrer n’importe quel chiffre d’affaires que vous gagnez et indiquez les informations de votre **synthèse des paiements**. Toutefois, nous n’envoie tous les paiements à votre compte jusqu'à ce que vous supprimiez le blocage. 

Pour placer vos paiements en attente, accédez à **paramètres du compte**. Sous **détails financiers**, dans le **paiement contiennent état** section, activer/désactiver le curseur pour **sur**. Vous pouvez modifier votre état en attente de paiement à tout moment, mais n’oubliez pas que votre décision aura un impact sur le paiement mensuel suivant. Par exemple, si vous souhaitez conserver les paiements d’avril, veillez à définir votre état en attente de paiement **sur** avant la fin du mois de mars.

Une fois que vous avez défini votre paiement conserver l’état sur **sur**, tous les paiements seront bloquées jusqu'à ce que vous passez le curseur vers **hors**. Lorsque vous procédez ainsi, vous serez inclus lors du prochain cycle de paiement mensuel (à condition que les seuils de paiement applicables ont été remplies). Par exemple, si vous avez vos paiements en attente, mais qu’ils aient un paiement généré en juin, puis veillez à activer/désactiver le paiement statut suspension à **hors** avant la fin de mai.

> [!NOTE]
> Votre **paiement contiennent état** sélection s’applique à **tous les** des sources de revenus sont payés via Microsoft Partner Center, y compris la place de marché Azure, AppSource, Microsoft Store, publicité, etc..). Vous ne pouvez pas sélectionner les statuts de suspension différents pour chaque source de revenus.

### <a name="devices"></a>Appareils

Les paramètres de gestion de périphérique s’appliquent uniquement à la publication de UWP. [Plus d’informations](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID de suivi

Dans le monde entier des identificateurs uniques (GUID) sont des nombres de référence unique (avec 32 chiffres hexadécimaux) qui peut être utilisé pour le suivi de votre utilisation d’Azure. 

Pour créer un GUID et effectuer son suivi, vous devez utiliser un générateur GUID. L’équipe de stockage Azure a créé un [formulaire générateur de GUID](https://aka.ms/StoragePartners) qui vous enverra par e-mail un GUID au format approprié et pourra être réutilisé dans les différents systèmes de suivi.

Nous vous recommandons de créer un GUID unique pour chaque offre et canal de distribution de chaque produit. Si vous ne souhaitez pas que les rapports soient scindés, vous pouvez choisir d'utiliser un GUID unique pour les différents canaux de distribution du produit.

Si vous déployez un produit à l'aide d'un modèle et qu'il est disponible à la fois sur la Place de marché Azure et sur GitHub, vous pouvez créer et enregistrer 2 GUID distincts :

*   Produit A sur la Place de marché Azure
*   Produit A sur GitHub

Les rapports sont établis à l'aide de la valeur de partenaire (ID partenaire Microsoft) et des GUID. Vous pouvez également suivre les GUID à un niveau plus granulaire en alignant sur chaque plan au sein de votre offre.

Pour plus d’informations, consultez le [l’utilisation du client Azure suivi avec GUID FAQ](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gestion des comptes d’utilisateurs multiples

Tire parti des partenaires [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) pour la gestion et accès aux comptes d’utilisateurs multiples. Votre organisation Azure AD est automatiquement associé à votre compte espace partenaires dans le cadre du processus d’inscription. 

## <a name="manage-users"></a>Gérer les utilisateurs

Le **utilisateurs** section de partenaires (sous **comptable**) vous utilisez Azure AD pour gérer les utilisateurs, groupes et les applications Azure AD qui ont accès à votre compte espace partenaires. Notez que pour gérer les utilisateurs, vous devez être connecté avec votre [compte professionnel](./company-work-accounts.md) (locataire Azure AD associé). Pour gérer les utilisateurs au sein d’un compte de travail / client, vous devrez vous déconnecter et puis reconnectez-vous en tant qu’utilisateur avec **Manager** fonctionnent les autorisations sur ce compte / locataire. 

Une fois que vous êtes connecté avec votre compte professionnel (locataire Azure AD), vous pouvez :
- [Ajouter ou supprimer des utilisateurs](#add-or-remove-users)
- [Modifier un mot de passe utilisateur](#change-a-user-password)
- [Ajouter ou supprimer des groupes](#add-or-remove-users)
- [Ajouter ou supprimer des applications Azure AD](#add-new-azure-ad-applications)
- [Gérer les clés pour une application Azure AD](#manage-keys-for-an-azure-ad-application)
- [Définir des autorisations et rôles d’utilisateur](#define-user-roles-and-permissions)


N’oubliez pas que tous les utilisateurs partenaires (y compris les groupes et applications Azure AD) doivent posséder un compte de travail actif dans un [client Azure AD](#manage-tenants) qui est associé à votre compte espace partenaires. 

### <a name="add-or-remove-users"></a>Ajouter ou supprimer des utilisateurs

Votre compte doit avoir [ **au niveau du Gestionnaire de** ](#define-user-roles-and-permissions) autorisations pour le [(locataire Azure AD) de compte professionnel](./company-work-accounts.md) dans lequel vous voulez ajouter ou modifier des utilisateurs.

#### <a name="add-existing-users"></a>Ajouter des utilisateurs existants

Pour ajouter des utilisateurs à votre compte espace partenaires qui existent déjà dans votre société [(locataire Azure AD) de compte professionnel](./company-work-accounts.md):

1. Accédez à **utilisateurs** (sous **paramètres du compte**) et sélectionnez **ajouter des utilisateurs**.
2. Sélectionnez un ou plusieurs utilisateurs dans la liste qui s’affiche. Vous pouvez utiliser la zone de recherche pour rechercher des utilisateurs spécifiques.
* Si vous sélectionnez plusieurs utilisateurs à ajouter à votre compte espace partenaires, vous devez les attribuer le même rôle ou jeu d’autorisations personnalisées. Pour ajouter plusieurs utilisateurs avec des rôles/autorisations différentes, répétez ces étapes pour chaque rôle ou d’un jeu d’autorisations personnalisées.
3.  Lorsque vous avez terminé de sélectionner les utilisateurs, cliquez sur **ajouter sélectionné**.
5.  Dans le **rôles** section, spécifiez l’ou les rôles ou les autorisations personnalisées pour les utilisateurs sélectionnés.
6.  Sélectionnez **Enregistrer**.

#### <a name="create-new-users"></a>Créer de nouveaux utilisateurs

Pour créer les tout nouveaux comptes d’utilisateur, vous devez disposer d’un compte avec [ **administrateur général** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) autorisations. 

1. Accédez à **utilisateurs** (sous **paramètres du compte**), sélectionnez **ajouter des utilisateurs**, puis choisissez **créer de nouveaux utilisateurs**.
1. Entrez un prénom, le nom de famille et le nom d’utilisateur pour chaque nouvel utilisateur. 
1. Si vous souhaitez que le nouvel utilisateur d’avoir un compte d’administrateur général dans le répertoire de votre organisation, cochez la case intitulée **faire de cet utilisateur un administrateur général dans Azure AD, avec contrôle total sur toutes les ressources de répertoire**. Cela donnera l’utilisateur un accès complet à toutes les fonctionnalités d’administration dans votre entreprise d’Azure AD. Ils pourrez ajouter et gérer des utilisateurs dans le compte de travail de votre organisation (locataire Azure AD), bien que pas dans Partner Center, sauf si vous accordez au compte les autorisations de rôle/appropriées. 
1. Si vous avez coché la case pour **faire de cet utilisateur un administrateur Global**, vous devez fournir un **e-mail de récupération de mot de passe** pour l’utilisateur de récupérer leur mot de passe si nécessaire.
1. Dans le **appartenance au groupe** , sélectionnez tous les groupes auxquels vous souhaitez le nouvel utilisateur doit appartenir.
1. Dans le **rôles** section, spécifiez l’ou les rôles ou les autorisations personnalisées pour l’utilisateur.
1. Sélectionnez **Enregistrer**.

Création d’un utilisateur dans l’espace partenaires est également créer un compte pour cet utilisateur dans le compte de travail (locataire Azure AD) auquel vous êtes connecté. Apporter des modifications à un nom d’utilisateur dans l’espace partenaires est d’apporter les mêmes modifications dans le compte de travail de votre organisation (locataire Azure AD).

#### <a name="invite-new-users-by-email"></a>Inviter de nouveaux utilisateurs par courrier électronique

Pour inviter les utilisateurs qui ne sont pas actuellement partie de votre compte de travail d’entreprise (client d’Azure AD) par courrier électronique, vous devez disposer d’un compte avec [ **administrateur général** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) autorisations. 

1. Accédez à **utilisateurs** (sous **paramètres du compte**), sélectionnez **ajouter des utilisateurs**, puis choisissez **inviter des utilisateurs par courrier électronique**.
2. Entrez l’e-mail d’un ou plusieurs adresses (jusqu'à dix), séparés par des virgules ou des points-virgules.
3. Dans le **rôles** section, spécifiez l’ou les rôles ou les autorisations personnalisées pour l’utilisateur.
4. Sélectionnez **Enregistrer**.

Les utilisateurs que vous avez invité obtiendra un e-mail d’invitation à rejoindre votre compte espace partenaires. Un nouveau compte d’utilisateur invité est créé dans votre compte professionnel (locataire Azure AD). Chaque utilisateur devra accepté leur invitation avant de pouvoir accéder à votre compte.

Si vous avez besoin de renvoyer une invitation, visitez le **utilisateurs** , recherchez l’invitation dans la liste d’utilisateurs, sélectionnez leur adresse de messagerie (ou le texte indiquant *Invitation en attente*). Ensuite, en bas de la page, sélectionnez **renvoyer l’invitation**.
 

> [!NOTE]
> Si votre organisation utilise [intégration d’annuaire](https://go.microsoft.com/fwlink/p/?LinkID=724033) pour synchroniser le service d’annuaire local avec votre compte Azure AD, vous ne pourrez pas créer de nouveaux utilisateurs, des groupes ou des applications Azure AD dans le centre de partenaires. Vous (ou un autre administrateur de votre annuaire local) devez les créer directement dans le répertoire local avant que vous serez en mesure de voir et de les ajouter dans l’espace partenaires.

#### <a name="remove-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur à partir de votre compte professionnel (locataire Azure AD), accédez à **utilisateurs** (sous **paramètres du compte**), sélectionnez l’utilisateur que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis choisissez  **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer les utilisateurs sélectionnés.

#### <a name="change-a-user-password"></a>Modifier un mot de passe utilisateur

Si un de vos utilisateurs a besoin de modifier leur mot de passe, ils peuvent le faire eux-mêmes si vous avez fourni un **e-mail de récupération de mot de passe** lors de la création du compte d’utilisateur. Vous pouvez également mettre à jour un mot de passe utilisateur en suivant les étapes ci-dessous. Pour modifier un mot de passe dans votre compte de travail d’entreprise (client d’Azure AD), vous devez être connecté sur un compte avec [ **administrateur général** ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) autorisations. Notez que cela modifiera le mot de passe dans votre locataire Azure AD, ainsi que le mot de passe qu’ils permettent d’accéder aux partenaires.

1.  À partir de la **utilisateurs** page (sous **paramètres du compte**), sélectionnez le nom du compte d’utilisateur que vous souhaitez modifier.
2.  Sélectionnez le **réinitialisation de mot de passe** bouton en bas de la page.
3.  Une page de confirmation s’affiche indiquant les informations de connexion pour l’utilisateur, y compris un mot de passe temporaire. N’oubliez pas d’imprimer ou copier ces informations et fournir à l’utilisateur, comme vous ne pourrez pas accéder à un mot de passe temporaire, une fois que vous quittez cette page.


## <a name="manage-groups"></a>Gérer des groupes

Groupes vous permettent de contrôler plusieurs rôles d’utilisateur et autorisations tous ensemble.

#### <a name="add-an-existing-group"></a>Ajouter un groupe existant

Pour ajouter qu'un groupe qui existe déjà dans le compte de travail de votre organisation (locataire Azure AD) à votre compte espace partenaires : 

1.  À partir de la **utilisateurs** page (sous **paramètres du compte**), sélectionnez **ajouter des groupes**.
2.  Sélectionnez un ou plusieurs groupes dans la liste qui s’affiche. Vous pouvez utiliser la zone de recherche pour rechercher des groupes spécifiques.
Si vous sélectionnez plus d’un groupe à ajouter à votre compte espace partenaires, vous devez les attribuer le même rôle ou jeu d’autorisations personnalisées. Pour ajouter plusieurs groupes avec des rôles/autorisations différentes, répétez ces étapes pour chaque rôle ou d’un jeu d’autorisations personnalisées.
3.  Lorsque vous avez terminé de sélectionner des groupes, cliquez sur **ajouter sélectionné**.
4.  Dans le **rôles** section, spécifiez l’ou les rôles ou les autorisations personnalisées pour les groupes sélectionnés. Tous les membres du groupe sera en mesure d’accéder à votre compte de partenaires avec les autorisations que vous appliquez au groupe, quel que soit les rôles et les autorisations associées à leur compte individuel.
5.  Sélectionnez **Enregistrer**.

Lorsque vous ajoutez un groupe existant, chaque utilisateur qui est un membre de ce groupe sera en mesure d’accéder à votre compte espace partenaires, avec les autorisations associées au rôle du groupe.

#### <a name="add-a-new-group"></a>Ajouter un groupe

Pour ajouter un groupe de toute nouvel à votre compte espace partenaires : 

1.  À partir de la **utilisateurs** page (sous **paramètres du compte**), sélectionnez **ajouter des groupes**.
2.  Sur la page suivante, sélectionnez **nouveau groupe**.
3.  Entrez le nom d’affichage pour le nouveau groupe.
4.  Spécifiez l’ou les rôles ou les autorisations personnalisées pour le groupe. Tous les membres du groupe sera en mesure d’accéder à votre compte de partenaires avec les autorisations que vous appliquez ici, quelles que soient les rôles/autorisations associées à leur compte individuel.
5.  Sélectionnez l’ou les utilisateurs pour le nouveau groupe dans la liste qui s’affiche. Vous pouvez utiliser la zone de recherche pour rechercher des utilisateurs spécifiques.
6.  Lorsque vous avez fini de sélectionner les utilisateurs, cliquez sur **ajouter sélectionné** pour les ajouter au nouveau groupe.
7.  Sélectionnez **Enregistrer**.

Notez que ce nouveau groupe sera créé dans le compte de travail de votre organisation (locataire Azure AD), de pas seulement dans votre compte espace partenaires.

#### <a name="remove-a-group"></a>Supprimer un groupe

Pour supprimer un groupe à partir de votre compte professionnel (locataire Azure AD), accédez à **utilisateurs** (sous **paramètres du compte**), sélectionnez le groupe que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis choisissez  **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer l’ou les groupes sélectionné.

## <a name="manage-azure-ad-applications"></a>Gérer les applications Azure AD

Vous pouvez autoriser les applications ou services qui font partie de Azure votre société AD pour accéder à votre compte espace partenaires. 

#### <a name="add-existing-azure-ad-applications"></a>Ajouter des applications Azure AD existantes 

Pour ajouter des applications qui existent déjà dans Azure Active Directory votre société : 

1.  À partir de la **utilisateurs** page (sous **paramètres du compte**), sélectionnez **ajouter des applications Azure AD**.
2.  Sélectionnez une ou plusieurs applications Azure AD dans la liste qui s’affiche. Vous pouvez utiliser la zone de recherche pour rechercher des propres applications Azure AD. Si vous sélectionnez plusieurs applications Azure AD à ajouter à votre compte espace partenaires, vous devez les attribuer le même rôle ou jeu d’autorisations personnalisées. Pour ajouter plusieurs applications Azure AD avec des rôles/autorisations différentes, répétez ces étapes pour chaque rôle ou d’un jeu d’autorisations personnalisées.
3.  Lorsque vous avez fini de sélectionner les applications Azure AD, cliquez sur **ajouter sélectionné**.
5.  Dans le **rôles** section, spécifiez l’ou les rôles ou les autorisations personnalisées pour l’ou les applications Azure AD.
6.  Sélectionnez **Enregistrer**.

#### <a name="add-new-azure-ad-applications"></a>Ajouter le nouvel Azure applications AD 

Si vous souhaitez accorder l’accès de partenaires à un tout nouveau Azure compte d’application AD, vous pouvez en créer un dans le **utilisateurs** section. Notez que cela créera un nouveau compte dans votre compte de travail d’entreprise (client d’Azure AD), pas seulement dans votre compte espace partenaires. Si vous utilisez principalement cette application Azure AD pour l’authentification des partenaires et que vous n’avez pas aux utilisateurs d’y accéder directement, vous pouvez entrer n’importe quelle adresse valide pour le **URL de réponse** et **URI ID d’application**, tant en tant que ces valeurs ne sont pas utilisés par une autre application Azure AD dans votre répertoire.

1.  À partir de la **utilisateurs** page (sous **paramètres du compte**), sélectionnez **ajouter des applications Azure AD**.
2.  Sur la page suivante, sélectionnez **New Azure AD application**.
3.  Entrez le **URL de réponse** pour la nouvelle application Azure AD. Il s’agit de l’URL où les utilisateurs peuvent se connecter et utiliser votre application Azure AD (parfois également appelée URL de l’application ou URL de connexion). Le **URL de réponse** ne peut pas dépasser 256 caractères et doit être unique dans votre répertoire.
4.  Entrez le **URI ID d’application** pour la nouvelle application Azure AD. Il s’agit d’un identificateur logique de l’application Azure AD qui est présentée lorsqu’une demande d’authentification unique est envoyée à Azure AD. Notez que le **URI ID d’application** doit être unique pour chaque application Azure AD dans votre répertoire. Cet ID ne peut pas dépasser 256 caractères. Pour plus d’informations sur l’URI ID d’application, consultez [intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#changing-the-application-registration-to-support-multi-tenant).
5.  Dans le **rôles** section, spécifiez l’ou les rôles ou les autorisations personnalisées pour l’application Azure AD.
6.  Sélectionnez **Enregistrer**.

Une fois que vous ajoutez ou que vous créez une application Azure AD, vous pouvez revenir à la **utilisateurs** section et sélectionnez le nom de l’application pour passer en revue les paramètres de l’application, y compris l’ID de locataire, ID de Client, l’URL de réponse et URI ID d’application.

#### <a name="remove-an-application"></a>Supprimer une application

Pour supprimer une application à partir de votre compte professionnel (locataire Azure AD), accédez à **utilisateurs** (sous **paramètres du compte**), sélectionnez l’application que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis choisissez **supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer les ou les applications sélectionnées.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gérer les clés pour une application Azure AD

Si votre application Azure AD lit et écrit des données dans Microsoft Azure AD, il devra une clé. Vous pouvez créer des clés pour une application Azure AD en modifiant ses informations dans le centre de partenaires. Vous pouvez également supprimer des clés qui ne sont plus nécessaires.

1.  À partir de la **utilisateurs** page (sous **paramètres du compte**), sélectionnez le nom de l’application Azure AD. Vous verrez toutes les touches actives pour l’application Azure AD, y compris la date à laquelle la clé a été créée et lorsque sa date d’expiration. 
2. Pour supprimer une clé qui n’est plus nécessaire, sélectionnez **supprimer**.
3.  Pour ajouter une nouvelle clé, sélectionnez **ajouter une nouvelle clé**.
4.  Vous verrez un écran montrant la **ID Client** et **les valeurs de clé**. Veillez à les imprimer ou copier ces informations, comme vous ne pourrez pas y accéder à nouveau une fois que vous quittez cette page.
4.  Si vous souhaitez créer plus de clés, sélectionnez **ajouter une autre clé**.


### <a name="define-user-roles-and-permissions"></a>Définir des autorisations et rôles d’utilisateur

Les utilisateurs de votre entreprise peuvent avoir les autorisations dans le programme de la place de marché Commercial sur les partenaires et les rôles suivants. 

Notez les rôles de locataire Azure Active Directory (AAD) incluent l’administrateur général, administrateur d’utilisateurs et rôles CSP. Rôles non-AAD sont les rôles que vous ne gérez pas le locataire et elles incluent MPN admin, administrateur de profil d’entreprise, référence administrateur, administrateur offre incitative et offre incitative utilisateur.


|**Rôle**|**autorisations**|
|----------------------------------|:---------------------------------|
|Administrateur général|• Peuvent accéder à tous les comptes/services Microsoft avec privilèges complets
|      |• Créer des tickets de support pour l’espace partenaires
||• Afficher les contrats, des listes de prix et des offres
||• Afficher, créer et gérer des utilisateurs partenaires|
|Responsable|• Peuvent accéder à toutes les fonctionnalités de compte Microsoft, à l’exception des paramètres de taxe et de paiement
|      |• Peuvent gérer les utilisateurs, rôles et utiliser des comptes (clients)|
|Développeur|• Peut charger des packages, soumettre des applications et des modules complémentaires et afficher le rapport d’utilisation pour plus d’informations de télémétrie
|      |• Ne peut pas accéder à des paramètres de compte ou des informations financières|
|Collaborateur de l’entreprise|• Peuvent accéder aux informations financières et définir les détails de la tarification
|      |• Ne peut pas créer ou soumettre des modules complémentaires et des nouvelles applications|
|Contributeur financier|• Peuvent afficher des rapports de paiement
|      |• Ne peut pas apporter des modifications à des applications ou des paramètres|
|Mercaticien|• Peut répondre à des avis de clients et des rapports non financiers
|      |• Ne peut pas apporter des modifications à des applications ou des paramètres|

Pour plus d’informations sur la gestion des rôles et les autorisations d’autres aspects de partenaires, tels que Azure Active Directory (AD), fournisseur de solutions Cloud (CSP), fournisseur de panneau de contrôle (CPV), les utilisateurs invités ou Microsoft Partner Network (MPN), consultez [affecter rôles d’utilisateurs et les autorisations dans partenaires](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gérer les locataires

Un locataire Azure Active Directory (AD), également appelé votre « compte professionnel » dans cette documentation, est une représentation de votre organisation définies dans le portail Azure et vous aide à gérer une instance spécifique de services de cloud de Microsoft pour un usage interne et les utilisateurs externes. Si votre organisation est abonnée à un service de cloud Microsoft, tels que Azure, Microsoft Intune ou Office 365, un locataire Azure AD a été établi pour vous. 

Vous pouvez configurer plusieurs clients à utiliser avec les partenaires. Tout utilisateur ayant le **Manager** rôle dans le compte de partenaires aura l’option pour ajouter et supprimer des locataires Azure AD à partir du compte.  

### <a name="add-an-existing-tenant"></a>Ajouter un locataire existant

Pour associer un autre locataire Azure AD à votre compte espace partenaires :

1.  À partir de la **locataires** page (sous **paramètres du compte**), sélectionnez **associer un autre locataire Azure AD**.
2. Entrez vos informations d’identification Azure AD pour le client que vous souhaitez associer.
3. Passez en revue l’organisation et nom de domaine pour votre locataire Azure AD. Pour terminer l’association, sélectionnez **confirmer**.

Si l’association est réussie, vous serez ensuite prêt à ajouter et gérer des utilisateurs de compte dans le **utilisateurs** section dans l’espace partenaires.

### <a name="create-a-new-tenant"></a>Créer un nouveau locataire

Pour créer un locataire marque nouveau répertoire Azure AD avec votre compte espace partenaires :

1.  À partir de la **locataires** page (sous **paramètres du compte**), sélectionnez **Azure AD de créer un nouveau locataire**.
2. Entrez les informations de répertoire pour votre nouvelle annonce Azure :
    - **Nom de domaine** : Le nom unique que nous allons utiliser pour votre domaine Azure AD, avec «. onmicrosoft.com ». Par exemple, si vous avez entré « exemple », votre domaine Azure AD serait « example.onmicrosoft.com ».
    - **Adresse de messagerie**: Une adresse de messagerie où nous pouvons vous contacter sur votre compte si nécessaire.
    - **Informations de compte d’utilisateur administrateur général**: Le prénom, dernier nom, nom d’utilisateur et mot de passe que vous souhaitez utiliser pour le nouveau compte d’administrateur général.
3. Sélectionnez **créer** pour confirmer les nouvelles informations de compte et de domaine.
4. Connectez-vous avec votre nouveau Azure AD administrateur général username et mot de passe pour commencer [Ajout et la gestion des utilisateurs](#manage-users).

Pour plus d’informations sur la création de nouveaux locataires à l’intérieur de votre portail Azure, plutôt que via le portail espace partenaires, consultez l’article [créer un nouveau locataire dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Supprimer un client

Pour supprimer un client à partir de votre compte espace partenaires, recherchez son nom sur le **locataires** page (dans **paramètres du compte**), puis sélectionnez **supprimer**. Vous allez être invité à confirmer que vous souhaitez supprimer le locataire. Une fois que vous procédez ainsi, aucun utilisateur dans ce client ne sera en mesure de vous connecter au compte de partenaires et les autorisations que vous avez configurée pour ces utilisateurs seront supprimées.

Lorsque vous supprimez un client, tous les utilisateurs qui ont été ajoutés au compte de partenaires à partir de ce client ne seront n’est plus en mesure de se connecter au compte.

> [!TIP]
> Vous ne pouvez pas supprimer un locataire si vous êtes actuellement connecté au Partner Center à l’aide d’un compte dans le même client. Pour supprimer un client, vous devez vous connecter à des partenaires comme un **Manager** pour un autre client qui est associé au compte. S’il n'existe qu’un seul locataire associé au compte, ce client peut uniquement être supprimé après vous être connecté avec le compte Microsoft qui a ouvert le compte.


## <a name="agreements"></a>Accords

Le **accords** section de partenaires (sous **comptable**) nous allons vous permet d’afficher une liste des contrats de publication que vous avez autorisé. Ces contrats sont répertoriés par nom et numéro de version, la date, qu'elle a été acceptée et le nom de l’utilisateur qui a accepté le contrat. 

**Les actions nécessaires** peut apparaître en haut de cette page s’il existe des mises à jour de contrat qui nécessitent votre attention. Pour accepter un accord de mise à jour, commencez par lire la Version de contrat lié, puis sélectionnez **accepter le contrat**. 

Pour plus d’informations sur les contrats de fournisseur de solutions Cloud (CSP) dans l’espace partenaires, visitez [accords de Cloud Microsoft par région et langue](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Étapes suivantes

- [Créez une offre SaaS](./create-new-saas-offer.md)
