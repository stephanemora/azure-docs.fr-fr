---
title: Sécuriser une base de données unique dans Azure SQL Database | Microsoft Docs
description: Découvrez les techniques et fonctionnalités à utiliser pour sécuriser une base de données unique dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 814d558efee4a72a25d956828e0db237424cab24
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409766"
---
# <a name="tutorial-secure-a-single-database-in-azure-sql-database"></a>Tutoriel : Sécuriser une base de données unique dans Azure SQL Database

SQL Database sécurise les données dans une base de données SQL Azure unique en :

- Limitant l’accès à votre base de données au moyen de règles de pare-feu
- Utilisant des mécanismes d’authentification qui nécessitent leur identité
- Autorisation des données au moyen d’appartenances basées sur le rôle et d’autorisations,
- Sécurité au niveau des lignes
- Masquage des données dynamiques

SQL Database dispose également de fonctionnalités avancées de surveillance, d’audit et de détection des menaces.

> [!IMPORTANT]
> Azure SQL Database sécurise une base de données dans une instance gérée à l'aide de règles de sécurité réseau et de points de terminaison privés. Pour plus d'informations, voir [Azure SQL Database Managed Instance](sql-database-managed-instance-index.yml) et [Architecture de la connectivité d'Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).

Quelques étapes simples suffisent pour améliorer la protection d’une base de données contre les utilisateurs malveillants ou tout accès non autorisé. Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Configurer des règles de pare-feu pour votre serveur dans le portail Azure
> - Créer des règles de pare-feu pour votre base de données à l’aide de SSMS
> - Se connecter à la base de données à l’aide d’une chaîne de connexion sécurisée
> - Configurer un administrateur Azure Active Directory pour Azure SQL
> - Gérer l’accès des utilisateurs
> - Protéger vos données à l’aide du chiffrement
> - Activer l’audit Azure SQL Database
> - Activer la détection de menaces pour les bases de données SQL

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vérifiez que vous disposez des éléments suivants :

- La dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).
- Microsoft Excel.
- Un serveur Azure SQL Server et une base de données. Consultez [Création d’une base de données SQL Azure à l’aide du portail Azure](sql-database-get-started-portal.md), [Créer une base de données SQL Azure à l’aide de l’interface de ligne de commande Azure](sql-database-cli-samples.md) et [Créer une base de données SQL Azure unique à l’aide de PowerShell](sql-database-powershell-samples.md).

> [!NOTE]
> Ce tutoriel suppose que vous avez déjà configuré Azure Active Directory ou que vous utilisez le domaine géré Azure Active Directory initial. Pour plus d'informations sur la configuration d'Azure Active Directory pour une variété de scénarios, consultez [Intégration des identités locales avec Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Ajout de votre propre nom de domaine à Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administration de votre annuaire Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Gestion d'Azure AD à l'aide de Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) et [Ports et protocoles nécessaires à l'identité hybride](../active-directory/hybrid/reference-connect-ports.md).

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

Les bases de données SQL sont protégées par un pare-feu dans Azure. Par défaut, toutes les connexions au serveur et aux bases de données du serveur sont rejetées, sauf celles provenant d’autres services Azure. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](sql-database-firewall-configure.md).

La configuration la plus sécurisée consiste à définir le paramètre « Autoriser l’accès aux services Azure » sur DÉSACTIVER. Si vous avez besoin de vous connecter à la base de données à partir d’une machine virtuelle Azure ou d’un service cloud, vous devez créer une [adresse IP réservée (déploiement classique)](../virtual-network/virtual-networks-reserved-public-ip.md) et autoriser uniquement cette adresse IP réservée à traverser le pare-feu. Si vous utilisez le modèle de déploiement [Gestionnaire des ressources](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm), une adresse IP publique dédiée est affectée à la ressource, et vous devez autoriser cette adresse IP via le pare-feu.

Suivez la procédure suivante pour créer une [règle de pare-feu au niveau du serveur SQL Database](sql-database-firewall-configure.md) pour votre serveur afin d’autoriser les connexions depuis une adresse IP spécifique.

> [!NOTE]
> Si vous avez créé une base de données exemple dans Azure à l’aide d’un des didacticiels ou des démarrages rapides précédents et que vous suivez ce didacticiel sur un ordinateur ayant la même adresse IP que lorsque vous avez effectué ces didacticiels, vous pouvez ignorer cette étape car vous avez déjà créé une règle de pare-feu au niveau du serveur.

1. Cliquez sur **Bases de données SQL** dans le menu de gauche et cliquez sur la base de données pour laquelle vous souhaitez configurer la règle de pare-feu sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver-20170313.database.windows.net**) et fournit des options pour poursuivre la configuration.

      ![règle de pare-feu de serveur](./media/sql-database-security-tutorial/server-firewall-rule.png)

2. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre.

3. Cliquez sur **Ajouter l’adresse IP du client** dans la barre d’outils pour ajouter l’adresse IP publique de l’ordinateur avec lequel vous vous êtes connecté au portail ou saisissez manuellement la règle de pare-feu, puis cliquez sur **Enregistrer**.

      ![définir la règle de pare-feu de serveur](./media/sql-database-security-tutorial/server-firewall-rule-set.png)

4. Cliquez sur **OK**, puis cliquez sur la **X** pour fermer la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter à n’importe quelle base de données du serveur avec l’adresse IP ou la plage d’adresses IP spécifiée.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Créer une règle de pare-feu au niveau de la base de données à l’aide de SSMS

Les règles de pare-feu de niveau base de données vous permettent de créer différents paramètres de pare-feu pour différentes bases de données sur le même serveur logique et de créer des règles de pare-feu portables, ce qui signifie qu'elles suivent la base de données pendant un basculement au lieu d'être stockées sur le serveur SQL. Les règles de pare-feu au niveau de la base de données peuvent seulement être configurées en utilisant des instructions Transact-SQL et uniquement après avoir configuré la première règle de pare-feu au niveau du serveur. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](sql-database-firewall-configure.md).

Procédez comme suit pour créer une règle de pare-feu spécifique à la base de données.

1. Connectez-vous par exemple à votre base de données à l’aide de [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur la base de données à laquelle vous souhaitez ajouter une règle de pare-feu, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à votre base de données s’ouvre.

3. Dans la fenêtre de requête, remplacez l’adresse IP par votre adresse IP publique, puis exécutez la requête suivante :

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Dans la barre d’outils, cliquez sur **Exécuter** pour créer la règle de pare-feu.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Connecter une application à votre base de données à l’aide d’une chaîne de connexion sécurisée

Pour garantir une connexion sécurisée et chiffrée entre une application cliente et SQL Database, la chaîne de connexion doit être configurée pour :

- demander une connexion chiffrée ;
- ne pas faire confiance au certificat du serveur.

Cela établit une connexion à l’aide du protocole TLS (Transport Layer Security) et réduit le risque d’attaques de l’intercepteur. Vous pouvez obtenir des chaînes de connexion correctement configurées pour votre base de données SQL pour les pilotes clients pris en charge à partir du portail Azure, comme indiqué dans cette capture d’écran pour ADO.net. Pour plus d’informations sur le protocole TLS et la connectivité, consultez [Considérations relatives au protocole TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**.

2. Dans la page **Vue d’ensemble** de votre base de données, cliquez sur **Afficher les chaînes de connexion de la base de données**.

3. Passez en revue la chaîne de connexion **ADO.NET** complète.

    ![Chaîne de connexion ADO.NET](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Approvisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database

Approvisionnez un administrateur Azure Active Directory pour votre serveur SQL Azure sur le portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), dans le coin supérieur droit, sélectionnez votre connexion pour développer une liste déroulante de répertoires Active Directories potentiels. Choisissez l’annuaire Active Directory approprié en tant qu’Azure AD par défaut. Cette étape lie l’association de l’abonnement avec Active Directory et le serveur SQL Azure, ce qui garantit que le même abonnement est utilisé à la fois pour Azure AD et pour SQL Server. (Le serveur SQL Azure peut héberger la base de données SQL Azure ou l’entrepôt Azure SQL Data Warehouse.)

    ![choose-ad](./media/sql-database-aad-authentication/8choose-ad.png)

2. Sur la page **SQL Server**, sélectionnez **Administrateur Active Directory**, puis sur la page **Administrateur Active Directory**, sélectionnez **Définir l'administrateur**. ![Sélectionnez le répertoire actif](./media/sql-database-aad-authentication/select-active-directory.png).  

3. À la page **Ajouter un administrateur**, recherchez un utilisateur, sélectionnez l’utilisateur ou le groupe à définir en tant qu’administrateur, puis choisissez **Sélectionner**. (La page Administrateur Active Directory affiche tous les membres et groupes présents dans Active Directory. Les utilisateurs ou les groupes grisés ne peuvent être sélectionnés, car ils ne sont pas pris en charge en tant qu’administrateurs Azure AD. (Consultez la liste des administrateurs de prise en charge dans la section **Fonctionnalités et limitations d’Azure AD** de [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database ou de SQL Data Warehouse](sql-database-aad-authentication.md).) Le contrôle d'accès basé sur les rôles (RBAC) s'applique uniquement au portail et n'est pas propagé vers SQL Server.
    ![sélectionner l’administrateur](./media/sql-database-aad-authentication/select-admin.png)  

4. En haut de la page **Administrateur Active Directory**, sélectionnez **ENREGISTRER**.
    ![enregistrer l’administrateur](./media/sql-database-aad-authentication/save-admin.png)

La procédure de changement de l’administrateur peut prendre plusieurs minutes. Le nouvel administrateur apparaîtra dans la zone **Administrateur Active Directory** .

   > [!NOTE]
   > Lors de la configuration de l’administrateur Azure AD, le nom du nouvel administrateur (utilisateur ou groupe) ne peut pas déjà être présent dans la base de données MASTER virtuelle en tant qu’utilisateur de l’authentification SQL Server. Si tel est le cas, la configuration de l’administrateur d’Azure AD échoue, annulant sa création et indiquant que cet administrateur (ce nom) existe déjà. Dans la mesure où un utilisateur de l’authentification SQL Server ne fait pas partie d’Azure AD, tout effort pour se connecter au serveur à l’aide de l’authentification Azure AD échoue.

## <a name="creating-database-users"></a>Création d’utilisateurs de base de données

Avant de créer des utilisateurs, vous devez d’abord choisir l’un des deux types d’authentification pris en charge par Azure SQL Database :

**L’authentification SQL**, qui utilise le nom d’utilisateur et le mot de passe pour les connexions et les utilisateurs qui sont valides uniquement dans le contexte d’une base de données spécifique au sein d’un serveur logique.

**L’authentification Azure Active Directory**, qui utilise des identités gérées par Azure Active Directory.

### <a name="create-a-user-using-sql-authentication"></a>Créer un utilisateur à l'aide de l'authentification SQL

Procédez comme suit pour créer un utilisateur à l'aide de l'authentification SQL :

1. Connectez-vous à votre base de données, par exemple via [SQL Server Management Studio](./sql-database-connect-query-ssms.md) à l’aide de vos informations d’identification d’administrateur de serveur.

2. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur la base de données à laquelle vous souhaitez ajouter un nouvel utilisateur, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à la base de données sélectionnée s’ouvre.

3. Dans la fenêtre de requête, entrez la requête suivante :

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Dans la barre d’outils, cliquez sur **Exécuter** pour créer l’utilisateur.

5. Par défaut, l’utilisateur peut se connecter à la base de données, mais ne peut ni lire ni écrire des données. Pour accorder ces autorisations au nouvel utilisateur, exécutez les deux commandes suivantes dans une nouvelle fenêtre de requête.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Nous vous recommandons de créer ces comptes non administrateurs au niveau de la base de données pour la connexion à votre base de données, sauf si vous avez besoin d’exécuter des tâches d’administrateur telles que la création de nouveaux utilisateurs. Consultez le [didacticiel Azure Active Directory](./sql-database-aad-authentication-configure.md) sur l’authentification via Azure Active Directory.

### <a name="create-a-user-using-azure-active-directory-authentication"></a>Créer un utilisateur à l'aide de l'authentification Azure Active Directory

L’authentification Azure Active Directory nécessite que les utilisateurs de base de données soient créés en tant qu’utilisateurs de base de données autonome. Un utilisateur de base de données autonome sur une identité Azure AD est un utilisateur de base de données qui ne dispose pas de connexion dans la base de données MASTER, et qui est mappé à une identité située dans l’annuaire Azure AD associé à la base de données. L’identité Azure AD peut être un compte d’utilisateur individuel ou un groupe. Pour plus d’informations sur les utilisateurs de base de données autonome, consultez [Utilisateurs de base de données - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Il n’est pas possible de créer des utilisateurs de base de données (à l’exception d’administrateurs) via le portail Azure. Les rôles RBAC ne sont pas propagés à SQL Server, Base de données SQL ou SQL Data Warehouse. Les rôles RBAC Azure sont utilisés pour la gestion des ressources Azure et ne s’appliquent pas aux autorisations de base de données. Par exemple, le rôle **Contributeur de SQL Server** ne permet pas de se connecter à Base de données SQL ou à SQL Data Warehouse. L’accès doit être accordé directement dans la base de données à l’aide d’instructions Transact-SQL.
> [!WARNING]
> Les caractères spéciaux tels que les deux-points `:` ou l'esperluette `&` ne sont pas pris en charge lorsqu'ils sont inclus en tant que noms d'utilisateur dans les instructions CREATE LOGIN et CREATE USER de T-SQL.

1. Connectez-vous à votre instance d'Azure SQL Server à l'aide d'un compte Azure Active Directory doté au minimum de l'autorisation **MODIFIER UN UTILISATEUR**.
2. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur la base de données à laquelle vous souhaitez ajouter un nouvel utilisateur, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à la base de données sélectionnée s’ouvre.

3. Dans la fenêtre de requête, entrez la requête suivante et remplacez `<Azure_AD_principal_name>` par le nom d'utilisateur principal souhaité d'un utilisateur Azure AD ou par le nom d'affichage d'un groupe Azure AD :

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

   > [!NOTE]
   > Les utilisateurs AD Azure sont marqués dans les métadonnées de la base de données avec le type E (EXTERNAL_USER) et pour les groupes avec le type X (EXTERNAL_GROUPS). Pour plus d’informations, consultez [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="protect-your-data-with-encryption"></a>Protéger vos données à l’aide du chiffrement

La technologie de chiffrement transparent des données (TDE) d’Azure SQL Database chiffre automatiquement vos données au repos, sans aucune modification au niveau de l’application qui accède à la base de données chiffrée. Pour les bases de données créées, TDE est activé par défaut. Pour activer TDE sur votre base de données ou pour vérifier que TDE est activé, procédez comme suit :

1. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**.

2. Cliquez sur **Chiffrement transparent des données** pour ouvrir la page de configuration pour TDE.

    ![Chiffrement transparent des données](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Si nécessaire, réglez le paramètre **Chiffrement des données** sur Activé et cliquez sur **Enregistrer**.

Le processus de chiffrement démarre en arrière-plan. Vous pouvez afficher la progression en vous connectant à SQL Database en utilisant [SQL Server Management Studio](./sql-database-connect-query-ssms.md) et en interrogeant la colonne [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). L’état 3 indique que la base de données est chiffrée.

## <a name="enable-sql-database-auditing-if-necessary"></a>Activer l’audit Azure SQL Database, si nécessaire

L’audit Azure SQL Database suit les événements de base de données et les écrit dans un journal d’audit dans votre compte Stockage Azure. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des violations potentielles de la sécurité. Procédez comme suit pour créer une stratégie d’audit par défaut pour votre base de données SQL :

1. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**.

2. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**. Notez que l’audit au niveau du serveur est désactivé et que le lien **Afficher les paramètres du serveur** vous permet d’afficher ou de modifier les paramètres d’audit du serveur à partir de ce contexte.

    ![Panneau Audit](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Si vous préférez activer un type d’audit (ou un emplacement) différent de celui spécifié au niveau du serveur, **activez** l’audit et choisissez le type d’audit **Objet blob**. Si l’audit d’objets Blob du serveur est activé, l’audit configuré pour la base de données coexiste avec celui-ci.

    ![Activer l’audit](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Sélectionnez **Détails du stockage** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure où les journaux sont enregistrés et la période de rétention après laquelle les anciens journaux sont supprimés, puis cliquez sur **OK** dans le bas.

   > [!TIP]
   > Utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport d’audit.

5. Cliquez sur **Enregistrer**.

> [!IMPORTANT]
> Si vous voulez personnaliser les événements audités, vous pouvez le faire via PowerShell ou l’API REST. Pour plus d’informations, consultez [Audit de bases de données SQL](sql-database-auditing.md).
>

## <a name="enable-sql-database-threat-detection"></a>Activer la détection de menaces pour les bases de données SQL

Threat Detection fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales. Les utilisateurs peuvent analyser les événements suspects à l’aide de la fonction d’audit de base de données SQL pour déterminer s’ils sont le résultat d’une tentative d’accès, d’une violation ou d’une exploitation des données dans la base de données. Threat Detection vous permet de réagir facilement aux menaces potentielles à la base de données sans avoir à acquérir une expertise de la sécurité ou à gérer des systèmes de surveillance de la sécurité avancés.
Par exemple, il détecte certaines activités de base de données anormales indiquant des tentatives d’injection SQL potentielles. L’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

1. Accédez au volet de configuration de la base de données SQL que voulez surveiller. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.

    ![Volet de navigation](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. Dans le panneau de configuration **Audit et détection des menaces**, **activez** l’audit pour afficher les paramètres Détection des menaces.

3. **Activez** la détection des menaces.

4. Configurez la liste des adresses électroniques qui recevront les alertes de sécurité en cas de détection d’activités anormales sur la base de données.

5. Cliquez sur **Enregistrer** dans le panneau **Audit et détection des menaces** pour enregistrer la stratégie d’audit et de détection des menaces que vous avez créée ou modifiée.

    ![Volet de navigation](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Si des activités de base de données anormales sont détectées, vous recevrez une notification par courrier électronique.  Le courrier électronique contiendra des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées afin d’examiner et atténuer la menace potentielle pesant sur la base de données. Voici la procédure à suivre si vous recevez ce type de courrier électronique :

    ![Courrier électronique de détection de menaces](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. Dans le courrier électronique, cliquez sur le lien **Azure SQL Auditing Log** pour ouvrir le portail Azure et afficher les enregistrements d’audit pertinents au moment de l’événement suspect.

    ![Enregistrements d’audit](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Cliquez sur les enregistrements d’audit pour afficher plus d’informations sur les activités de base de données suspectes, comme l’instruction SQL, la cause de l’échec et l’adresse IP client.

    ![Détails des enregistrements](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. Dans le panneau des enregistrements d’audit, cliquez sur **Ouvrir dans Excel** pour ouvrir un modèle Excel préconfiguré à importer et exécuter une analyse plus approfondie du journal d’audit au moment de l’événement suspect.

    > [!NOTE]
    > Dans Excel 2010 ou version ultérieure, les paramètres Power Query et **Combinaison rapide** sont requis.

    ![Ouvrir les enregistrements dans Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Pour configurer le paramètre **Combinaison rapide** : sous l’onglet du ruban **POWER QUERY**, sélectionnez **Options** pour afficher la boîte de dialogue correspondante. Sélectionnez la section Confidentialité et choisissez la deuxième option « Ignore the Privacy Levels and potentially improve performance » :

    ![Combinaison rapide dans Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Pour charger les journaux d’audit SQL, vérifiez que les paramètres de l’onglet Paramètres sont correctement définis, puis sélectionnez le ruban « Données » et cliquez sur le bouton « Actualiser tout ».

    ![Paramètres Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Les résultats s’affichent dans la feuille **SQL Audit Logs** , qui vous permet d’analyser de manière plus approfondie les activités anormales détectées et de limiter l’impact de l’événement de sécurité sur votre application.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris en quelques étapes simples à améliorer la protection de votre base de données contre les utilisateurs malveillants ou tout accès non autorisé.  Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> - Définir des règles de pare-feu pour votre serveur et ou base de données
> - Se connecter à la base de données à l’aide d’une chaîne de connexion sécurisée
> - Configurer un administrateur Azure Active Directory pour Azure SQL
> - Gérer l’accès des utilisateurs
> - Protéger vos données à l’aide du chiffrement
> - Activer l’audit Azure SQL Database
> - Activer la détection de menaces pour les bases de données SQL

Passez au didacticiel suivant pour apprendre à implémenter une base de données géodistribuée.

> [!div class="nextstepaction"]
>[Mettre en œuvre une base de données réparties géographiquement](sql-database-implement-geo-distributed-database.md)
