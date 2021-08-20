---
title: Tutoriel sur le connecteur SQL générique pour l’hôte de connecteur ECMA Azure AD
description: Ce tutoriel explique comment utiliser le connecteur SQL générique de provisionnement d’applications locales.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/13/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ef75392676b2c766064eefb3c8113734bfa622
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437533"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-tutorial"></a>Tutoriel sur le connecteur SQL générique pour l’hôte de connecteur ECMA Azure AD

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Ce tutoriel décrit les étapes à effectuer pour provisionner et déprovisionner automatiquement des utilisateurs d’Azure AD (Azure Active Directory) dans une base de données SQL. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).

Ce tutoriel explique comment configurer et utiliser le connecteur SQL générique avec l’hôte de connecteur ECMA Azure AD. 

## <a name="prepare-the-sample-database"></a>Préparer l’exemple de base de données
Sur un serveur exécutant SQL Server, exécutez le script SQL qui se trouve dans l’[Annexe A](#appendix-a). Ce script crée un exemple de base de données avec le nom CONTOSO. Il s’agit de la base de données dans laquelle vous allez provisionner les utilisateurs.


## <a name="create-the-dsn-connection-file"></a>Créer le fichier de connexion DSN
Le connecteur SQL générique est un fichier DSN permettant de se connecter au serveur SQL. Vous devez tout d’abord créer un fichier avec les informations de connexion ODBC.

1. Démarrez l’utilitaire de gestion ODBC sur votre serveur.
  
     ![Capture d’écran montrant la gestion ODBC.](./media/tutorial-ecma-sql-connector/odbc.png)
1. Accédez à l’onglet **DSN de fichier** et sélectionnez **Ajouter**. 
 
     ![Capture d’écran montrant l’onglet DSN de fichier.](./media/tutorial-ecma-sql-connector/dsn-2.png)
1. Sélectionnez **SQL Server Native Client 11.0** et **Suivant**. 
 
     ![Capture d’écran montrant le choix d’un client natif.](./media/tutorial-ecma-sql-connector/dsn-3.png)
1. Nommez le fichier, par exemple **GenericSQL**, et sélectionnez **Suivant**. 
 
     ![Capture d’écran montrant le nommage du connecteur.](./media/tutorial-ecma-sql-connector/dsn-4.png)
1. Sélectionnez **Terminer**. 
 
     ![Capture d’écran montrant l’option Terminer.](./media/tutorial-ecma-sql-connector/dsn-5.png)
1. Configurez maintenant la connexion. Entrez **APP1** comme nom du serveur, puis sélectionnez **Suivant**.

     ![Capture d’écran qui montre l’entrée d’un nom de serveur.](./media/tutorial-ecma-sql-connector/dsn-6.png)
1. Conservez l’authentification Windows et sélectionnez **Suivant**.

     ![Capture d’écran qui montre l’authentification Windows.](./media/tutorial-ecma-sql-connector/dsn-7.png)
1. Entrez le nom de l’exemple de base de données, à savoir **CONTOSO**.

     ![Capture d’écran qui montre l’entrée d’un nom de base de données.](./media/tutorial-ecma-sql-connector/dsn-8.png)
1. Conservez toutes les valeurs par défaut sur cet écran, et sélectionnez **Terminer**.

     ![Capture d’écran qui montre l’option Terminer sélectionnée.](./media/tutorial-ecma-sql-connector/dsn-9.png)
1. Pour vérifier que tout fonctionne comme prévu, sélectionnez **Tester la source de données**. 
 
     ![Capture d’écran qui montre Tester la source de données.](./media/tutorial-ecma-sql-connector/dsn-10.png)
1. Vérifiez que le test a réussi.

     ![Capture d’écran qui montre la réussite de l’opération.](./media/tutorial-ecma-sql-connector/dsn-11.png)
1. Sélectionnez **OK** deux fois. Fermez l’Administrateur de la source de données ODBC.

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Télécharger et installer le package de l’agent d’approvisionnement Azure AD Connect

 1. Connectez-vous au serveur que vous allez utiliser à l’aide des autorisations d’administrateur d’entreprise.
 1. Connectez-vous au portail Azure, puis accédez à **Azure Active Directory**.
 1. Dans le menu de gauche, sélectionnez **Azure AD Connect**.
 1. Sélectionnez **Gérer la synchronisation cloud** > **Passer en revue tous les agents**.
 1. Téléchargez le package de l’agent de provisionnement Azure AD Connect à partir du portail Azure.
 1. Acceptez les conditions générales et sélectionnez **Télécharger**.
 1. Exécutez le programme d’installation du provisionnement Azure AD Connect AADConnectProvisioningAgentSetup.msi.
 1. Sur l’écran **Microsoft Azure AD Connect Provisioning Agent Package**, sélectionnez **Install**.
 
    ![Capture de l’écran Microsoft Azure AD Connect Provisioning Agent Package.](media/on-premises-ecma-install/install-1.png)</br>
 1. Une fois cette opération terminée, l’Assistant Configuration démarre. Sélectionnez **Suivant**.
 
    ![Capture d’écran montrant l’écran d’accueil.](media/on-premises-ecma-install/install-2.png)</br>
 1. Sur l’écran **Select Extension**, sélectionnez **On-premises application provisioning (Azure AD to application)** , puis **Suivant**. 
 
    ![Capture d’écran montrant l’écran de sélection d’extension.](media/on-premises-ecma-install/install-3.png)</br>
 1. Utilisez votre compte d’administrateur général et connectez-vous à Azure AD.
 
     ![Capture d’écran montrant l’écran de connexion à Azure AD.](media/on-premises-ecma-install/install-4.png)</br>
 1. Sur l’écran **Agent configuration**, sélectionnez **Confirm**.
 
     ![Capture d’écran montrant la confirmation de l’installation.](media/on-premises-ecma-install/install-5.png)</br>
 1. Une fois l’installation terminée, un message s’affiche au bas de l’Assistant. Sélectionnez **Quitter**.
 
     ![Capture d’écran montrant le bouton Exit.](media/on-premises-ecma-install/install-6.png)</br>
 
## <a name="configure-the-azure-ad-ecma-connector-host"></a>Configurer l’Hôte de connecteur ECMA Azure AD
1. Sur le Bureau, sélectionnez le raccourci ECMA.
1. Après le démarrage de la configuration de l’hôte du connecteur ECMA, conservez le port par défaut **8585** et sélectionnez **Generate** pour générer un certificat. Le certificat généré automatiquement est auto-signé dans le cadre de la racine de confiance. Le réseau SAN correspond au nom d’hôte.

     ![Capture d’écran montrant la configuration de vos paramètres.](.\media\on-premises-ecma-configure\configure-1.png)
1. Sélectionnez **Enregistrer**.

## <a name="create-a-generic-sql-connector"></a>Créer un connecteur SQL générique
 1. Sélectionnez le raccourci de l’hôte du connecteur ECMA sur le Bureau.
 1. Sélectionnez **Nouveau connecteur**.
 
     ![Capture d’écran montrant le choix du nouveau connecteur.](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. Dans la page **Properties**, renseignez les zones avec les valeurs spécifiées dans le tableau qui suit l’image, puis sélectionnez **Next**.
 
     ![Capture d’écran montrant l’entrée des propriétés.](.\media\tutorial-ecma-sql-connector\conn-1.png)

     |Propriété|Valeur|
     |-----|-----|
     |Nom|SQL|
     |Minuteur de synchronisation automatique (minutes)|120|
     |Jeton secret|Entrez votre propre clé ici. Elle doit comporter 12 caractères au minimum.|
       |Extension DLL|Pour un connecteur SQL générique, sélectionnez **Microsoft.IAM.Connector.GenericSql.dll**.|
 1. Dans la page **Connectivity**, renseignez les zones avec les valeurs spécifiées dans le tableau qui suit l’image, puis sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Connectivity.](.\media\tutorial-ecma-sql-connector\conn-2.png)

     |Propriété|Valeur|
     |-----|-----|
     |Fichier DSN|Accédez au fichier créé au début du tutoriel dans « Créer le fichier de connexion DSN ».|
     |User Name|contoso\administrator|
     |Mot de passe|Entrez le mot de passe de l’administrateur.|
 1. Dans la page **Schema 1**, renseignez les zones avec les valeurs spécifiées dans le tableau qui suit l’image, puis sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Schema 1.](.\media\tutorial-ecma-sql-connector\conn-3.png)

     |Propriété|Valeur|
     |-----|-----|
     |Méthode de détection du type d’objet|Valeur fixe|
     |Liste de valeurs fixes/Table/Vue/SP|Utilisateur|
 1. Dans la page **Schema 2**, renseignez les zones avec les valeurs spécifiées dans le tableau qui suit l’image, puis sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Schema 2.](.\media\tutorial-ecma-sql-connector\conn-4.png)

     |Propriété|Valeur|
     |-----|-----|
     |Utilisateur : Détection d’attribut|Table de charge de travail|
     |Utilisateur : Table/Vue/SP|Employees|
 1. Dans la page **Schema 3**, renseignez les zones avec les valeurs spécifiées dans le tableau qui suit l’image, puis sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Schema 3.](.\media\tutorial-ecma-sql-connector\conn-5.png)

     |Propriété|Description|
     |-----|-----|
     |Select Anchor for :User|User:ContosoLogin|
     |Sélectionner l’attribut DN pour Utilisateur|AzureID|
 1. Dans la page **Schema 4**, conservez les valeurs par défaut et sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Schema 4.](.\media\tutorial-ecma-sql-connector\conn-6.png)
 1. Dans la page **General**, renseignez les zones et sélectionnez **Next**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.
 
     ![Capture d’écran qui montre la page Global.](.\media\tutorial-ecma-sql-connector\conn-7.png)

     |Propriété|Description|
     |-----|-----|
     |Format date/heure de la source de données|aaaa-MM-jj HH:mm:ss|
 1. Dans la page **Partitions**, sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Partitions.](.\media\tutorial-ecma-sql-connector\conn-8.png)

 1. Dans la page **Run Profiles**, laissez la case **Export** cochée. Cochez la case **Full import** et sélectionnez **Next**.
 
     ![Capture d’écran montrant la page Run Profiles.](.\media\tutorial-ecma-sql-connector\conn-9.png)

 1. Dans la page **Export**, renseignez les zones et sélectionnez **Next**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner. 
 
     ![Capture d’écran montrant la page Export.](.\media\tutorial-ecma-sql-connector\conn-10.png)

     |Propriété|Description|
     |-----|-----|
     |Méthode d’opération|Table de charge de travail|
     |Table/Vue/SP|Employees|
 
 1. Dans la page **Full Import**, renseignez les zones et sélectionnez **Next**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner. 
 
     ![Capture d’écran montrant la page Full Import.](.\media\tutorial-ecma-sql-connector\conn-11.png)

     |Propriété|Description|
     |-----|-----|
     |Méthode d’opération|Table de charge de travail|
     |Table/Vue/SP|Employees|
 
 1. Dans la page **Object Types**, renseignez les zones et sélectionnez **Next**. Utilisez le tableau qui suit l’image pour obtenir des conseils concernant les différentes zones à renseigner.

      - **Anchor** : cet attribut doit être unique dans le système cible. Le service de provisionnement Azure AD interrogera l’hôte ECMA à l’aide de cet attribut après le cycle initial. Cette valeur d’ancre doit être la même que celle du schéma 3.
      - **Query Attribute** : utilisé par l’hôte ECMA pour interroger le cache en mémoire. Cet attribut doit être unique.
      - **DN** : l’option **Autogenerated** doit être sélectionnée dans la plupart des cas. Si elle ne l’est pas, vérifiez que l’attribut DN est mappé à un attribut dans Azure AD qui stocke le DN au format suivant : CN = anchorValue, Object = objectType.

        ![Capture d’écran montrant la page Object Types.](.\media\tutorial-ecma-sql-connector\conn-12.png)

      |Propriété|Description|
      |-----|-----|
      |Objet cible|Utilisateur|
      |Ancre|ContosoLogin|
      |Query Attribute|AzureID|
      |DN|AzureID|
      |Généré automatiquement|Activée|
      

 1. Dans la page **Select Attributes**, ajoutez tous les attributs dans la liste déroulante, puis sélectionnez **Next**. 
 
     ![Capture d’écran montrant la page Select Attributes.](.\media\tutorial-ecma-sql-connector\conn-13.png)

      La liste déroulante **Attribute** affiche tout attribut qui a été découvert dans le système cible et qui *n’a pas* été choisi dans la page **Select Attributes** précédente. 
 1. Dans la page **Deprovisioning**, sous **Disable flow**, sélectionnez **Delete**. Sélectionnez **Terminer**.
 
     ![Capture d’écran montrant la page Deprovisioning.](.\media\tutorial-ecma-sql-connector\conn-14.png)

## <a name="ensure-ecma2host-service-is-running"></a>Vérifier que le service ECMA2Host est en cours d’exécution
1. Sur le serveur sur lequel s’exécute l’hôte du connecteur ECMA Azure AD, sélectionnez **Démarrer**.
1. Entrez **run** et tapez **services.msc** dans la zone.
1. Dans la liste **Services**, vérifiez que **Microsoft ECMA2Host** est présent et en cours d’exécution. Si ce n’est pas le cas, sélectionnez **Démarrer**.

   ![Capture d’écran montrant que le service est en cours d’exécution.](.\media\on-premises-ecma-configure\configure-2.png)

## <a name="add-an-enterprise-application"></a>Ajouter une application d’entreprise
1. Connectez-vous au portail Azure en tant qu’administrateur d’application.
1. Dans le portail, accédez à **Azure Active Directory** > **Applications d’entreprise**.
1. Sélectionnez **Nouvelle application**.

   ![Capture d’écran qui monter l’ajout d’une nouvelle application.](.\media\on-premises-ecma-configure\configure-4.png)
1. Recherchez **On-premises ECMA app** dans la galerie, puis sélectionnez **Créer**.

## <a name="configure-the-application-and-test"></a>Configurer l’application et tester
1. Une fois l’application créée, sélectionnez la page **Provisioning**.
1. Sélectionnez **Prise en main**.

     ![Capture d’écran montrant Get started.](.\media\on-premises-ecma-configure\configure-1.png)
1. Dans la page **Provisioning**, définissez le mode sur **Automatic**.

     ![Capture d’écran qui montre la définition du mode Automatic.](.\media\on-premises-ecma-configure\configure-7.png)
1. Dans la section **On-Premises Connectivity**, sélectionnez l’agent que vous venez de déployer, puis **Assign Agent(s)** .
     >[!NOTE]
     >Après avoir ajouté l’agent, patientez 10 minutes que l’inscription se termine. Le test de connectivité ne fonctionne pas tant que l’inscription n’est pas terminée.
     >
     >Vous pouvez également forcer l’inscription de l’agent en redémarrant l’agent de provisionnement sur votre serveur. Accédez à votre serveur, recherchez **services** dans la barre de recherche Windows, identifiez **Azure AD Connect Provisioning Agent Service**, cliquez avec le bouton droit sur le service, puis redémarrez.
   
     ![Capture d’écran montrant le redémarrage d’un agent.](.\media\on-premises-ecma-configure\configure-8.png)
1.  Après 10 minutes, dans la section **Admin credentials**, entrez l’URL suivante. Remplacez la partie `connectorName` par le nom du connecteur sur l’hôte ECMA. Vous pouvez également remplacer `localhost` par le nom d’hôte.

     |Propriété|Valeur|
     |-----|-----|
     |URL de locataire|https://localhost:8585/ecma2host_connectorName/scim|

1. Entrez la valeur **Secret Token** que vous avez définie lors de la création du connecteur.
1. Sélectionnez **Test Connection** et patientez une minute.

     ![Capture d’écran montrant l’affectation d’un agent.](.\media\on-premises-ecma-configure\configure-5.png)
1. Une fois le test de connexion réussi, sélectionnez **Save**.</br>

     ![Capture d’écran montrant le test d’un agent.](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="assign-users-to-an-application"></a>Affecter des utilisateurs à une application
Maintenant que l’hôte du connecteur ECMA Azure AD est capable de communiquer avec Azure AD, vous pouvez passer à la configuration de l’étendue du provisionnement. 

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**.
1. Sélectionnez l’application **Provisionnement local**.
1. À gauche, sous **Gérer**, sélectionnez **Utilisateurs et groupes**.
1. Sélectionner **Ajouter un utilisateur/groupe**.

     ![Capture d’écran montrant l’ajout d’un utilisateur.](.\media\tutorial-ecma-sql-connector\app-2.png)
1. Sous **Utilisateurs**, sélectionnez **Aucun sélectionné**.

     ![Capture d’écran montrant Aucune sélection.](.\media\tutorial-ecma-sql-connector\app-3.png)
1. Sélectionnez des utilisateurs à droite, puis cliquez sur le bouton **Sélectionner**.</br>

     ![Capture d’écran montrant Sélectionner des utilisateurs.](.\media\tutorial-ecma-sql-connector\app-4.png)
1. Sélectionnez **Affecter**.

     ![Capture d’écran montrant Affecter des utilisateurs.](.\media\tutorial-ecma-sql-connector\app-5.png)

## <a name="configure-attribute-mappings"></a>Configuration des mappages d’attributs
Vous devez maintenant mapper des attributs entre l’application locale et votre serveur SQL.

#### <a name="configure-attribute-mapping"></a>Configurer le mappage d’attributs
 1. Dans le portail Azure AD, sous **Applications d’entreprise**, sélectionnez la page **Provisionnement**.
 1. Sélectionnez **Prise en main**.
 1. Développez **Mappages** et sélectionnez **Provisionner les utilisateurs Azure Active Directory**.
 
    ![Capture d’écran montrant le provisionnement d’un utilisateur.](.\media\on-premises-ecma-configure\configure-10.png)
 1. Sélectionnez **Ajouter un mappage**.
 
     ![Capture d’écran montrant Ajouter un mappage.](.\media\on-premises-ecma-configure\configure-11.png)
 1. Spécifiez les attributs source et cible, puis ajoutez tous les mappages indiqués dans le tableau suivant.

      |Type de mappage|Attribut source|Attribut cible|
      |-----|-----|-----|
      |Direct|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:ContosoLogin|
      |Direct|objectID|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:AzureID|
      |Direct|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|
      |Direct|givenName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:FirstName|
      |Direct|surName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:LastName|
      |Direct|mailNickName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:textID|

 1. Sélectionnez **Enregistrer**.
 
     ![Capture d’écran montrant l’enregistrement du mappage.](.\media\tutorial-ecma-sql-connector\app-6.png)

## <a name="test-provisioning"></a>Tester le provisionnement
Maintenant que vos attributs sont mappés, vous pouvez tester le provisionnement à la demande avec l’un de vos utilisateurs.
 
 1. Dans le portail Azure, sélectionnez **Applications d’entreprise**.
 1. Sélectionnez l’application **Provisionnement local**.
 1. Sur la gauche, sélectionnez **Provisionnement**.
 1. Sélectionnez **Approvisionner à la demande**.
 1. Recherchez l’un de vos utilisateurs de test, puis sélectionnez **Provisionner**.
 
     ![Capture d’écran montrant le test du provisionnement.](.\media\on-premises-ecma-configure\configure-13.png)

## <a name="start-provisioning-users"></a>Démarrer le provisionnement des utilisateurs
 1. Une fois le provisionnement à la demande réussi, revenez à la page de configuration du provisionnement. Vérifiez que l’étendue est définie sur les seuls utilisateurs et groupes affectés, **activez** le provisionnement, puis sélectionnez **Enregistrer**.
 
    ![Capture d’écran montrant Démarrer le provisionnement.](.\media\on-premises-ecma-configure\configure-14.png)
 1. Attendez que le provisionnement démarre. Cela peut prendre jusqu’à 40 minutes. Une fois le travail de provisionnement terminé, comme décrit dans la section suivante, vous pouvez modifier l’état du provisionnement en **Désactivé** et sélectionner **Enregistrer**. Cette action empêche que le service de provisionnement s’exécute à l’avenir.

## <a name="check-that-users-were-successfully-provisioned"></a>Vérifier que les utilisateurs ont été correctement provisionnés
Après avoir attendu, vérifiez la base de données SQL pour vous assurer que les utilisateurs sont en cours de provisionnement.

 ![Capture d’écran montrant la vérification du provisionnement des utilisateurs.](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="appendix-a"></a>Annexe A
Utilisez le script SQL suivant pour créer l’exemple de base de données.

```SQL
---Creating the Database---------
Create Database CONTOSO
Go
-------Using the Database-----------
Use [CONTOSO]
Go
-------------------------------------

/****** Object:  Table [dbo].[Employees]    Script Date: 1/6/2020 7:18:19 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[Employees](
    [ContosoLogin] [nvarchar](128) NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [Email] [nvarchar](128) NULL,
    [InternalGUID] [uniqueidentifier] NULL,
    [AzureID] [uniqueidentifier] NULL,
    [textID] [nvarchar](128) NULL
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Employees] ADD  CONSTRAINT [DF_Employees_InternalGUID]  DEFAULT (newid()) FOR [InternalGUID]
GO

```




## <a name="next-steps"></a>Étapes suivantes

- [Résoudre des problèmes liés au provisionnement d’application locale](on-premises-ecma-troubleshoot.md)
- [Passer en revue les limitations connues](known-issues.md)
- [Prérequis liés au provisionnement local](on-premises-ecma-prerequisites.md)
- [Passer en revue les prérequis pour le provisionnement local](on-premises-ecma-prerequisites.md)
