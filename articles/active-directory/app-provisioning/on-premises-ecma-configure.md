---
title: Configuration de l’hôte du connecteur Azure AD ECMA
description: Cet article explique la procédure de configuration de l’Hôte de connecteur ECMA Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 769b5a312572a2c10f55ee3efbb54191b4f13707
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449689"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>Configurer l’hôte du connecteur Azure AD ECMA et l’agent d’approvisionnement

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Cet article fournit des conseils sur la configuration de l’Hôte de connecteur ECMA Azure Active Directory (Azure AD) et de l’agent d’approvisionnement une fois que vous l’avez installé avec succès.

Ce flux vous guide tout au long du processus d’installation et de configuration de l’Hôte connecteur ECMA Azure AD. 

 ![Diagramme montrant le flux d’installation.](./media/on-premises-ecma-configure/flow-1.png)

Pour plus d’informations sur l’installation et la configuration, consultez :
   - [Configurations requises pour l’hôte du connecteur Azure AD ECMA](on-premises-ecma-prerequisites.md)
   - [Installation de l’hôte du connecteur Azure AD ECMA](on-premises-ecma-install.md)
   - [Configuration du connecteur SQL générique pour l’Hôte de connecteur ECMA Azure AD](on-premises-sql-connector-configure.md)
   
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Configurer l’Hôte de connecteur ECMA Azure AD
La configuration de l’Hôte du connecteur ECMA Azure AD se fait en deux parties :
    
   - **Configurez les paramètres** : configurez le port et le certificat pour l’Hôte du connecter ECMA Azure AD à utiliser. Cette étape est effectuée uniquement lors du premier démarrage de l’Hôte du connecteur ECMA.
   - **Créez un connecteur** : créez un connecteur (SQL ou LDAP) pour permettre à l’Hôte du connecteur ECMA Azure AD d’exporter ou d’importer des données vers une source de données.

### <a name="configure-the-settings"></a>Configurer les paramètres
Lorsque vous démarrez pour la première fois l’Hôte du connecteur ECMA Azure AD, vous verrez un numéro de port qui est rempli avec la valeur par défaut **8585**. 

 ![Capture d’écran montrant la configuration de vos paramètres.](.\media\on-premises-ecma-configure\configure-1.png)

Pour la préversion, vous devez générer un nouveau certificat auto-signé.

 >[!NOTE]
 >Cette préversion utilise un certificat sensible au temps. Le certificat généré automatiquement sera auto-signé. Une partie de la racine de confiance et du réseau SAN correspond au nom d’hôte.


### <a name="create-a-connector"></a>Créer un connecteur
Vous devez maintenant créer un connecteur pour l’Hôte du connecter ECMA Azure AD à utiliser. Ce connecteur permettra à l’hôte du connecteur ECMA d’exporter des données vers la source de données pour le connecteur que vous créez. Vous pouvez également l’utiliser pour importer des données si vous le souhaitez.

Les étapes de configuration de chacun des connecteurs individuels sont plus longues et sont fournies dans leurs propres documents.

Pour créer et configurer un connecteur, utilisez le [connecteur SQL générique](on-premises-sql-connector-configure.md). Ce connecteur fonctionnera avec les bases de données Microsoft SQL, comme Azure SQL Database ou Azure Database pour MySQL.

## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>Établir la connectivité entre Azure AD et l’Hôte du connecteur ECMA Azure AD
Les sections suivantes vous guident tout au long de l’établissement de la connectivité avec l’Hôte du connecteur ECMA Azure AD local et Azure AD.

### <a name="ensure-the-ecma2host-service-is-running"></a>Vérifier que le service ECMA2Host est en cours d’exécution
1. Sur le serveur sur lequel s’exécute l’Hôte du connecteur ECMA Azure AD, sélectionnez **Démarrer**.
1. Entrez **run** et tapez **services.msc** dans la zone.
1. Dans la liste **Services**, vérifiez que **Microsoft ECMA2Host** est présent et en cours d’exécution. Si ce n’est pas le cas, sélectionnez **Démarrer**.

   ![Capture d’écran montrant que le service est en cours d’exécution.](.\media\on-premises-ecma-configure\configure-2.png)

### <a name="add-an-enterprise-application"></a>Ajouter une application d’entreprise
1. Connectez-vous au Portail Azure en tant qu’administrateur d’application.
1. Dans le portail, accédez à **Azure Active Directory** > **Applications d’entreprise**.
1. Sélectionnez **Nouvelle application**.

   ![Capture d’écran montrant l’ajout d’une nouvelle application.](.\media\on-premises-ecma-configure\configure-4.png)
1. Localisez l’application **d’approvisionnement local** depuis la galerie et sélectionnez **Créer**.

### <a name="configure-the-application-and-test"></a>Configurer l’application et tester
  1. Une fois l’application créée, sélectionnez la page **Approvisionnement**.
  1. Sélectionnez **Prise en main**.
  
     ![Capture d’écran montrant le Démarrage.](.\media\on-premises-ecma-configure\configure-6.png)
  1. Sur la page **Approvisionnement**, définissez le **Mode Approvisionnement** sur **Automatique**.

      ![Capture d’écran montrant le changement de mode.](.\media\on-premises-ecma-configure\configure-7.png)
  1. Dans la section **Connectivité locale**, sélectionnez l’agent que vous venez de déployer, puis **Attribuer les agents**.
  
      ![Capture d’écran montrant l’attribution d’un agent.](.\media\on-premises-ecma-configure\configure-8.png)</br>

      >[!NOTE]
      >Après avoir ajouté l’agent, patientez 10 à 20 minutes que l’inscription se termine. Le test de connectivité ne fonctionne pas tant que l’inscription n’est pas terminée.
      >
      >Vous pouvez également forcer l’inscription de l’agent en redémarrant l’agent de provisionnement sur votre serveur. Accédez à votre serveur, recherchez **services** dans la barre de recherche Windows, identifiez **Azure AD Connect Provisioning Agent Service**, cliquez avec le bouton droit sur le service, puis redémarrez.

  1. Après 10 minutes, dans la section **Informations de connexion de l’administrateur**, entrez l’URL suivante. Remplacez la partie `"connectorName"` par le nom du connecteur sur l’Hôte ECMA.

      |Propriété|Valeur|
      |-----|-----|
      |URL de locataire|https://localhost:8585/ecma2host_connectorName/scim|

  1. Entrez la valeur du jeton secret que vous avez définie lors de la création du connecteur.
  1. Sélectionnez **Tester la connection** et patientez une minute.
  
     ![Capture d’écran montrant Tester la connexion.](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >Veillez à attendre 10 à 20 minutes après avoir affecté l’agent pour tester la connexion. La connexion échoue si l’inscription n’est pas terminée.

  1. Une fois le test de connexion réussi, sélectionnez **Save**.</br>
  
     ![Capture d’écran montrant un test réussi.](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-whos-in-scope-for-provisioning"></a>Configurer les personnes qui sont dans l’étendue de l’approvisionnement
Maintenant que l’hôte du connecteur ECMA Azure AD est capable de communiquer avec Azure AD, vous pouvez passer à la configuration de l’étendue du provisionnement. Les sections suivantes fournissent des informations sur l’étendue de vos utilisateurs.

### <a name="assign-users-to-your-application"></a>Attribuer des utilisateurs à vos applications
En utilisant Azure AD, vous pouvez étendre qui peut être approvisionné en fonction de l’attribution à une application ou en filtrant sur un attribut en particulier. Déterminez qui doit se trouver dans l’étendue de l’approvisionnement et définissez vos règles d’étendue, si nécessaire. Pour plus d’informations, consultez [Gérer l’attribution d’utilisateur pour une application dans Azure Active Directory](../../active-directory/manage-apps/assign-user-or-group-access-portal.md).

### <a name="configure-your-attribute-mappings"></a>Configurer les mappages d'attributs
À présent, vous mappez les attributs utilisateur dans Azure AD aux attributs dans l’application cible. Le service d’approvisionnement Azure AD s’appuie sur la norme SCIM pour l’approvisionnement. Par conséquent, les attributs de surface ont l’espace de noms SCIM. L’exemple suivant montre comment mapper les attributs **mail** et **objectId** dans Azure AD sur les attributs **Email** et **InternalGUID** dans une application.

>[!NOTE]
>Le mappage par défaut connecte **userPrincipalName** à un nom d’attribut *PLACEHOLDER*. Vous devez changer l’attribut *PLACEHOLDER* pour un attribut de votre application. Pour plus d’informations, consultez [Correspondance des utilisateurs dans les systèmes source et cible](customize-application-attributes.md#matching-users-in-the-source-and-target--systems).

|Nom d’attribut dans Azure AD|Nom d’attribut dans SCIM|Nom d’attribut dans l’application cible|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|Courrier|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

### <a name="configure-attribute-mapping"></a>Configurer le mappage d’attributs
 1. Dans le portail Azure AD, sous **Applications d’entreprise**, sélectionnez la page **Provisionnement**.
 2. Sélectionnez **Prise en main**.
 3. Développez **Mappages** et sélectionnez **Provisionner les utilisateurs Azure Active Directory**.
 
    ![Capture d’écran montrant Approvisionner les utilisateurs d’Azure Active Directory.](.\media\on-premises-ecma-configure\configure-10.png)
 1. Sélectionnez **Ajouter un mappage**.
 
    ![Capture d’écran montrant Ajouter un mappage.](.\media\on-premises-ecma-configure\configure-11.png)
 1. Spécifiez les attributs source et cible, puis sélectionnez **OK**.</br>
 
    ![Capture d’écran montrant le panneau Modifier l’attribut.](.\media\on-premises-ecma-configure\configure-12.png)


Pour plus d’informations sur le mappage des attributs utilisateur des applications à Azure AD, consultez [Didacticiel : personnaliser l’attribut de configuration d’utilisateur-mappages pour les applications SaaS dans Azure Active Directory](customize-application-attributes.md).

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>Tester votre configuration en approvisionnant les utilisateurs à la demande
Pour tester votre configuration, vous pouvez utiliser l’approvisionnement des utilisateurs à la demande. Pour plus d’informations sur l’approvisionnement des utilisateurs à la demande, consultez [Approvisionnement à la demande](provision-on-demand.md).

 1. Accédez au volet de l’authentification unique, puis revenez au volet approvisionnement. Sur le nouveau volet de présentation d’approvisionnement, sélectionnez **À la demande**.
 1. Testez l’approvisionnement de quelques utilisateurs à la demande, comme décrit dans [Approvisionnement à la demande dans Azure Active Directory](provision-on-demand.md).
 
    ![Capture d’écran montrant le test du provisionnement.](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>Démarrer le provisionnement des utilisateurs
 1. Une fois l’approvisionnement à la demande réussi, revenez à la page de configuration de l’approvisionnement. Vérifiez que l’étendue est définie sur les seuls utilisateurs et groupes attribués, **activez** l’approvisionnement, puis sélectionnez **Enregistrer**.
 
    ![Capture d’écran montrant le démarrage de l’approvisionnement.](.\media\on-premises-ecma-configure\configure-14.png)

1. Attendez que le provisionnement démarre. Cela peut prendre jusqu’à 40 minutes. Une fois le travail d’approvisionnement terminé, comme décrit dans la section suivante, vous pouvez modifier l’état de l’approvisionnement en **Désactivé** et sélectionner **Enregistrer**. Cette action empêche que le service d’approvisionnement s’exécute à l’avenir.

### <a name="verify-users-were-successfully-provisioned"></a>Vérifier que les utilisateurs ont été correctement approvisionnés
Après l’attente, vérifiez votre source de données pour voir si de nouveaux utilisateurs sont approvisionnés.

 ![Capture d’écran montrant la vérification d’approvisionnement des utilisateurs.](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>Surveiller votre déploiement

1. Utilisez les journaux de provisionnement pour déterminer pour quels utilisateurs le provisionnement a réussi et pour quels utilisateurs il a échoué.
1. Créez des alertes, des tableaux de bord et des requêtes personnalisés à l’aide de l’intégration Azure Monitor.
1. Si la configuration de l’approvisionnement semble être dans un état non sain, l’application passe en quarantaine. En savoir plus sur les [états de quarantaine](https://github.com/MicrosoftDocs/azure-docs-pr/compare/application-provisioning-quarantine-status.md?expand=1).

## <a name="next-steps"></a>Étapes suivantes

- [Prérequis de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-prerequisites.md)
- [Installation de l’Hôte de connecteur ECMA Azure AD](on-premises-ecma-install.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
