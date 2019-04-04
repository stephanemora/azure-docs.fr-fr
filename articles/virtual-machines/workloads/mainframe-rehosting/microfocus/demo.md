---
title: Configurer Micro Focus CICS BankDemo pour Micro Focus Enterprise Developer 4.0 sur des Machines virtuelles Azure
description: Exécutez l’application de Micro Focus BankDemo sur Machines virtuelles (VM) pour apprendre à utiliser Micro Focus Enterprise Server et code Enterprise Developer.
author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: be94cf0367f93f14249239fce5e09c8635a01136
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892481"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurer Micro Focus CICS BankDemo pour Micro Focus Enterprise Developer 4.0 sur Azure

Lorsque vous configurez Micro Focus Enterprise Server 4.0 et 4.0 de développeur d’entreprise sur Azure, vous pouvez tester les déploiements de charges de travail IBM z/OS. Cet article explique comment configurer CICS BankDemo, un exemple d’application qui est fourni avec le développeur en entreprise.

CICs signifie système pour le contrôle des informations de client, la plateforme de la transaction utilisée par la plupart des applications de macroordinateur en ligne. L’application BankDemo est idéale pour apprendre comment Enterprise Server et code Enterprise Developer fonctionnent et comment gérer et déployer une application réelle avec terminaux à écran vert.

## <a name="prerequisites"></a>Conditions préalables

- Une machine virtuelle avec [Enterprise Developer](set-up-micro-focus-azure.md). N’oubliez pas qu’une instance complète de serveur d’entreprise sur celui-ci pour à des fins de développement et de test Enterprise Developer. Il s’agit de l’instance de serveur d’entreprise utilisé pour la démonstration.

- [Édition SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express). Téléchargez et installez-le sur la machine virtuelle de Enterprise Developer. Serveur d’entreprise nécessite une base de données pour la gestion des régions CICS et l’application BankDemo utilise également une base de données SQL Server appelé BANKDEMO. Cette démonstration suppose que vous utilisez SQL Server Express pour les deux bases de données. Lors de l’installation, sélectionnez l’installation de base.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS est utilisé pour la gestion des bases de données et l’exécution d’un script T-SQL. Téléchargez et installez-le sur la machine virtuelle de Enterprise Developer.

- [Visual Studio 2017](https://azure.microsoft.com/downloads/) avec le dernier service pack ou [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), que vous pouvez télécharger gratuitement.

- Rumba bureau ou un autre émulateur 3270.

## <a name="configure-the-windows-environment"></a>Configurer l’environnement Windows

Après avoir installé Enterprise Developer 4.0 sur la machine virtuelle, vous devez configurer l’instance de serveur d’entreprise qui l’accompagne. Pour ce faire, vous devez installer quelques fonctionnalités supplémentaires de Windows comme suit.

1. Utilisez RDP pour vous connecter à la machine virtuelle 4.0 du serveur entreprise que vous avez créé.

2. Cliquez sur le **recherche** icône située à côté du **Démarrer** bouton et le type **les fonctionnalités de Windows**. Le Gestionnaire de serveur ajouter des rôles et fonctionnalités Assistant s’ouvre.

3. Sélectionnez **rôle serveur Web (IIS)**, puis vérifiez les éléments suivants :

    - Outils d’administration Web
    - IIS 6 Management Compatibility (sélectionnez toutes les fonctionnalités disponibles)
    - Console de gestion IIS
    - Gestion des Scripts et outils IIS
    - Service de gestion IIS

4. Sélectionnez **Services World Wide Web**et vérifiez les éléments suivants :

     Fonctionnalités de développement d’application :
    - Extensibilité .NET
    - ASP.NET
    - Fonctionnalités HTTP communes : Ajouter toutes les fonctionnalités disponibles
    - Intégrité et Diagnostics : Ajouter toutes les fonctionnalités disponibles
    - Sécurité :
        - Authentification de base
        - Authentification Windows

5. Sélectionnez **Windows Process Activation Service** et tous ses enfants.

6. Pour **fonctionnalités**, vérifiez **Microsoft .NET framework 3.5.1**et vérifiez les éléments suivants :

    - Activation de Windows Communication Foundation HTTP
    - Activation de Windows Communication Foundation Non-HTTP

7. Pour **fonctionnalités**, vérifiez **Microsoft .NET framework 4.6**et vérifiez les éléments suivants :

   - Activation de canaux nommés
   - Activation TCP
   - Partage de Port TCP

     ![Assistant Ajout de rôles et fonctionnalités : Services de rôle](media/01-demo-roles.png)

8. Lorsque vous avez sélectionné toutes les options, cliquez sur **suivant** à installer.

9. Une fois les fonctionnalités de Windows, accédez à **le panneau de configuration \> système et sécurité \> outils d’administration**, puis sélectionnez **Services**. Défiler vers le bas et vérifiez que les services suivants sont en cours d’exécution et la valeur **automatique**:

    - **NetTcpPortSharing**
    - **Adaptateur d’écouteur Net.Pipe**
    - **Adaptateur d’écouteur NET.TCP**

10. Pour configurer la prise en charge IIS et WAS, dans le menu localiser **invite de commandes développeur Micro Focus Enterprise (64 bits)** et qui s’exécutent en tant que **administrateur**.

11. Type **wassetup – i** et appuyez sur **entrée**.

12. Une fois l’exécution du script, vous pouvez fermer la fenêtre.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurer le compte système local pour SQL Server

Certains processus Enterprise Server doivent être en mesure de se connecter à SQL Server et créer des bases de données et d’autres objets. Ces processus utilisent le compte système local, vous devez donc donner sysadmin autorité à ce compte.

1. Lancer le **SSMS** et cliquez sur **Connect** pour se connecter au serveur SQLEXPRESS local à l’aide de l’authentification Windows. Il doit être disponible dans le **nom du serveur** liste.

2. Sur la gauche, développez le **sécurité** dossier et sélectionnez **connexions**.

3. Sélectionnez **autorité NT\\système** et sélectionnez **propriétés**.

4. Sélectionnez **rôles serveur** et vérifiez **sysadmin**.

     ![Fenêtre de l’Explorateur d’objets SSMS : Propriétés de connexion](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Créer la base de données BankDemo et tous ses objets

1. Ouvrez **Windows Explorer** et accédez à **C:\\utilisateurs\\Public\\Documents\\Micro Focus\\Enterprise Developer\\ Exemples\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL**.

2. Copiez le contenu du **BankDemoCreateAll.SQL** fichier dans le Presse-papiers.

3. Ouvrez **SSMS**. Sur la droite, cliquez sur **Server** et sélectionnez **nouvelle requête**.

4. Collez le contenu du Presse-papiers dans le **nouvelle requête** boîte.

5. Exécutez le code SQL en cliquant sur **Execute** à partir de la **commande** onglet au-dessus de la requête.

La requête doit s’exécuter sans erreurs. Lorsqu’il est terminé, vous disposez de la base de données pour l’application BankDemo.

![Sortie de SQLQuery1.sql](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Vérifiez que les tables de base de données et les objets ont été créés.

1. Cliquez sur le **BANKDEMO** de base de données et sélectionnez **Actualiser**.

2. Développez le **base de données** et sélectionnez **Tables**. Vous devez voir quelque chose comme ce qui suit.

     ![Table BANKDEMO développée dans l’Explorateur d’objets](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Générez l’application de développeur en entreprise

1. Ouvrez Visual Studio et connectez-vous.

2. Sous le **fichier** option de menu, sélectionnez **ouvrir un projet/Solution**, accédez à **C:\\utilisateurs\\Public\\Documents\\Micro Le focus\\Enterprise Developer\\exemples\\Mainframe\\CICS\\DotNet\\BankDemo**, puis sélectionnez le **sln**fichier.

3. Prendre un certain temps pour examiner les objets. Programmes COBOL sont affichées dans l’Explorateur de solutions avec l’extension CBL ainsi JCL CopyBooks (CPY).

4. Cliquez sur le **BankDemo2** projet, puis sélectionnez **définir comme projet de démarrage**.

    > [!NOTE]
    > Le projet BankDemo se sert de HCOSS (compatibilité Option hôte SQL Server), ce qui n’est pas utilisé pour cette démonstration.

5. Dans **l’Explorateur de solutions**, cliquez sur le **BankDemo2** projet, puis sélectionnez **Build**.

    > [!NOTE]
    > Construction au niveau de la solution entraîne des erreurs, comme HCOSS n’a pas été configuré.

6. Lorsque le projet est généré, examinez le **sortie** fenêtre. Il doit ressembler à l’image ci-dessous.

     ![Fenêtre de sortie affiche le build réussie](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Déployer l’application BankDemo dans la base de données de la région

1. Ouvrez une invite de commandes développeur en entreprise (64 bits) en tant qu’administrateur.

2. Accédez à la **PUBLIC %\\Documents\\Micro Focus\\Enterprise Developer\\exemples\\Mainframe\\CICS\\DotNet\\ BankDemo**.

3. À l’invite de commandes, exécutez **bankdemodbdeploy** et inclure le paramètre pour la base de données à déployer, par exemple :

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Veillez à utiliser une barre oblique (/) pas une barre oblique (\\). Ce script s’exécute pendant un certain temps.

![Administration : Fenêtre d’invite de commandes développeur entreprise](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Créer la région BankDemo dans administrateur d’entreprise pour .NET

1. Ouvrez le **Enterprise Server pour l’Administration de .NET** l’interface utilisateur.

2. Pour démarrer le composant logiciel enfichable MMC à partir de la Windows **Démarrer** menu, choisissez **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server pour l’administration .NET**. (Pour Windows Server, choisissez **Micro Focus Enterprise Developer \> Enterprise Server pour l’administration .NET**).

3. Développez le **régions** conteneur dans le volet gauche avec le bouton droit puis **CICS**.

4. Sélectionnez **définissent la région** pour créer une nouvelle région CICS appelée **BANKDEMO**, hébergé dans la base de données (local).

5. Fournissez l’instance de serveur de base de données, cliquez sur **suivant**, puis entrez le nom de la région **BANKDEMO**.

     ![Définir la boîte de dialogue de région](media/07-demo-cics.png)

6. Pour sélectionner le fichier de définition de région pour la base de données entre les régions, recherchez **région\_bankdemo\_db.config** dans **C:\\utilisateurs\\Public\\ Documents\\Micro Focus\\Enterprise Developer\\exemples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Définir la région - nom de la région : BANKDEMO](media/08-demo-cics.png)

7. Cliquez sur **Terminer**.

## <a name="create-xa-resource-definitions"></a>Créer des définitions de ressources XA

1. Dans le volet gauche de la **Enterprise Server pour l’Administration de .NET** l’interface utilisateur, développez **système**, puis **définitions de ressources XA**. Ce paramètre définit la façon dont la région interagit avec Enterprise Server et les bases de données d’application.

2. Avec le bouton droit sur **définitions de ressources XA** et sélectionnez **ajouter une Instance de serveur**.

3. Dans la zone de liste déroulante, sélectionnez **Instance de Service de base de données**. Il s’agit de l’ordinateur local SQLEXPRESS.

4. Sélectionnez l’instance sous le **définitions de ressources XA (machinename\\sqlexpress)** conteneur et cliquez sur **ajouter**.

5. Sélectionnez **définition de ressource de base de données XA** , puis tapez **BANKDEMO** pour le **nom** et **région**.

     ![Nouvel écran de définition de ressource de base de données XA](media/09-demo-xa.png)

6. Cliquez sur le bouton de sélection (**...** ) pour ouvrir l’Assistant de chaîne de connexion. Pour **nom du serveur**, type **(local)\\SQLEXPRESS**. Pour **ouverture de session**, sélectionnez **l’authentification Windows**. Pour le nom de la base de données, tapez **BANKDEMO**

     ![Modifier l’écran de la chaîne de connexion](media/10-demo-string.png)

7. Testez la connexion.

## <a name="start-the-bankdemo-region"></a>Démarrer la région BANKDEMO

> [!NOTE]
> La première étape est importante : Vous devez définir la région à utiliser la définition de ressource XA que vous venez de créer.

1. Accédez à la **BANDEMO CICS région** sous le **régions conteneur**, puis sélectionnez **modifier un fichier de démarrage de la région** à partir de la **Actions** volet. Faites défiler vers les propriétés de SQL et entrez **bankdemo** pour le **nom de la ressource XA** , ou utiliser les points de suspension pour le sélectionner.

2. Cliquez sur le **enregistrer** icône pour enregistrer vos modifications.

3. Avec le bouton droit **BANKDEMO CICS région** dans le **Console** volet et sélectionnez **Start/Stop région**.

4. Dans la partie inférieure de la **Start/Stop région** boîte qui apparaît dans le volet central, sélectionnez **Démarrer**. Après quelques secondes, la région démarre.

     ![Boîte de démarrage/arrêt de SQL](/media/11-demo-sql.png)

     ![Région CICS BANKDEMO - écran prise en main](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Créer un écouteur

Vous devez créer un écouteur pour les sessions de TN3270 qui accèdent à l’application BankDemo.

1. Dans le volet gauche, développez **éditeurs de Configuration** et sélectionnez **écouteur**.

2. Cliquez sur le **ouvrir un fichier** icône, sélectionnez le **seelistener.exe.config** fichier. Ce fichier sera modifié et est chargé chaque fois que Enterprise Server démarre.

3. Notez que les deux régions défini précédemment (ESDEMO et JCLDEMO).

4. Pour créer une nouvelle région pour BANKDEMO, avec le bouton droit **régions**, puis sélectionnez **ajouter une région**.

5. Sélectionnez **BANKDEMO région**.

6. Ajouter un canal de TN3270 en double-cliquant sur **BANKDEMO région** et en sélectionnant **Add Channel**.

7. Pour **nom**, entrez **TN3270**. Pour **Port**, entrez **9024**. (Notez que l’application ESDEMO utilise le port 9230 vous devez donc utiliser un autre port.)

8. Pour enregistrer le fichier, cliquez sur le **enregistrer** icône ou choisissez **fichier** \> **enregistrer**.

9. Pour démarrer l’écouteur, cliquez sur le **écouteur Démarrer** icône ou choisissez **Options** \> **écouteur Démarrer**.

     ![Fenêtres de l’éditeur de Configuration d’écouteur](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurer Rumba pour accéder à l’application BankDemo

La dernière chose que vous devez faire est de configurer une session 3270 à l’aide de Rumba, un émulateur 3270. Cette étape permet d’accéder à l’application BankDemo via l’écouteur que vous venez de créer.

1. À partir de la Windows **Démarrer** menu, lancez Rumba Desktop.

2. Sous le **connexions** élément de menu, sélectionnez **TN3270**.

3. Cliquez sur **insérer** et type **127.0.0.1** pour l’adresse IP et **9024** pour le port défini par l’utilisateur.

4. En bas de la boîte de dialogue, cliquez sur **Connect**. Un écran CICS noir s’affiche.

5. Type **bank** pour afficher l’écran 3270 initiale pour l’application BankDemo.

6. Pour l’ID d’utilisateur, tapez **B0001** et le mot de passe, tapez quoi que ce soit. Le premier écran BANK20 s’ouvre.

![Écran d’accueil d’Affichage mainframe](media/14-demo.png)
![écran d’affichage de macroordinateur - Rumba - sous-système démonstration](media/15-demo.png)

Félicitations ! Vous exécutez à présent une application CICS dans Azure à l’aide de Micro Focus Enterprise Server.

## <a name="next-steps"></a>Étapes suivantes

- [Exécuter le serveur d’entreprise dans des conteneurs Docker sur Azure](run-enterprise-server-container.md)
- [Migration mainframe - portail](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Résolution de problèmes](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Démystifier la migration d’une infrastructure mainframe vers Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
