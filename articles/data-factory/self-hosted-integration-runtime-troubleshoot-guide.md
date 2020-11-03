---
title: Résoudre les problèmes du runtime d’intégration auto-hébergé dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés au runtime d’intégration auto-hébergé dans Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/26/2020
ms.author: lle
ms.openlocfilehash: 3598db409e5493737753a8a1b03de168af5c664b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637188"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Résoudre les problèmes liés au runtime d’intégration auto-hébergé

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés au runtime d'intégration auto-hébergé dans Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Collecter les journaux du runtime d’IR auto-hébergé auprès d’Azure Data Factory

Azure Data Factory prend en charge l’affichage et le chargement des journaux d’erreurs pour les activités qui n’ont pas été exécutées sur l’IR auto-hébergé ou partagé. Vous pouvez suivre les étapes ci-dessous pour obtenir l’ID du rapport d’erreur, puis saisir l’ID du rapport pour localiser les problèmes connexes connus.

1. Accédez à la page **Exécutions d’activités**.

1. Sous la colonne **ERREUR** , cliquez sur le bouton ci-dessous.

    ![Page Exécutions d’activités](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Vous verrez les journaux relatifs à l’exécution de l’activité qui a échoué. Cliquez sur le bouton **Envoyer les journaux** pour obtenir de l’aide.

    ![Envoyer les journaux](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Vous pouvez choisir les journaux que vous souhaitez envoyer. Pour *IR auto-hébergé* , vous pouvez charger les journaux relatifs à l’activité ayant échoué ou tous les journaux sur le nœud de l’IR auto-hébergé. Pour *IR partagé* , vous pouvez uniquement charger les journaux relatifs à l’activité ayant échoué.

    ![Choisir les journaux](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Lorsque les journaux sont chargés, conservez une trace de l’ID du rapport au cas où vous auriez besoin d’une aide supplémentaire pour résoudre le problème.

    ![Charger les journaux](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Les requêtes de consultation et de chargement des journaux seront exécutées sur toutes les instances de l’IR auto-hébergé en ligne. Assurez-vous que toutes les instances de l’IR auto-hébergé sont en ligne en cas de journal manquant. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Erreur générale ou échec général de l’IR auto-hébergé

### <a name="tlsssl-certificate-issue"></a>Problème certificat TLS/SSL

#### <a name="symptoms"></a>Symptômes

Lorsque vous tentez d’activer le certificat TLS/SSL (avancé) à partir de **Configuration Manager IR auto-hébergé** -> **Accès à distance à partir de l’intranet** , après avoir sélectionné le certificat TLS/SSL, l’erreur ci-dessous s’affiche :

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

Dans le cas ci-dessus, l’utilisateur utilise le certificat avec « microsoft.com » comme dernier élément.

#### <a name="cause"></a>Cause

Il s'agit d'un problème connu dans WCF. La validation TLS/SSL WCF vérifie uniquement les derniers DNSName dans le réseau SAN. 

#### <a name="resolution"></a>Résolution

Le certificat générique est pris en charge dans le runtime d’intégration Azure Data Factory v2 IR auto-hébergé. Ce problème se produit généralement parce que le certificat SSL n’est pas correct. Le dernier DNSName dans le SAN doit être valide. Suivez les étapes ci-dessous pour le vérifier. 
1.  Ouvrez la console de gestion, vérifiez à la fois le *Sujet* et le *Nom alternatif du sujet* dans les Détails du Certificat. Dans le cas ci-dessus, par exemple, le dernier élément de *Autre nom de l’objet* , qui est « DNS Name = microsoft.com.com », n’est pas légitime.
2.  Contactez l’entreprise émettrice du certificat pour supprimer le nom DNS incorrect.

### <a name="concurrent-jobs-limit-issue"></a>Problème lié à la limite de tâches simultanées

#### <a name="symptoms"></a>Symptômes

Lorsque vous essayez d’augmenter la limite des tâches simultanées à partir de l’interface utilisateur Azure Data Factory, elle reste bloquée au stade *mise à jour*.
La valeur maximale des tâches simultanées a été définie sur 24 et vous souhaitez augmenter le nombre afin que les tâches puissent s’exécuter plus rapidement. La valeur minimale que vous puissiez entrer est 3 et la valeur maximale que vous puissiez entrer est 32. Vous avez augmenté la valeur de 24 à 32 et cliqué sur le bouton *Mettre à jour* , dans l’interface utilisateur elle reste bloquée sur *Mise à jour* comme vous pouvez le voir ci-dessous. Après rafraîchissement, la valeur était toujours de 24 et n'a jamais été actualisée à 32.

![Mise à jour de l'état](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Cause

Il y a une limitation pour le réglage car la valeur dépend des cœurs logiques et de la mémoire de l'ordinateur, vous pouvez simplement l'ajuster à une valeur plus petite telle que 24 et voir le résultat.

> [!TIP] 
> - Pour plus d’informations sur le nombre de cœurs logiques et sur la façon de trouver le nombre de cœurs logiques de l’ordinateur, consultez [cet article](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Pour plus d’informations sur le calcul du math. log, consultez [cet article](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problème de certificat SSL à haute disponibilité IR auto-hébergé

#### <a name="symptoms"></a>Symptômes

Le nœud de travail IR auto-hébergé a signalé l’erreur ci-dessous :

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Cause

Lorsque nous traitons des cas liés à la liaison SSL/TLS, nous pouvons rencontrer certains problèmes liés à la vérification de la chaîne de certificats. 

#### <a name="resolution"></a>Résolution

- Voici un moyen rapide et intuitif de résoudre les échecs de génération de chaîne de certificats X. 509.
 
    1. Exportez le certificat, qui doit être vérifié. Accédez à Gérer le certificat de l’ordinateur et recherchez le certificat que vous souhaitez vérifier, puis cliquez avec le bouton droit sur **Toutes les tâches** -> **Exporter**.
    
        ![Exporter des tâches](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copiez le certificat exporté sur la machine cliente. 
    3. Côté client, exécutez la commande ci-dessous dans CMD. Assurez-vous que vous avez bien remplacé *\<certificate path>* et les espaces réservés *\<output txt file path>* ci-dessous par des chemins connexes.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Par exemple :

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Vérifiez s’il existe une erreur dans le fichier txt de sortie. Le résumé des erreurs se trouve à la fin du fichier txt.

        Par exemple : 

        ![Résumé des erreurs](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Si aucune erreur ne s’affiche à la fin du fichier journal, comme indiqué ci-dessous, vous pouvez considérer que la chaîne de certificats est créée correctement dans la machine cliente.
        
        ![Aucune erreur dans le fichier journal](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- En présence d’AIA, CDP et OCSP sont configurés dans le fichier de certificat. Nous pouvons le vérifier de manière plus intuitive.
 
    1. Vous pouvez obtenir ces informations en vérifiant les détails d’un certificat.
    
        ![Détails du certificat](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Exécutez la commande ci-dessous : Assurez-vous que vous avez bien remplacé les espaces réservés *\<certificate path>* ci-dessous par le chemin du certificat.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. **L’outil de récupération d’URL** s’ouvre alors. Vous pouvez vérifier des certificats depuis AIA, CDP et OCSP en cliquant sur le bouton **Récupérer**.

        ![Bouton de récupération](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        La chaîne de certificats peut être créée correctement si le certificat d’AIA est « vérifié » et que le certificat de CDP ou OCSP est « vérifié ».

        Si vous constatez un échec lors de la récupération d’AIA, CDP, contactez l’équipe réseau pour préparer la machine cliente à se connecter à l’URL cible. Cela sera suffisant si le chemin d’accès http ou le chemin d’accès ldap peut être vérifié.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>L’IR auto-hébergé n’a pas pu télécharger le fichier ou l’assembly

#### <a name="symptoms"></a>Symptômes

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Par exemple : 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Cause

Si vous prenez Process Monitor, vous pouvez voir le résultat suivant :

[![Process Monitor](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Vous pouvez définir le filtre comme indiqué dans la capture d’écran ci-dessous.
> Il indique que la dll **System.ValueTuple** ne se trouve pas dans le dossier associé GAC ou dans *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* , ou dans le dossier *C:\Program Files\Microsoft Integration Runtime\4.0\Shared*.
> En fait, cela chargera d’abord la dll à partir du dossier *GAC* , puis du dossier *Partagé* et enfin du dossier de la *Passerelle*. Par conséquent, vous pouvez placer la dll dans n’importe quel chemin d’accès, ce qui peut être utile.

![Configurer des filtres](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Résolution

Il peut arriver que **System. ValueTuple. dll** se trouve dans le dossier *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan*. Copiez **System. ValueTuple. dll** dans le dossier *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* pour résoudre le problème.

Vous pouvez utiliser la même méthode pour résoudre d’autres problèmes de fichier ou d’assembly manquant.

#### <a name="more-information"></a>Informations complémentaires

La raison pour laquelle vous voyez le fichier System. ValueTuple. dll sous *%windir%\Microsoft.NET\assembly* et *%windir%\assembly* est qu’il s’agit d’un comportement .NET. 

À partir de l’erreur ci-dessous, vous pouvez voir clairement que l’assembly *System. ValueTuple* n’est pas là. Ce problème se produit lorsque l’application tente de vérifier l’assembly *System. ValueTuple. dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Pour plus d'informations sur le GAC, consultez [cet article](/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Procédure d’audit de la clé IR auto-hébergée manquante

#### <a name="symptoms"></a>Symptômes

Le runtime d’intégration auto-hébergé passe soudainement à hors connexion sans clé, le message d’erreur ci-dessous s’affiche dans le Journal des événements : `Authentication Key is not assigned yet`

![Clé d’authentification manquante](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Cause

- Le nœud IR auto-hébergé ou le runtime d’intégration logique auto-hébergé dans le portail est supprimé.
- Une désinstallation correcte est effectuée.

#### <a name="resolution"></a>Résolution

Si aucune des causes ci-dessus ne s’applique, vous pouvez accéder au dossier : *%programdata%\Microsoft\Data Transfer\DataManagementGateway* et vérifier si le fichier nommé **Configurations** est bien supprimé. S’il est supprimé, suivez les instructions [ici](https://www.netwrix.com/how_to_detect_who_deleted_file.html) pour auditer qui supprime le fichier.

![Vérifier le fichier de configuration](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Impossible d’utiliser le runtime d’IR auto-hébergé pour relier deux magasins de données locaux

#### <a name="symptoms"></a>Symptômes

Après avoir créé l’IR auto-hébergé pour les magasins de données source et de destination, vous souhaitez connecter les deux IR ensemble pour terminer une copie. Si les banques de données sont configurées dans différents réseaux virtuels ou si elles ne peuvent pas comprendre le mécanisme de passerelle, vous allez atteindre des erreurs telles que : *le pilote de source est introuvable dans l’IR de destination*  ; *la source n’est pas accessible par l’IR de destination*.
 
#### <a name="cause"></a>Cause

Le runtime d’IR auto-hébergé est conçu comme un nœud central d’une activité de copie, et non un agent client qui doit être installé pour chaque magasin de données.
 
Dans le cas ci-dessus, le service lié pour chaque magasin de données doit être créé avec le même IR et l’IR doit être en mesure d’accéder aux deux banques de données via le réseau. Quel que soit l’emplacement d’installation de l’IR avec le magasin de données source, le magasin de données de destination ou sur une troisième machine, si deux services liés sont créés avec d’autres IR, mais utilisés dans la même activité de copie, l’IR est utilisé, et les pilotes des deux banques de données doivent être installés sur la machine IR de destination.

#### <a name="resolution"></a>Résolution

Installez les pilotes pour la source et la destination sur le runtime d’intégration de destination et assurez-vous qu’il peut accéder au magasin de données source.
 
Si le trafic ne peut pas traverser le réseau entre deux magasins de données (par exemple, s’ils sont configurés en deux réseaux virtuels), vous risquez de ne pas terminer la copie dans une seule activité, même si l’IR est installé. Dans ce cas, vous pouvez créer deux activités de copie avec deux IR, chacune dans un réseau virtuel : Un IR pour copier depuis le magasin de données 1 vers le stockage d’objets BLOB Azure, un autre pour copier à partir du stockage BLOB Azure vers le magasin de données 2. Cela peut simuler la nécessité d’utiliser le runtime d’intégration pour créer un pont qui connecte deux banques de données déconnectées.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Un problème de synchronisation des informations d’identification provoque la perte des informations d’identification de haute disponibilité

#### <a name="symptoms"></a>Symptômes

Les informations d'identification de source de données « XXXXXXXXXX » ont été supprimées du nœud Integration Runtime actif avec la charge utile « quand vous avez supprimé le service de lien sur le portail Azure, ou la tâche n'a pas la bonne charge utile. Recréez un service de lien avec vos informations d'identification ».

#### <a name="cause"></a>Cause

Votre IR auto-hébergé est intégré en mode HA avec deux nœuds, mais ils ne sont pas dans l’état de synchronisation des informations d’identification, ce qui signifie que les informations d’identification stockées dans le nœud de répartiteur ne sont pas synchronisées avec d’autres nœuds de travail. Si un basculement se produit à partir du nœud répartiteur vers le nœud Worker, mais que les informations d’identification n’existaient que dans le nœud répartiteur précédent, la tâche échoue lors de la tentative d’accès aux informations d’identification, et vous pouvez atteindre l’erreur ci-dessus.

#### <a name="resolution"></a>Résolution

La seule façon d’éviter ce problème consiste à s’assurer que deux nœuds sont dans l’état de synchronisation des informations d’identification. Dans le cas contraire, vous devez resaisir les informations d’identification pour le nouveau répartiteur.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Impossible de choisir le certificat en raison de la clé privée manquante

#### <a name="symptoms"></a>Symptômes

1.  Importez un fichier PFX dans le magasin de certificats.
2.  Lorsque vous sélectionnez le certificat par le biais de l’interface utilisateur Configuration Manager IR, vous atteignez le message d’erreur suivant :

    ![Clé privée manquante](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Cause

- Le compte utilisateur dispose d’un privilège faible et ne peut pas accéder à la clé privée.
- Le certificat a été généré en tant que signature, mais pas en tant qu’échange de clés.

#### <a name="resolution"></a>Résolution

1.  Utilisez un compte privilégié qui peut accéder à la clé privée pour fonctionner avec l’interface utilisateur.
2.  Exécutez la commande suivante pour importer le certificat :
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Installation du runtime d’intégration IR auto-hébergé

### <a name="the-integration-runtime-registration-error"></a>Erreur d’inscription de Microsoft Integration Runtime 

#### <a name="symptoms"></a>Symptômes

Parfois, nous exécutons le runtime d’intégration IR auto-hébergé dans un autre compte pour les raisons suivantes :
- La stratégie d’entreprise interdit le compte de service.
- L’authentification est obligatoire.

Après avoir modifié le compte de service dans le panneau de service, vous constaterez peut-être que Microsoft Integration Runtime cesse de fonctionner.

![Erreur d'inscription de l’IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Cause

De nombreuses ressources sont uniquement accordées au compte de service. Lorsque vous remplacez le compte de service par un autre compte, l’autorisation de toutes les ressources dépendantes reste la même.

#### <a name="resolution"></a>Résolution

Accédez au journal des événements Microsoft Integration Runtime pour vérifier l’erreur.

![Journal des événements](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Si l’erreur s’affiche comme ci-dessus *UnauthorizedAccessException* , suivez les instructions ci-dessous :


1. Vérifiez le compte de service d’ouverture de session *DIAHostService* dans le panneau de service Windows.

    ![Compte de service d’ouverture de session](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Vérifiez si le compte de service d’ouverture de session dispose de l’autorisation R/W sur le dossier : *%programdata%\Microsoft\DataTransfer\DataManagementGateway*.

    - Par défaut, si le compte d’ouverture de session du service n’a pas été modifié, il doit avoir l’autorisation de lecture/écriture.

        ![Autorisation de service](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Si vous avez modifié le compte d’ouverture de session du service, suivez les étapes ci-dessous pour régler le problème :
        1. Désinstallez correctement le runtime d’intégration IR auto-hébergé actuel.
        1. Installez le runtime d’intégration IR auto-hébergé bits.
        1. Suivez les instructions ci-dessous pour modifier le compte de service : 
            1. Accédez au dossier d’installation de l’IR auto-hébergé et basculez vers le dossier : *Microsoft Integration Runtime\4.0\Shared*.
            1. Démarrez une ligne de commande avec des privilèges élevés. Remplacez *\<user>* et *\<password>* par vos propres nom d’utilisateur et mot de passe, puis exécutez la commande suivante :
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Si vous souhaitez passer au compte LocalSystem, veillez à utiliser un format correct pour ce compte. Voici un exemple de format correct :

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Ne **pas** utiliser le format comme indiqué ci-dessous :

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. À l’inverse, étant donné que le système local dispose de privilèges plus élevés que l’administrateur, vous pouvez également le modifier directement en « Services ».
            1. Vous pouvez utiliser un utilisateur local/de domaine pour le compte d’ouverture de session du service IR.            
        1. Inscrire le runtime d’intégration.

Si l’erreur s’affiche comme suit : *Échec du démarrage du service « Integration Runtime Service » (DIAHostService). Vérifiez que vous disposez des droits nécessaires pour démarrer les services système* , suivez les instructions ci-dessous :

1. Vérifiez le compte de service d’ouverture de session *DIAHostService* dans le panneau de service Windows.
   
    ![Compte de service d’ouverture de session](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Vérifiez si le compte de service d’ouverture de session dispose de l’autorisation **Ouvrir une session en tant que service** pour démarrer le Service Windows :

    ![Ouvrir une session en tant que service](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Informations complémentaires

Si aucun des deux modèles ci-dessus ne s’applique dans votre cas, essayez de collecter sous les journaux des événements Windows : 
- Journaux des applications et des services-> Runtime d’intégration
- Journaux d’activité Windows -> Application

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Impossible de trouver le bouton Inscrire pour inscrire un runtime d’intégration IR auto-hébergé    

#### <a name="symptoms"></a>Symptômes

Impossible de trouver le bouton de **Inscription** dans l’interface utilisateur Configuration Manager lors de l’inscription d’un Runtime d’intégration IR auto-hébergé.

![Aucun bouton Inscription](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Cause

Depuis la sortie d’ *Integration Runtime 3.0* , le bouton **Register** sur un nœud IR existant a été supprimé pour permettre un environnement plus propre et plus sécurisé. Si un nœud a été inscrit sur un IR (qu’il soit en ligne ou non), pour le réinscrire dans un autre IR, vous devez désinstaller le nœud précédent, puis installer et inscrire le nœud.

#### <a name="resolution"></a>Résolution

1. Accédez au panneau de configuration pour désinstaller l’IR existant.

    > [!IMPORTANT] 
    > Dans le processus ci-dessous, sélectionnez Oui. Ne conservez pas les données pendant le processus de désinstallation.

    ![Suppression de données](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Si vous n’avez pas le fichier MSI du programme d’installation du runtime d’intégration, accédez au [Centre de téléchargement](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) pour télécharger les IR les plus récents.
1. Installer le MSI et inscrire le runtime d'intégration


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Impossible d’enregistrer le runtime d’intégration IR auto-hébergé en raison du localhost    

#### <a name="symptoms"></a>Symptômes

Impossible d’inscrire le runtime d’intégration IR auto-hébergé sur un nouvel ordinateur avec get_LoopbackIpOrName.

**Débogage :** Une erreur d'exécution s'est produite.
L’initialiseur de type pour « Microsoft. DataTransfer. DIAgentHost. DataSourceCache » a levé une exception.
Une erreur non récupérable s’est produite lors d’une recherche de base de données.
 
**Détail de l’exception :** System.TypeInitializationException : L’initialiseur de type pour « Microsoft. DataTransfer. DIAgentHost. DataSourceCache » a levé une exception. ---> System.Net.Sockets.SocketException: Une erreur non récupérable s’est produite lors d’une recherche de base de données sur System net.DNS.GetAddrInfo (String name).

#### <a name="cause"></a>Cause

Le problème se produit généralement lors de la résolution de localhost.

#### <a name="resolution"></a>Résolution

Utilisez Localhost 127.0.0.1 pour héberger le fichier et résoudre ce problème.


### <a name="self-hosted-setup-failed"></a>Échec de l’installation auto-hébergée    

#### <a name="symptoms"></a>Symptômes

Impossible de désinstaller un Runtime d’intégration ou d’installer un nouveau Runtime d’intégration existant ou de mettre à niveau un IR existant vers un nouveau Runtime d’intégration.

#### <a name="cause"></a>Cause

L’installation dépend du service Windows Installer. Plusieurs raisons peuvent être à l’origine du problème d’installation :
- Espace disque insuffisant
- Permissions manquantes
- Le service NT est verrouillé pour une raison quelconque
- Utilisation du processeur trop élevée
- Le fichier MSI est hébergé dans un emplacement réseau lent
- Certains registres ou fichiers système ont été touchés par inadvertance


## <a name="self-hosted-ir-connectivity-issues"></a>Problème de connexion de l’IR auto-hébergé

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Le runtime d’intégration auto-hébergé ne peut pas se connecter au service Cloud

#### <a name="symptoms"></a>Symptômes

![Problème de connexion de l’IR auto-hébergé](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Cause 

Le runtime d’intégration auto-hébergé ne peut pas se connecter au service Data Factory (backend). Ce problème est généralement dû à des paramètres réseau dans le pare-feu.

#### <a name="resolution"></a>Résolution

1. Vérifiez si le service du runtime d’intégration est en cours d’exécution.
    
   ![État d’exécution du service d’IR auto-hébergé](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Si le service est en cours d'exécution, passez à l'étape 3.

1. Si aucun proxy n’est configuré sur le runtime d’intégration auto-hébergé (qui est le paramètre par défaut), exécutez la commande PowerShell suivante sur l’ordinateur sur lequel est installé le runtime d’intégration auto-hébergé :

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > L’URL du service peut varier en fonction de votre emplacement Data Factory. L’URL du service se trouve sous **UI ADF** > **Connexions** > **Runtimes d’intégration** >  **Modifier l’IR auto-hébergé** > **Nœuds** > **Afficher les URL du service**.
            
    Voici la réponse attendue :
            
    ![Réponse de la commande PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Si vous ne recevez pas la réponse attendue, utilisez l’une des méthodes suivantes en fonction de votre situation :
            
    * Si vous recevez le message « le nom distant n’a pas pu être résolu », il existe un problème DNS (Domain Name System). Contactez votre équipe réseau pour résoudre ce problème.
    * Si vous recevez un message « certificat SSL/TLS non approuvé », vérifiez si le certificat de https://wu2.frontend.clouddatahub.net/ est approuvé sur l’ordinateur, puis installez le certificat public à l’aide du gestionnaire de certificats. Cette action doit atténuer le problème.
    * Accédez à **Windows** > **Visionneur d’événements (journaux)**  > **Journaux des applications et des services** > **Runtime d’intégration** et recherchez les défaillances provoquées par DNS, une règle de pare-feu ou des paramètres réseau d’entreprise. (Si vous trouvez une de ces défaillances, forcez la fermeture de la connexion.) Étant donné que chaque entreprise a personnalisé des paramètres réseau, contactez votre équipe réseau pour résoudre ces problèmes.

1. Si « proxy » a été configuré sur le runtime d’intégration auto-hébergé, vérifiez que votre serveur proxy est en mesure d’accéder à notre point de terminaison de service. Pour obtenir un exemple de commande, consultez [PowerShell, les requêtes Web et les proxys](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Voici la réponse attendue :
            
![Réponse de la commande PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considérations liées au proxy :
> *    Vérifiez si le serveur proxy doit être placé dans la liste des destinataires approuvés. Dans ce cas, assurez-vous que [ces domaines](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) figurent dans la liste des destinataires approuvés.
> *    Vérifiez si le certificat TLS/SSL « wu2.frontend.clouddatahub.net/ » est approuvé sur le serveur proxy.
> *    Si vous utilisez l’authentification Active Directory sur le proxy, remplacez le compte de service par le compte d’utilisateur qui peut accéder au proxy en tant que « Service Integration Runtime ».

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Message d’erreur : Le nœud du runtime d’intégration auto-hébergé /le runtime d’intégration auto-hébergé logique présente l’état Inactif/ « En cours d’exécution (limité) »

#### <a name="cause"></a>Cause 

Le nœud Runtime intégré auto-hébergé peut avoir un État **inactif** , comme indiqué dans la capture d’écran suivante :

![Nœud du runtime d’intégration auto-hébergé inactif](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ce comportement se produit lorsque les nœuds ne peuvent pas communiquer entre eux.

#### <a name="resolution"></a>Résolution

1. Connectez-vous à la machine virtuelle hébergée sur un nœud. Sous **Journaux des applications et des services** > **Runtime d’intégration** , ouvrez Observateur d’événements, puis filtrez tous les journaux d’erreurs.

1. Vérifiez si un journal des erreurs contient l’erreur suivante : 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Si vous rencontrez cette erreur, exécutez la commande suivante sur une ligne de commande : 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Si vous recevez l’erreur suivante, contactez votre service informatique pour obtenir de l’aide pour résoudre ce problème. Lorsque vous parvenez à utiliser Telnet, contactez le support Microsoft si vous rencontrez toujours des problèmes pour l’état du nœud IR d’intégration.
        
   ![Erreur de ligne de commande](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Vérifiez si le journal des erreurs contient ce qui suit :

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Pour résoudre le problème, essayez l’une des méthodes suivantes, voire les deux :
    - Placez tous les nœuds dans le même domaine.
    - Ajoutez l’adresse IP au mappage de l’hôte dans tous les fichiers hôtes de la machine virtuelle hébergée.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Problème de connectivité entre le runtime d’intégration IR auto-hébergé et Data Factory ou le runtime d’intégration IR auto-hébergé et la source de données/le récepteur

Pour résoudre le problème de connectivité réseau, vous devez savoir comment collecter la trace réseau, comprendre comment l’utiliser et [analyser la trace netmon](#how-to-analyze-netmon-trace) avant d’appliquer les outils Netmon à de vrais cas à partir du runtime d’intégration auto-hébergé.

#### <a name="symptoms"></a>Symptômes

Parfois, lorsque nous dépannons des problèmes de connectivité comme le suivant entre le runtime d’intégration auto-hébergé et Data Factory : 

![Échec de la requête HTTP](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Ou un problème entre le runtime d’intégration auto-hébergé et la source de données/le récepteur, nous rencontrons les erreurs suivantes :

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Résolution :

Lorsque vous rencontrez les problèmes ci-dessus, reportez-vous aux instructions de résolution suivantes :

Prenez la trace netmon et analysez-la plus en détail.
- Tout d’abord, vous pouvez définir le filtre pour voir toute réinitialisation à partir du serveur vers le côté client. Dans l’exemple ci-dessous, vous pouvez voir que le côté serveur correspond au serveur Data Factory.

    ![Serveur Data Factory](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Lorsque vous obtenez le package de réinitialisation, vous pouvez trouver la conversation en suivant le protocole TCP.

    ![Rechercher la conversation](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Vous pouvez ensuite obtenir la conversation entre le client et le serveur Data Factory ci-dessous en supprimant le filtre.

    ![Obtenir la conversation](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- En fonction de la trace netmon collectée, nous pouvons dire que la durée de vie (TimeToLive) totale est 64. D’après les **valeurs de durée de vie et de limite de tronçon par défaut** mentionnées dans [cet article](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (comme extrait ci-dessous), nous pouvons voir que le système Linux réinitialise le package et provoque la déconnexion.

    Les valeurs de durée de vie et de limite de tronçon par défaut varient entre différents systèmes d’exploitation. Voici les valeurs par défaut pour certains d’entre-eux :
    - Noyau Linux 2.4 (circa 2001) : 255 pour TCP, UDP et ICMP
    - Noyau Linux 4.10 (2015) : 64 pour TCP, UDP et ICMP
    - Windows XP (2001) : 128 pour TCP, UDP et ICMP
    - Windows 10 (2015) : 128 pour TCP, UDP et ICMP
    - Windows Server 2008 : 128 pour TCP, UDP et ICMP
    - Windows Server 2019 (2018) : 128 pour TCP, UDP et ICMP
    - macOS (2001) : 64 pour TCP, UDP et ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Toutefois, la durée de vie affichée est de 61 au lieu de 64 dans l’exemple ci-dessus, car lorsque le package réseau atteint la destination, il doit passer par différents tronçons tels que des routeurs/périphériques réseau. Le nombre de routeurs/périphériques réseau sera déduit dans la durée de vie (TTL) finale.
    Dans ce cas, nous pouvons voir que la réinitialisation (Reset) peut être envoyée à partir du système Linux avec TTL 64.

- Nous devons vérifier le quatrième tronçon du runtime d’intégration auto-hébergé pour confirmer l’origine de l’appareil de réinitialisation.
 
    *Package réseau provenant du système Linux A avec TTL 64 -> B TTL 64 moins 1 = 63 -> C TTL 63 moins 1 = 62 -> TTL 62 moins 1 = 61 Runtime d’intégration auto-hébergé*

- Dans le cas idéal, la durée de vie (TTL) sera de 128, ce qui signifie que le système Windows exécute notre Data Factory. Comme indiqué dans l’exemple ci-dessous, *128 – 107 = 21 tronçons* , ce qui signifie que 21 tronçons pour le package ont été envoyés depuis Data Factory vers le runtime d’intégration auto-hébergé au cours de la négociation TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Par conséquent, vous devez faire appel à l’équipe réseau pour vérifier à quoi correspond le quatrième tronçon provenant du runtime d'intégration auto-hébergé. S’il s’agit du pare-feu en tant que système Linux, consultez tous les journaux pour déterminer la raison pour laquelle ce périphérique réinitialise le package après la négociation TCP 3. Toutefois, si vous n’êtes pas sûr de l’emplacement où effectuer vos investigations, essayez d’obtenir la trace netmon à partir du runtime d'intégration auto-hébergé et du pare-feu pendant la période problématique afin de déterminer quel périphérique peut réinitialiser ce package et provoquer la déconnexion. Dans ce cas, vous devez également encourager votre équipe réseau à avancer.

### <a name="how-to-analyze-netmon-trace"></a>Procédure d’analyse de la trace netmon

> [!NOTE] 
> Les instructions ci-dessous s’appliquent à la trace netmon. Étant donné que la trace netmon n’est pas prise en charge pour l’instant, vous pouvez tirer parti de Wireshark.

Lorsque vous essayez d’établir une connexion Telnet à **8.8.8.8 888** avec la trace netmon collectée, vous êtes censé voir la trace ci-dessous :

![trace netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![trace netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Cela signifie que vous n’avez pas pu établir la connexion TCP côté serveur **8.8.8.8** sur la base du port **888** , de sorte que vous y voyez deux packages supplémentaires **SynReTransmit**. Étant donné que la source **SELF-HOST2** n’a pas pu établir de connexion à **8.8.8.8** au niveau du premier package, elle continue à établir la connexion.

> [!TIP]
> - Vous pouvez cliquer sur **Charger le filtre** -> **Filtre standard** -> **Adresses** -> **Adresses IPv4**.
> - Entrez **IPv4.Address == 8.8.8.8** comme filtre, puis cliquez sur **Appliquer**. Après cela, vous verrez uniquement la communication de l’ordinateur local à la destination **8.8.8.8**.

![filtre d’adresses 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filtre d’adresses 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

L’exemple ci-dessous montre à quoi ressemblerait un scénario approprié. 

- Si Telnet **8.8.8.8 53** fonctionne correctement sans aucun problème, vous pouvez voir la négociation TCP 3, puis la session se termine avec la négociation TCP 4.

    ![exemple de scénario correct 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![exemple de scénario correct 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Sur la base de la négociation TCP 3 ci-dessus, vous pouvez voir le flux de travail ci-dessous :

    ![Flux de travail de négociation TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- La négociation TCP 4 destinée à terminer la session et son flux de travail s’affichent comme suit :

    ![Négociation TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Flux de travail de négociation TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Réception d’un e-mail visant à mettre à jour la configuration du réseau pour permettre la communication avec les nouvelles adresses IP

#### <a name="email-notification-from-microsoft"></a>Notification par e-mail de Microsoft

Vous pouvez recevoir la notification par e-mail ci-dessous, qui vous recommande de mettre à jour la configuration du réseau afin de permettre la communication avec les nouvelles adresses IP pour Azure Data Factory d’ici le 8 novembre 2020 :

   ![E-mail de notification](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Comment déterminer si vous êtes concerné par cette notification

Cette notification a un impact sur les scénarios suivants :
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Scénario 1 : Communications sortantes à partir du runtime d’intégration auto-hébergé exécuté localement derrière le pare-feu d’entreprise
Comment déterminer si vous êtes concerné :
- Vous n’êtes pas concerné si vous définissez des règles de pare-feu basées sur des noms de domaine complets à l’aide de l’approche décrite dans ce document : [Configurations de pare-feu et configuration de la liste d’autorisation pour les adresses IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- Vous êtes néanmoins concerné si vous activez explicitement la liste d’autorisation pour les adresses IP sortantes sur votre pare-feu d’entreprise.

Action à prendre si vous êtes concerné : demandez à votre équipe d’infrastructure réseau de mettre à jour votre configuration réseau pour utiliser les adresses IP les plus récentes de Data Factory d’ici le 8 novembre 2020.  Pour télécharger les adresses IP les plus récentes, accédez au [lien de téléchargement de la plage d’adresses IP des étiquettes de service](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Scénario 2 : Communications sortantes à partir du runtime d’intégration auto-hébergé exécuté sur une machine virtuelle Azure au sein d’un réseau virtuel Azure géré par le client
Comment déterminer si vous êtes concerné :
- Vérifiez si vous avez des règles de groupe de sécurité réseau sortantes dans votre réseau privé qui contient le runtime d’intégration auto-hébergé. En l’absence de restrictions sur les communications sortantes, il n’y a aucun impact.
- Si vous avez des restrictions sur les règles de trafic sortant, vérifiez si vous utilisez ou non une étiquette de service. Si vous utilisez une étiquette de service, vous n’avez pas besoin de modifier ou d’ajouter quoi que ce soit, car les nouvelles plages d’adresses IP se trouvent sous l’étiquette de service actuelle. 
 ![Vérification de la destination](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- Vous êtes néanmoins concerné si vous activez explicitement la liste d’autorisation pour les adresses IP sortantes sur votre paramètre de règles NSG sur le réseau virtuel Azure.

Action à prendre si vous êtes concerné : demandez à votre équipe d’infrastructure réseau de mettre à jour les règles de groupe de sécurité réseau sur votre configuration de réseau virtuel Azure pour utiliser les adresses IP les plus récentes de Data Factory d’ici le 8 novembre 2020.  Pour télécharger les adresses IP les plus récentes, accédez au [lien de téléchargement de la plage d’adresses IP des étiquettes de service](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Scénario 3 : Communications sortantes à partir d’Azure-SSIS Integration Runtime dans le réseau virtuel Azure géré par le client
- Vérifiez si vous avez des règles de groupe de sécurité réseau sortantes dans votre réseau privé qui contient Azure-SSIS Integration Runtime. En l’absence de restrictions sur les communications sortantes, il n’y a aucun impact.
- Si vous avez des restrictions sur les règles de trafic sortant, vérifiez si vous utilisez ou non une étiquette de service. Si vous utilisez une étiquette de service, vous n’avez pas besoin de modifier ou d’ajouter quoi que ce soit, car les nouvelles plages d’adresses IP se trouvent sous l’étiquette de service actuelle.
- Vous êtes néanmoins concerné si vous activez explicitement la liste d’autorisation pour les adresses IP sortantes sur votre paramètre de règles NSG sur le réseau virtuel Azure.

Action à prendre si vous êtes concerné : demandez à votre équipe d’infrastructure réseau de mettre à jour les règles de groupe de sécurité réseau sur votre configuration de réseau virtuel Azure pour utiliser les adresses IP les plus récentes de Data Factory d’ici le 8 novembre 2020.  Pour télécharger les adresses IP les plus récentes, accédez au [lien de téléchargement de la plage d’adresses IP des étiquettes de service](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Impossible d’établir une relation de confiance pour le canal sécurisé SSL/TLS 

#### <a name="symptoms"></a>Symptômes

Le runtime d’intégration auto-hébergé n’a pas pu se connecter au service ADF.

En consultant le journal des événements du runtime d’intégration auto-hébergé ou les journaux de notification du client dans la table CustomLogEvent, vous trouvez le message d’erreur suivant :

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Comment vérifier le certificat de serveur du service ADF :

La méthode la plus simple consiste à ouvrir l’URL du service ADF dans un navigateur (par exemple, ouvrez https://eu.frontend.clouddatahub.net/ sur l’ordinateur sur lequel le runtime d’intégration auto-hébergé est installé), puis consultez les informations du certificat de serveur :

  ![Vérifier le certificat de serveur du service ADF](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Vérifier le chemin d’accès du certificat de serveur](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Cause :

Deux raisons possibles à ce problème :

- L’autorité de certification racine du certificat de serveur de service ADF n’est pas approuvée sur l’ordinateur sur lequel le runtime d’intégration auto-hébergé est installé. 
- Vous utilisez un proxy dans votre environnement et le certificat de serveur du service ADF est remplacé par le proxy, tandis que le certificat de serveur remplacé n’est pas approuvé par l’ordinateur sur lequel le runtime d’intégration auto-hébergé est installé.

#### <a name="solution"></a>Solution

- Pour la raison 1, assurez-vous que le certificat de serveur ADF et sa chaîne de certificats sont approuvés par l’ordinateur sur lequel le runtime d’intégration auto-hébergé est installé.
- Pour la raison 2, approuvez l’autorité de certification racine remplacée sur l’ordinateur du runtime d’intégration auto-hébergé ou configurez le proxy pour qu’il ne remplace pas le certificat de serveur ADF.

Pour plus d’informations sur l’approbation d’un certificat sur Windows, consultez [cet article](https://docs.microsoft.com/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-info"></a>Informations supplémentaires
Nous déployons un nouveau certificat SSL, qui est signé par DigiCert. Vérifiez si DigiCert Global Root G2 figure parmi les autorités de certification racine approuvées.

  ![DigiCert Global Root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Si ce n’est pas le cas, téléchargez-le [ici](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 

## <a name="self-hosted-ir-sharing"></a>Partage du runtime d’intégration auto-hébergé

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Le partage du runtime d’intégration IR auto-hébergé à partir d’un autre locataire n’est pas pris en charge 

#### <a name="symptoms"></a>Symptômes

Vous pouvez remarquer d’autres fabriques de données (sur différents locataires) lors de la tentative de partage du runtime d’intégration IR auto-hébergé à partir de l’interface utilisateur Azure Data Factory, mais ne peut pas partager le runtime d’intégration IR auto-hébergé entre les fabriques de données qui se trouvent sur des locataires différents.

#### <a name="cause"></a>Cause

Le runtime d’intégration IR auto-hébergé ne peut pas être partagé entre plusieurs locataires.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances des flux de données de mappage](concepts-data-flow-performance.md)