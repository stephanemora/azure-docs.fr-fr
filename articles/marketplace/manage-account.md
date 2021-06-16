---
title: Procédure de gestion d’un compte Place de marché commerciale dans l’Espace partenaires Microsoft - Place de marché Azure
description: Découvrez comment gérer un compte Place de marché commerciale dans l’Espace partenaires Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: fa09a03a9ffda04e09bbd5708e97334304013078
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006578"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Gérer votre compte Place de marché commerciale dans l’Espace partenaires

**Rôles appropriés**

- Propriétaire
- Manager

Une fois que vous avez [créé un compte Espace partenaires](./create-account.md), vous pouvez utiliser le [tableau de bord de la Place de marché commerciale](https://go.microsoft.com/fwlink/?linkid=2165290) pour gérer votre compte et les offres.

## <a name="access-your-account-settings"></a>Accéder à vos paramètres de compte

Si ce n’est déjà fait, vous (ou l’administrateur de votre organisation) devez accéder aux [paramètres du compte](https://go.microsoft.com/fwlink/?linkid=2165291) de votre compte Espace partenaires.

1. Connectez-vous au [tableau de bord de la place de marché commerciale](https://go.microsoft.com/fwlink/?linkid=2165290) dans l’Espace partenaires à l’aide du compte auquel vous souhaitez accéder. Si vous êtes membre de plusieurs comptes et que vous vous êtes connecté avec un autre compte, vous pouvez [changer de compte](switch-accounts.md).
1. En haut à droite, sélectionnez **Paramètres** (icône d’engrenage), puis **Paramètres du compte**.

    [ ![Capture d’écran du menu Paramètres du compte dans l’Espace partenaires.](./media/manage-accounts/settings-account.png) ](./media/manage-accounts/settings-account.png#lightbox)

1. Sous **Paramètres du compte**, sélectionnez **Légal**. Sélectionnez ensuite l’onglet **Développeur** pour afficher les détails relatifs à votre compte Place de marché commerciale.

    [ ![Capture d’écran de l’onglet Développeur sur la page Légal dans Paramètres du compte.](./media/manage-accounts/developer-tab.png) ](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Page Paramètres du compte

Lorsque vous sélectionnez **Paramètres** et développez **Paramètres du compte**, l’affichage par défaut est **Informations légales**. Cette page peut comporter jusqu’à trois onglets, en fonction des programmes auxquels vous vous êtes inscrit : _Partenaire_, _Revendeur_ et _Développeur_.

L’onglet **Partenaire**, destiné aux partenaires inscrits à MPN, comprend les éléments suivants :

- Toutes les informations légales sur l’entreprise, telles que le nom légal enregistré et l’adresse de votre entreprise
- Contact principal
- Sites de l’entreprise.

L’onglet **Revendeur**, destiné aux partenaires qui sont fournisseurs de solutions cloud, comprend les éléments suivants :

- Coordonnées du contact principal
- Profil de support client
- Informations sur le programme

L’onglet **Développeur**, destiné aux partenaires inscrits à un programme de développement, contient les informations suivantes :

- **Détails du compte** : le type de compte et l’état du compte
- **ID de l’éditeur** : ID de vendeur, ID d’utilisateur, ID d’éditeur, locataires Azure AD, etc.
- **Informations de contact** : nom d’affichage de l’éditeur, contact du vendeur (nom, adresse e-mail, téléphone et adresse) et approbateur de l’entreprise (nom, adresse e-mail, téléphone)

### <a name="account-settings---developer-tab"></a>Paramètres du compte - Onglet Développeur

Les informations suivantes décrivent les informations de l’onglet Développeur.

#### <a name="account-details"></a>Détails du compte

Dans la section _Détails du compte_ de l’onglet _Développeur_, vous pouvez consulter des informations de base, telles que votre **Type de compte** (entreprise ou personnel) et l’**État de vérification** de votre compte. Pendant le processus de vérification du compte, ces paramètres affichent toutes les étapes nécessaires, y compris la vérification par e-mail, la vérification de l’emploi et la vérification de l’entreprise.

#### <a name="publisher-ids"></a>ID de l’éditeur

Dans la section ID d’éditeur, vous pouvez voir vos **ID Symantec** (le cas échéant), **ID de vendeur**, **ID d’utilisateur**, **ID MPN** et **locataires Azure AD**. Ces valeurs sont assignées par Microsoft uniquement pour identifier votre compte de développeur et ne peuvent pas être modifiées.

Si vous n’avez pas d’ID Symantec, vous pouvez sélectionner le lien pour en demander un.

### <a name="contact-info"></a>Informations de contact

Dans la section _Informations de contact_, vous pouvez voir le **Nom d’affichage de l’éditeur**, les **Informations de contact du vendeur** (le nom du contact, l’adresse e-mail, le numéro de téléphone et l’adresse du vendeur de l’entreprise) et l’**Approbateur de l’entreprise** (le nom, l’e-mail et le numéro de téléphone de la personne autorisée à approuver les décisions de l’entreprise).

Vous pouvez également sélectionner le lien **Mettre à jour** pour modifier vos informations de contact, telles que le nom d’affichage et l’adresse e-mail de l’éditeur.

### <a name="account-settings-identifiers"></a>Identificateurs des paramètres du compte

Sous **Paramètres du compte** > **Profil de l’organisation**, sélectionnez **Identificateurs** pour afficher les informations suivantes :

- **ID MPN** : tous les ID MPN associés à votre compte
- **CSP** : ID MPN associés au programme CSP pour ce compte
- **Éditeur** : ID de vendeur associés à votre compte
- **GUID de suivi** : tous les GUID de suivi associés à votre compte

#### <a name="tracking-guids"></a>GUID de suivi

Les identificateurs globaux uniques (GUID) sont des nombres de référence uniques (avec 32 chiffres hexadécimaux) qui peuvent être utilisés pour surveiller votre utilisation d’Azure.

Pour créer un GUID et effectuer son suivi, vous devez utiliser un générateur GUID. L’équipe de stockage Azure a créé un [formulaire générateur de GUID](https://aka.ms/StoragePartners) qui vous enverra par e-mail un GUID au format approprié et pourra être réutilisé dans les différents systèmes de suivi.

Nous vous recommandons de créer un GUID unique pour chaque offre et canal de distribution de chaque produit. Si vous ne souhaitez pas que les rapports soient scindés, vous pouvez choisir d'utiliser un GUID unique pour les différents canaux de distribution du produit.

Si vous déployez un produit à l’aide d’un modèle et qu’il est disponible à la fois sur la Place de marché Azure et sur GitHub, vous pouvez créer et enregistrer deux GUID distincts :

- Produit A sur la Place de marché Azure
- Produit A sur GitHub

Les rapports sont établis à l'aide de la valeur de partenaire (ID partenaire Microsoft) et des GUID. Vous pouvez également suivre les GUID alignés sur chaque plan au sein de l’offre à un niveau plus granulaire.

Pour plus d’informations, consultez la [FAQ Suivi de l’utilisation d’Azure des clients avec des GUID](azure-partner-customer-usage-attribution.md#faq).

### <a name="agreements"></a>Accords

La page **Contrats** vous permet d’afficher la liste des contrats de publication que vous avez autorisés. Ces contrats sont répertoriés selon le nom, le numéro de version, la date à laquelle ils ont été acceptés et le nom de l’utilisateur qui a accepté le contrat.

Pour accéder à la page Contrats :

1. Connectez-vous à l’[Espace partenaires](https://go.microsoft.com/fwlink/?linkid=2165507).
1. En haut à droite, sélectionnez **Paramètres** > **Paramètres du compte**.
1. Sous **Paramètres du compte**, sélectionnez **Contrats**.

Les **actions nécessaires** peuvent apparaître en haut de cette page s’il existe des mises à jour de contrat qui nécessitent votre attention. Pour accepter un contrat modifié, commencez par lire la version du contrat liée, puis sélectionnez **Accepter le contrat**.

## <a name="set-up-a-payout-profile"></a>Paramétrer un profil de paiement

Un profil de paiement est le compte bancaire auquel les recettes sont envoyées en fonction de vos ventes. Ce compte bancaire doit se trouver dans le même pays/la même région que celui/celle où vous avez enregistré votre compte Espace partenaires. Pour plus d’informations sur un profil de paiement, consultez [Créer et gérer des profils fiscaux et de paiement des primes incitatives dans l’Espace partenaires](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) et [Configurer votre compte de paiement et vos déclarations de taxe](/partner-center/set-up-your-payout-account).

Pour configurer votre profil de paiement :

1. Accédez à la [page Vue d’ensemble de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) dans l’Espace partenaires.
2. Dans la section **Profil**, à côté de **Profil de paiement**, sélectionnez **Mettre à jour**.
3. **Choisir un mode de paiement** : Compte bancaire ou PayPal.
4. **Ajouter des informations de paiement** : Il peut s’agir du type de compte (courant ou épargne), du nom du titulaire du compte, le numéro de compte, le numéro de routage, l’adresse de facturation, le numéro de téléphone ou l’adresse e-mail PayPal. Pour plus d’informations sur l’utilisation de PayPal comme mode de paiement du compte et pour savoir s’il est pris en charge dans votre marché ou région, consultez [Informations PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modification de votre compte de paiement peut retarder vos paiements jusqu’à un cycle de paiement. Ce retard se produit parce que nous devons vérifier le changement de compte, comme nous l’avons fait lorsque vous avez configuré le compte de paiement pour la première fois. Vous serez payé pour le montant total une fois que votre compte aura été vérifié. Tout paiement dû dans le cycle de paiement actuel sera ajouté au cycle suivant  

## <a name="tax-profile"></a>Profil fiscal

Examinez l’état actuel de votre profil de taxe, et confirmez si le **type d’entité** et les **informations du certificat de taxe** qui s’affichent sont corrects. Sélectionnez **Modifier** pour mettre à jour ou complétez les formulaires requis.

Afin d’établir votre statut fiscal, vous devez spécifier votre pays ou région de résidence et votre nationalité, et remplir les formulaires fiscaux appropriés associés à votre pays ou région.

Quel que soit votre pays ou région de résidence ou votre nationalité, vous devez remplir les formulaires fiscaux américains pour vendre des offres via Microsoft. Les partenaires qui répondent à certaines exigences en matière de résidence aux États-Unis doivent remplir un formulaire IRS W-9. Les autres partenaires en dehors des États-Unis doivent remplir un formulaire IRS W-8. Vous pouvez remplir ces formulaires en ligne lorsque vous complétez votre profil fiscal.

Aucun numéro individuel de contribuable (ITIN) n’est nécessaire pour recevoir des paiements de la part de Microsoft ou pour bénéficier des avantages découlant des conventions fiscales.

Vous pouvez compléter et soumettre vos formulaires fiscaux par voie électronique dans l’Espace partenaires. En général, vous n’avez pas besoin d’imprimer ou d’envoyer de formulaires par courrier.

Les différents pays et régions ont des exigences fiscales différentes. La quantité exacte d’impôts que vous devez payer varie selon les pays et les régions dans lesquels vous vendez vos offres. Microsoft vous verse les recettes des ventes et les taxes d’utilisation en votre nom dans certains pays et certaines régions. Ces pays et régions seront listés durant le processus de référencement de votre offre. Dans d’autres pays et régions, en fonction de l’endroit où vous êtes inscrit, vous devrez peut-être directement reverser les ventes et les taxes d’utilisation de vos ventes à l’autorité fiscale locale. En outre, les recettes de vente que vous recevez peuvent être considérées comme du revenu imposable. Nous vous encourageons vivement à contacter l’autorité compétente de votre pays ou région qui vous aidera à déterminer les informations fiscales appropriées pour les transactions commerciales de Microsoft.

Pour plus d’informations sur un profil fiscal, consultez [Créer et gérer des profils fiscaux et de paiement des primes incitatives dans l’Espace partenaires](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) et [Configurer votre compte de paiement et vos déclarations de taxe](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Taux de retenue d’impôt

Les informations que vous envoyez dans vos formulaires fiscaux déterminent le taux de retenue d’impôt approprié. Le taux de retenue d’impôt s’applique uniquement aux ventes que vous réalisez aux États-Unis. Les ventes réalisées dans des régions en dehors des États-Unis ne sont pas soumises à la retenue d’impôt. Le taux de retenue d’impôt peut varier, mais pour la plupart des développeurs inscrits en dehors des États-Unis, le taux par défaut est de 30 %. Vous pouvez réduire ce taux si votre pays ou région a signé une convention fiscale avec les États-Unis.

### <a name="tax-treaty-benefits"></a>Avantages issus des conventions fiscales

Si vous êtes situé en dehors des États-Unis, vous pourrez peut-être tirer parti des avantages liés aux conventions fiscales. Ces avantages varient d’un pays/d’une région à l’autre et peuvent vous aider à réduire le taux d’imposition retenu par Microsoft. Vous pouvez demander des avantages issus des conventions fiscales en complétant la partie II du formulaire W-8BEN. Nous vous conseillons de communiquer avec les ressources appropriées dans votre pays ou région pour déterminer si ces avantages peuvent s’appliquer à vous.

[En savoir plus sur les informations fiscales pour les développeurs d’applications/jeux Windows et les éditeurs de la Place de marché Azure](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>État de paiement en attente

Par défaut, Microsoft vous envoie les paiements sur une base mensuelle. Toutefois, vous avez la possibilité de mettre vos paiements en attente, ce qui empêche l’envoi des paiements à votre compte. Même si vous choisissez de mettre vos paiements en attente, nous continuerons d’enregistrer vos revenus et de vous fournir les informations associées dans votre **Synthèse des paiements**. Toutefois, nous n’enverrons pas de paiements à votre compte tant que vous n’aurez pas retiré la mise en attente.

**Pour mettre vos paiements en attente** :

1. Accédez à **Paramètres du compte**. 
1. Dans le volet de navigation gauche, développez **Paiement et taxes**, puis sélectionnez **Profils de paiement et fiscaux**.
1. Sélectionnez le programme pour lequel vous souhaitez mettre les paiements en attente, puis cochez la case **Conserver mon paiement**.

Vous pouvez modifier votre état de paiement en attente à tout moment, mais n’oubliez pas que votre décision aura un impact sur le paiement mensuel suivant. Par exemple, si vous souhaitez mettre le paiement d’avril en attente, veillez à définir votre état de paiement en attente sur **Activé** avant la fin du mois de mars.

Une fois que vous avez défini votre état de paiement en attente sur **Activé**, tous les paiements seront bloqués jusqu’à ce que vous fassiez glisser le curseur vers **Désactivé**. Dans ce cas, votre paiement sera intégré au prochain cycle mensuel (à condition que les seuils de paiement applicables aient été atteints). Par exemple, si vous avez mis vos paiements en attente, mais que vous souhaitez générer un paiement en juin, faites basculer votre état de paiement en attente sur **Désactivé** avant la fin du mois de mai.

> [!NOTE]
> Votre sélection pour l’**état de paiement en attente** s’applique à **toutes** les sources de revenus payés via l’Espace partenaires Microsoft, notamment la Place de marché Azure, Microsoft AppSource, Microsoft Store, la publicité, etc.). Vous ne pouvez pas sélectionner des états d’attente différents pour chaque source de revenus.

## <a name="devices"></a>Appareils

Les paramètres de gestion des appareils ne s’appliquent qu’à la publication de la plateforme Windows universelle (UWP). [Plus d’informations](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

## <a name="create-a-billing-profile"></a>Créer un profil de facturation

Si vous publiez une offre [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) ou [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md), vous devez compléter votre *profil de facturation*.

L’adresse de facturation est préremplie avec votre entité légale et vous pouvez la mettre à jour ultérieurement. Les champs Taxes et ID TVA sont obligatoires pour certains pays et facultatifs pour d’autres. Le nom du pays/de la région et le nom de la société ne peuvent pas être modifiés.

1. Accédez à **Paramètres du compte**.
1. Ensuite, dans le volet de navigation gauche, développez **Profil de l’organisation** et sélectionnez **Profil de facturation**.


## <a name="multi-user-account-management"></a>Gestion des comptes multi-utilisateurs

L’Espace partenaires utilise [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) pour l’accès aux comptes multi-utilisateurs et la gestion de ceux-ci. L’Azure AD de votre organisation est automatiquement associé à votre compte Espace partenaires dans le cadre du processus d’inscription.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter et gérer des utilisateurs](add-manage-users.md)