---
title: Prise de contrôle administrateur d’un répertoire non managé – Azure AD | Microsoft Docs
description: Prise de contrôle d’un nom de domaine DNS dans une organisation Azure AD non managée (locataire fantôme).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834186"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Prendre le contrôle d’un annuaire non géré en tant qu’administrateur dans Azure Active Directory

Cet article décrit deux façons de prendre le contrôle d’un nom de domaine DNS dans un annuaire non géré dans Azure Active Directory. Quand un utilisateur en libre-service s’inscrit à un service cloud qui utilise Azure AD, il est ajouté à un annuaire Azure AD non géré en fonction de son domaine de messagerie. Pour plus d’informations sur l’inscription en libre-service ou « virale » pour un service, consultez [Présentation de l’inscription en libre-service pour Azure Active Directory](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Décidez comment vous voulez prendre le contrôle d’un annuaire non géré
Lors du processus de prise de contrôle par l’administrateur, vous pouvez prouver la propriété comme décrit dans [Ajouter un nom de domaine personnalisé à Azure AD](../fundamentals/add-custom-domain.md). Les sections suivantes vous expliquent plus en détail l'expérience administrateur, mais en voici un résumé :

* Quand vous effectuez une [prise de contrôle par l’administrateur « interne »](#internal-admin-takeover) d’un annuaire Azure non géré, vous êtes ajouté en tant qu’administrateur général de l’annuaire non géré. Aucun utilisateur, domaine ou plan de service n’est migré vers un autre annuaire que vous administrez.

* Quand vous effectuez une [prise de contrôle par administrateur « externe »](#external-admin-takeover) d’un annuaire Azure non géré, vous ajoutez le nom de domaine DNS de l’annuaire non géré à votre annuaire Azure géré. Quand vous ajoutez le nom de domaine, un mappage des utilisateurs aux ressources est créé dans votre annuaire Azure géré, pour que les utilisateurs puissent continuer à accéder aux services sans interruption. 

## <a name="internal-admin-takeover"></a>Prise de contrôle par administrateur interne

Certains produits qui incluent SharePoint et OneDrive, comme Office 365, ne permettent pas la prise de contrôle externe. Si c’est votre scénario, ou si vous êtes administrateur et que vous voulez prendre le contrôle d’un locataire non géré ou « fantôme » créé par des utilisateurs ayant utilisé l’inscription en libre-service, vous pouvez le faire avec une prise de contrôle par administrateur interne.

1. Créez un contexte utilisateur dans le locataire non géré via une inscription auprès de Power BI. Par commodité pour l’exemple, cette procédure suppose que cette voie est choisie.

2. Ouvrez le [site Power BI](https://powerbi.com) et sélectionnez **Démarrer gratuitement**. Entrez un compte d’utilisateur qui utilise le nom de domaine pour l’organisation ; par exemple, `admin@fourthcoffee.xyz`. Une fois que vous avez entré le code de vérification, vous recevez le code de confirmation dans votre messagerie.

3. Dans l’e-mail de confirmation de Power BI, sélectionnez **Oui, c’est bien moi**.

4. Connectez-vous au [centre d’administration Microsoft 365](https://portal.office.com/admintakeover) avec le compte d’utilisateur Power BI. Vous recevez un message qui vous indique de **devenir l’administrateur** du nom de domaine qui a été déjà vérifié dans le locataire non géré. Sélectionnez **Oui, je veux être l’administrateur**.
  
   ![Première capture d’écran pour Devenir l’administrateur](./media/domains-admin-takeover/become-admin-first.png)
  
5. Ajoutez un enregistrement TXT pour prouver que vous êtes propriétaire du nom de domaine **fourthcoffee.xyz** à votre bureau d’enregistrement de nom de domaine. Dans cet exemple, il s’agit de GoDaddy.com.
  
   ![Ajouter un enregistrement TXT pour le nom de domaine](./media/domains-admin-takeover/become-admin-txt-record.png)

Quand les enregistrements TXT DNS sont vérifiés au niveau de votre bureau d’enregistrement de nom de domaine, vous pouvez gérer le locataire Azure AD.

Une fois les étapes précédentes terminées, vous êtes l’administrateur général du locataire Fourth Coffee dans Office 365. Pour intégrer le nom de domaine à vos autres services Azure, vous pouvez le supprimer d’Office 365 et l’ajouter à un autre locataire géré dans Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Ajout du nom de domaine à un locataire géré dans Azure AD

1. Ouvrez le [centre d’administration Microsoft 365](https://admin.microsoft.com).
2. Sélectionnez l’onglet **Utilisateurs** et créez un compte d’utilisateur avec un nom comme *user\@fourthcoffeexyz.onmicrosoft.com* qui n’utilise pas le nom de domaine personnalisé. 
3. Vérifiez que le compte d’utilisateur dispose des privilèges d’administrateur général pour le locataire Azure AD.
4. Ouvrez l’onglet **Domaines** dans le centre d’administration Microsoft 365, sélectionnez le nom de domaine, puis sélectionnez **Supprimer**. 
  
   ![Supprimer le nom de domaine d’Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Si vous avez des utilisateurs ou des groupes dans Office 365 qui référencent le nom de domaine supprimés, ils doivent être renommés pour le domaine onmicrosoft.com. Si vous forcez la suppression du nom de domaine, tous les utilisateurs sont automatiquement renommés, dans cet exemple en *user\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Connectez-vous au [Centre d’administration d’Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte d’administrateur général pour le locataire Azure AD.
  
7. Sélectionnez **Noms de domaine personnalisés**, puis ajoutez le nom de domaine. Vous devrez entrer les enregistrements TXT DNS pour confirmer la propriété du nom de domaine. 
  
   ![domaine vérifié comme ajouté à Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Les utilisateurs du service Power BI ou Azure Rights Management qui ont des licences affectées dans le locataire Office 365 doivent enregistrer leurs tableaux de bord si le nom de domaine est supprimé. Ils doivent se connecter avec un nom d’utilisateur comme *user\@fourthcoffeexyz.onmicrosoft.com* plutôt que *user\@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Prise de contrôle par administrateur externe

Si vous gérez déjà un client avec les services Azure ou Office 365, vous ne pouvez pas ajouter un nom de domaine personnalisé s’il est déjà vérifié dans un autre client Azure AD. Cependant, depuis votre locataire géré dans Azure AD, vous pouvez prendre le contrôle d’un locataire comme prise de contrôle par administrateur externe. La procédure générale suit l’article [Ajouter un domaine personnalisé à Azure AD](../fundamentals/add-custom-domain.md).

Quand vous confirmez la propriété du nom de domaine, Azure AD supprime le nom de domaine du locataire non géré et le déplace dans votre locataire existant. La prise de contrôle par administrateur externe d’un annuaire non géré nécessite le même processus de validation TXT DNS que la prise de contrôle par administrateur interne. La différence est que les éléments suivants sont également déplacés avec le nom de domaine :

- Utilisateurs
- Abonnements
- Affectations de licences

### <a name="support-for-external-admin-takeover"></a>Prise en charge de la prise de contrôle par administrateur externe
La prise de contrôle par administrateur externe est prise en charge par les services en ligne suivants :

- Azure Rights Management
- Exchange Online

Les plans de service pris en charge sont les suivants :

- PowerApps Gratuit
- PowerFlow Gratuit
- RMS pour les particuliers
- Microsoft Stream
- Version d’évaluation gratuite de Dynamics 365

La prise de contrôle par administration externe n’est pas prise en charge pour les services dont les plans de service incluent SharePoint, OneDrive ou Skype entreprise, par exemple, via un abonnement gratuit à Office. 

Sinon, vous pouvez recourir à l’option [**ForceTakeover**](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) pour retirer le nom de domaine du client non géré et le vérifier sur le client souhaité. 

#### <a name="more-information-about-rms-for-individuals"></a>En savoir plus sur RMS pour les particuliers

Pour [RMS pour les particuliers](/azure/information-protection/rms-for-individuals), où le client non géré se trouve dans la même région que le client que vous détenez, la [clé de locataire Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) et les [modèles de protection par défaut](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) sont également déplacés avec le nom de domaine.

La clé et les modèles ne sont pas déplacés lorsque le client non géré se trouve dans une région différente. Par exemple, si le client non géré se trouve en Europe et votre organisation en Amérique du Nord.

Bien que RMS pour les particuliers soit conçu pour prendre en charge l’authentification Azure AD sur le contenu protégé ouvert, cette solution n’empêche pas les utilisateurs de protéger également le contenu. Si les utilisateurs protègent le contenu avec l’abonnement RMS pour les particuliers et que la clé et les modèles n’ont pas été déplacés, le contenu n’est plus accessible une fois le domaine pris en charge.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Applets de commande Azure AD PowerShell pour l’option ForceTakeover
Vous pouvez voir ces applets de commande utilisées dans un [exemple PowerShell](#powershell-example).

Applet de commande | Usage
------- | -------
`connect-msolservice` | Quand vous y êtes invité, connectez-vous à votre locataire géré.
`get-msoldomain` | Affiche vos noms de domaine associés au locataire actif.
`new-msoldomain –name <domainname>` | Ajoute le nom de domaine au locataire avec l’état Non vérifié (aucune vérification DNS n’a encore été effectuée).
`get-msoldomain` | Le nom de domaine est désormais inclus dans la liste des noms de domaine associés à votre locataire géré, mais il apparaît avec l’état **Non vérifié**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Fournit les informations à placer dans le nouvel enregistrement TXT DNS pour le domaine (MS=xxxxx). La vérification peut ne pas se produire immédiatement, car un certain temps est nécessaire à la propagation de l’enregistrement TXT : attendez donc quelques minutes avant d’envisager l’utilisation de l’option **-ForceTakeover**. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Si votre nom de domaine n’est pas encore vérifié, vous pouvez appliquer l’option **-ForceTakeover**. Elle vérifie que l’enregistrement TXT a été créé et lance le processus de prise de contrôle.<li>L’option **-ForceTakeover** doit être ajoutée à l’applet de commande seulement quand la prise de contrôle par administrateur externe est forcée, par exemple quand des services Office 365 bloquent la prise de contrôle du locataire non géré.
`get-msoldomain` | La liste des domaines affiche maintenant le nom de domaine avec l’état **Vérifié**.

> [!NOTE]
> L’organisation Azure AD non gérée est supprimée 10 jours après l’utilisation de l’option prise de contrôle forcée externe.

### <a name="powershell-example"></a>Exemple PowerShell

1. connectez-vous à Azure AD en utilisant les informations d’identification qui ont été utilisées pour répondre à l’offre en libre-service :
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Obtenez une liste de domaines :
  
   ```powershell
   Get-MsolDomain
   ```
3. Exécutez l’applet de commande Get-MsolDomainVerificationDns pour créer un test :
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Par exemple :
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Copiez la valeur (le test) qui est renvoyée depuis cette commande. Par exemple :
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Dans votre espace de noms DNS public, créez un enregistrement DNS txt qui contient la valeur que vous avez copiée lors de l'étape précédente. Le nom de cet enregistrement est le nom du domaine parent : ainsi, si vous créez cet enregistrement de ressource en utilisant le rôle DNS de Windows Server, laissez vide le nom de l’enregistrement et collez simplement la valeur dans la zone de texte.
6. Exécutez l'applet de commande Confirm-MsolDomain pour vérifier le test :
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Par exemple :
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Un test réalisé avec succès vous renvoie à l'invite sans erreur.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter un nom de domaine personnalisé à Azure AD](../fundamentals/add-custom-domain.md)
* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Guide de référence des applets de commande Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
