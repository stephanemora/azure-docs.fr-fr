---
title: Installer l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Fournit un guide pour l’installation de l’outil Azure Application Consistent Snapshot Tool que vous pouvez utiliser avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 16e758ac520c8584a11c88c0823fac7cfec43c0b
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108226297"
---
# <a name="install-azure-application-consistent-snapshot-tool"></a>Installer l’outil Azure Application Consistent Snapshot Tool

Cet article fournit des instructions d’installation de l’outil Azure Application Consistent Snapshot Tool que vous pouvez utiliser avec Azure NetApp Files ou Azure Large Instance.

> [!IMPORTANT]
> Les installations distribuées représentent la seule option pour les systèmes **Azure Large Instance**, car elles sont déployées dans un réseau privé.  Par conséquent, les installations AzAcSnap doivent être effectuées sur chaque système pour garantir la connectivité.

## <a name="introduction"></a>Introduction

Le programme d’installation automatique téléchargeable est conçu pour faciliter la configuration et l’exécution des outils d’instantané avec des privilèges d’utilisateur non-racine (par exemple, azacsnap). Le programme d’installation configure l’utilisateur et place les outils d’instantané dans le sous-répertoire `$HOME/bin` de l’utilisateur (par défaut = `/home/azacsnap/bin`).
Le programme d’installation automatique tente de déterminer les paramètres et chemins d’accès appropriés pour tous les fichiers en fonction de la configuration de l’utilisateur qui effectue l’installation (par exemple, racine). Si les étapes préalables (activer la communication avec le stockage et SAP HANA) ont été exécutées en tant que racine, l’installation copie la clé privée et l’élément `hdbuserstore` dans l’emplacement de l’utilisateur de sauvegarde. Cependant, les étapes qui activent la communication avec le serveur principal de stockage et SAP HANA peuvent être exécutées manuellement par un administrateur compétent après l’installation.

## <a name="prerequisites-for-installation"></a>Prérequis pour l’installation

Suivez les instructions pour configurer et exécuter les instantanés et les commandes de récupération d’urgence. Nous vous recommandons d’effectuer les étapes suivantes en tant que racine avant d’installer et d’utiliser les outils d’instantané.

1. **Le système d’exploitation est corrigé** : consultez la mise à jour corrective et la configuration de SMT dans le [Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system).
1. **La synchronisation temporelle est configurée**. Le client doit fournir un serveur de temps compatible avec NTP et configurer le système d’exploitation en conséquence.
1. **HANA est installé** : consultez les instructions d’installation de HANA dans [Installation de SAP NetWeaver sur une base de données HANA](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Activer la communication avec le stockage](#enable-communication-with-storage)** (pour plus d’informations, voir la section dédiée) : Le client doit configurer SSH avec une paire de clés privée/publique et fournir la clé publique pour chaque nœud sur lequel les outils d’instantané doivent être exécutés sur Microsoft Operations pour une configuration sur le serveur principal de stockage.
   1. **Pour Azure NetApp Files (pour plus d’informations, voir la section dédiée)**  : le client doit générer le fichier d’authentification du principal du service.
   1. **Pour les grandes instances Azure (pour plus d’informations, voir la section dédiée)**  : le client doit configurer SSH avec une paire de clés privée/publique et fournir la clé publique pour chaque nœud sur lequel les outils d’instantané doivent être exécutés sur Microsoft Operations pour une configuration sur le serveur principal de stockage.

      Effectuez un test à l’aide de SSH pour vous connecter à l’un des nœuds (par exemple, `ssh -l <Storage UserName> <Storage IP Address>`).
      Tapez `exit` pour vous déconnecter de l’invite de stockage.

      Microsoft Operations fournit l’utilisateur et l’adresse IP du stockage au moment de l’approvisionnement.
  
1. **[Activer la communication avec SAP HANA](#enable-communication-with-sap-hana)** (pour plus d’informations, voir la section dédiée) : le client doit configurer un utilisateur SAP HANA approprié avec les privilèges requis pour effectuer l’instantané.
   1. Ce paramètre peut être testé à partir de la ligne de commande comme suit à l’aide du texte en `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Les exemples ci-dessus concernent la communication non-SSL pour SAP HANA.

## <a name="enable-communication-with-storage"></a>Activer la communication avec le stockage

Cette section explique comment activer la communication avec le stockage.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Créer un principal du service RBAC

1. Dans une session Azure Cloud Shell, vérifiez que vous êtes connecté à l’abonnement dans lequel vous voulez être associé au principal de service par défaut :

    ```azurecli-interactive
    az account show
    ```

1. Si l’abonnement n’est pas correct, utilisez

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Créez un principal de service avec Azure CLI selon l’exemple suivant

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. Vous devez obtenir une sortie similaire à l’exemple suivant :

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Cette commande attribue automatiquement le rôle de contributeur RBAC au principal de service au niveau de l’abonnement. Vous pouvez en limiter l’étendue au groupe de ressources spécifique dans lequel vos tests vont créer les ressources.

1. Coupez et collez le contenu de la sortie dans un fichier nommé `azureauth.json` stocké sur le même système que la commande `azacsnap` et sécurisez le fichier avec les autorisations système appropriées.

### <a name="azure-large-instance"></a>Instance volumineuse Azure

La communication avec le serveur principal de stockage s’exécute sur un canal SSH chiffré. Les exemples d’étapes suivants fournissent des conseils sur la configuration de SSH pour cette communication.

1. Modifiez le fichier `/etc/ssh/ssh_config`.

    Reportez-vous à la sortie suivante dans laquelle la ligne `MACs hmac-sha1` a été ajoutée :

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Créez une paire de clés publique/privée.

    Pour générer la paire de clés à l’aide de l’exemple de commande suivant, n’entrez pas de mot de passe lors de la génération d’une clé.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Envoyez la clé publique à Microsoft Operations.

    Envoyez la sortie de la commande `cat /root/.ssh/id_rsa.pub` (exemple ci-dessous) à Microsoft Operations pour permettre aux outils d’instantané de communiquer avec le sous-système de stockage.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Activer la communication avec SAP HANA

Les outils d’instantané communiquent avec SAP HANA et nécessitent un utilisateur disposant des autorisations appropriées pour lancer et libérer le point d’enregistrement de la base de données. L’exemple suivant illustre la configuration de l’utilisateur de SAP HANA v2 et de `hdbuserstore` pour la communication avec la base de données SAP HANA.

Les exemples de commandes suivants configurent un utilisateur (AZACSNAP) dans SYSTEMDB sur la base de données SAP HANA 2
et modifient l’adresse IP, les noms d’utilisateur et les mots de passe comme il se doit :

1. Connectez-vous à SYSTEMDB pour créer l’utilisateur.

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Créez l’utilisateur.

    Cet exemple crée l’utilisateur AZACSNAP dans SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Octroyez les autorisations à l’utilisateur.

    Cet exemple définit l’autorisation permettant à l’utilisateur AZACSNAP d’exécuter un instantané du stockage cohérent sur l’ensemble de la base de données.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *FACTULTATIF* - Empêchez l’expiration du mot de passe de l’utilisateur.

    > [!NOTE]
    > Consultez la stratégie d’entreprise avant d’effectuer cette modification.

   Cet exemple désactive l’expiration du mot de passe pour l’utilisateur AZACSNAP. Sans cette modification, le mot de passe de l’utilisateur expire, ce qui affecte la prise d’instantanés.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Configurez le magasin d’utilisateurs sécurisé SAP HANA (modifiez le mot de passe). Cet exemple utilise la commande `hdbuserstore` à partir de l’interpréteur de commandes Linux pour configurer le magasin d’utilisateurs sécurisé SAP HANA.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Consultez le magasin d’utilisateurs sécurisé SAP HANA. Pour vérifier que le magasin d’utilisateurs sécurisé est correctement configuré, utilisez la commande `hdbuserstore` pour répertorier les sorties, comme dans l’exemple suivant. Pour plus d’informations sur l’utilisation de `hdbuserstore`, consultez le site web de SAP.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Utiliser SSL pour la communication avec SAP HANA

L’outil `azacsnap` utilise la commande `hdbsql` de SAP HANA pour communiquer avec SAP HANA. Cela comprend l’utilisation d’options SSL lors du chiffrement de la communication avec SAP HANA. `azacsnap` utilise les options SSL de la commande `hdbsql` comme suit.

Les éléments suivants sont toujours utilisés avec l’option `azacsnap --ssl` :

- `-e` - Active le chiffrement TLS ou TLS/SSL. Le serveur choisit le niveau le plus élevé disponible.
- `-ssltrustcert` - Indique s’il faut valider le certificat du serveur.
- `-sslhostnameincert "*"` - Spécifie le nom d’hôte utilisé pour vérifier l’identité du serveur. Si le nom d’hôte `"*"` est spécifié, le nom d’hôte du serveur n’est pas validé.

La communication SSL nécessite également des fichiers Magasin de clés et Magasin de confiance.  Bien que ces fichiers puissent être stockés dans des emplacements par défaut sur une installation Linux, afin de s’assurer que la clé utilisée pour les différents systèmes SAP HANA est correcte (c’est-à-dire dans les cas où chaque système SAP HANA utilise différents fichiers Magasin de clés et Magasin de confiance), `azacsnap` suppose que ces fichiers sont stockés dans l’emplacement `securityPath` tel que défini dans le fichier de configuration `azacsnap`.

#### <a name="key-store-files"></a>Fichiers Magasin de clés

- Si vous utilisez plusieurs SID avec la même clé, il est plus facile de créer des liens vers l’emplacement securityPath tel que défini dans le fichier de configuration `azacsnap`.  Vérifiez que ces valeurs existent pour chaque SID à l’aide de SSL.
  - Pour OpenSSL :
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Pour commoncrypto :
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Si vous utilisez plusieurs SID avec une clé différente pour chacun, copiez (ou déplacez et renommez) les fichiers dans l’emplacement securityPath tel que défini dans le fichier de configuration `azacsnap` des SID.
  - Pour OpenSSL :
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Pour commoncrypto :
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Lorsque `azacsnap` appelle `hdbsql`, il ajoute `-sslkeystore=<securityPath>/<SID>_keystore` à la ligne de commande.

#### <a name="trust-store-files"></a>Fichiers Magasin de confiance

- Si vous utilisez plusieurs SID avec la même clé, créez des liens physiques vers l’emplacement securityPath tel que défini dans le fichier de configuration `azacsnap`.  Vérifiez que ces valeurs existent pour chaque SID à l’aide de SSL.
  - Pour OpenSSL :
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Pour commoncrypto :
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Si vous utilisez plusieurs SID avec une clé différente pour chacun, copiez (ou déplacez et renommez) les fichiers dans l’emplacement securityPath tel que défini dans le fichier de configuration `azacsnap` des SID.
  - Pour OpenSSL :
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Pour commoncrypto :
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> Le composant `<SID>` des noms de fichiers doit être l’identificateur système SAP HANA tout en majuscules (par exemple, `H80`, `PR1`, etc.)

Lorsque `azacsnap` appelle `hdbsql`, il ajoute `-ssltruststore=<securityPath>/<SID>_truststore` à la ligne de commande.

Par conséquent, l’exécution de `azacsnap -c test --test hana --ssl openssl`, où le `SID` est `H80` dans le fichier de configuration, établit les connexions `hdbsql` comme suit :

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> Le caractère `\` est un retour à la ligne de ligne de commande qui améliore la lisibilité des nombreux paramètres transmis sur la ligne de commande.

## <a name="installing-the-snapshot-tools"></a>Installation des outils d’instantané

Le programme d’installation automatique téléchargeable est conçu pour faciliter la configuration et l’exécution des outils d’instantané avec des privilèges d’utilisateur non-racine (par exemple, azacsnap). Le programme d’installation configure l’utilisateur et place les outils d’instantané dans le sous-répertoire `$HOME/bin` de l’utilisateur (par défaut = `/home/azacsnap/bin`).

Le programme d’installation automatique tente de déterminer les paramètres et chemins d’accès appropriés pour tous les fichiers en fonction de la configuration de l’utilisateur qui effectue l’installation (par exemple, racine). Si les étapes de configuration précédentes (activer la communication avec le stockage et SAP HANA) ont été exécutées en tant que racine, l’installation copie la clé privée et l’élément `hdbuserstore` dans l’emplacement de l’utilisateur de sauvegarde. Cependant, les étapes qui activent la communication avec le serveur principal de stockage et SAP HANA peuvent être exécutées manuellement par un administrateur compétent après l’installation.

> [!NOTE]
> Dans les versions antérieures de SAP HANA sur des installations Grandes instances Azure, le répertoire des outils d’instantané préinstallés était `/hana/shared/<SID>/exe/linuxx86_64/hdb`.

Une fois les [étapes préalables](#prerequisites-for-installation) terminées, il est désormais possible d’installer les outils d’instantané à l’aide du programme d’installation automatique comme suit :

1. Copiez le programme d’installation automatique téléchargé sur le système cible.
1. Exécutez le programme d’installation automatique en tant qu’utilisateur `root` (voir l’exemple ci-dessous). Si nécessaire, rendez le fichier exécutable à l’aide de la commande `chmod +x *.run`.

L’exécution de la commande du programme d’installation automatique sans argument a pour effet d’afficher de l’aide sur l’utilisation du programme d’installation pour installer les outils d’instantané :

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Le programme d’installation automatique offre une option permettant d’extraire (-X) les outils d’instantané du lot sans création ni configuration d’un utilisateur. Un administrateur expérimenté peut ainsi terminer manuellement la configuration ou copier les commandes pour mettre à niveau une installation existante.

### <a name="easy-installation-of-snapshot-tools-default"></a>Installation facile des outils d’instantané (par défaut)

Le programme d’installation a été conçu pour installer rapidement les outils d’instantané pour SAP HANA sur Azure. Par défaut, si le programme d’installation est exécuté uniquement avec l’option -I, les étapes suivantes sont effectuées :

1. Créer l’utilisateur d’instantané « azacsnap » ainsi que le répertoire de base et définir l’appartenance à un groupe.
1. Configurer le `~/.profile` de connexion de l’utilisateur azacsnap.
1. Rechercher dans le système de fichiers les répertoires à ajouter au `$PATH`de l’utilisateur azacsnap. Il s’agit généralement des chemins d’accès aux outils SAP HANA, tels que `hdbsql` et `hdbuserstore`.
1. Rechercher dans le système de fichiers les répertoires à ajouter au `$LD_LIBRARY_PATH` de l’utilisateur azacsnap. Pour s’exécuter correctement, de nombreuses commandes exigent qu’un chemin d’accès de bibliothèque soit défini. Il est ainsi défini pour l’utilisateur installé.
1. Copier les clés SSH pour le stockage back-end pour azacsnap à partir de l’utilisateur « racine » (celui qui exécute l’installation). Cela suppose que l’utilisateur « racine » a déjà configuré la connectivité au stockage.
    - Voir la section « [Activer la communication avec le stockage](#enable-communication-with-storage) ».
1. Copiez le magasin d’utilisateurs sécurisé de la connexion SAP HANA pour l’utilisateur cible, azacsnap. Cela suppose que l’utilisateur « racine » a déjà configuré le magasin d’utilisateurs sécurisé. Voir la section « Activer la communication avec SAP HANA ».
1. Les outils d’instantané sont extraits dans `/home/azacsnap/bin/`.
1. Les commandes de `/home/azacsnap/bin/` ont leurs autorisations définies (bit de propriété et d’exécutable, etc.).

L’exemple suivant illustre la sortie correcte du programme d’installation lorsqu’il est exécuté avec l’option d’installation par défaut.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Désinstallation des outils d’instantané

Si les outils d’instantané ont été installés avec les paramètres par défaut, la désinstallation nécessite uniquement la suppression de l’utilisateur pour lequel les commandes ont été installées (valeur par défaut = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Installation manuelle des outils d’instantané

Dans certains cas, il est nécessaire d’installer manuellement les outils, mais il est recommandé d’utiliser l’option par défaut du programme d’installation pour faciliter le processus.

Chaque ligne commençant par un caractère `#` montre que les exemples de commandes qui suivent ce caractère sont exécutés par l’utilisateur racine. Le caractère `\` situé à la fin d’une ligne est le caractère de continuation de ligne standard pour une commande d’interpréteur de commandes.

En tant que superutilisateur racine, une installation manuelle peut être effectuée comme suit :

1. Obtenez l’ID de groupe « sapsys ». Dans le cas présent, l’ID de groupe est 1010.

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Créez l’utilisateur d’instantané « azacsnap » ainsi que le répertoire de base et définissez l’appartenance à un groupe à l’aide de l’ID de groupe de l’étape 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Vérifiez que le `.profile` de connexion de l’utilisateur azacsnap existe.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Recherchez dans le système de fichiers les répertoires à ajouter au $PATH de l’utilisateur azacsnap. Il s’agit généralement des chemins d’accès aux outils SAP HANA, tels que `hdbsql` et `hdbuserstore`.

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Ajoutez le chemin d’accès mis à jour au profil de l’utilisateur.

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Recherchez dans le système de fichiers les répertoires à ajouter au $LD_LIBRARY_PATH de l’utilisateur azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Ajoutez le chemin d’accès de bibliothèque mis à jour au profil de l’utilisateur.

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Sur des Grandes instances Azure
    1. Copiez les clés SSH pour le stockage back-end pour azacsnap à partir de l’utilisateur « racine » (celui qui exécute l’installation). Cela suppose que l’utilisateur « racine » a déjà configuré la connectivité au stockage.
       > Voir la section « [Activer la communication avec le stockage](#enable-communication-with-storage) ».

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Définissez correctement les autorisations de l’utilisateur pour les fichiers SSH.

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Sur Azure NetApp Files
    1. Configurez le chemin d’accès `DOTNET_BUNDLE_EXTRACT_BASE_DIR` de l’utilisateur conformément aux instructions d’extraction de fichier unique .NET Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Copiez le magasin d’utilisateurs sécurisé de la connexion SAP HANA pour l’utilisateur cible, azacsnap. Cela suppose que l’utilisateur « racine » a déjà configuré le magasin d’utilisateurs sécurisé.
    > Voir la section « [Activer la communication avec SAP HANA](#enable-communication-with-sap-hana) ».

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Définissez correctement les autorisations de l’utilisateur pour les fichiers `hdbuserstore`.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extrayez les outils d’instantané dans /home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Rendez les commandes exécutables.

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Vérifiez que les autorisations de propriété définies sur le répertoire de base de l’utilisateur sont correctes.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Terminer l’installation des outils d’instantané

Le programme d’installation indique les étapes à suivre une fois l’installation des outils d’instantané effectuée.
Procédez comme suit pour configurer et tester les outils d’instantané.  Une fois les tests réussis, prenez le premier instantané de stockage cohérent sur la base de données.

La sortie suivante montre les étapes à effectuer après avoir exécuté le programme d’installation avec les options d’installation par défaut :

1. Modifiez le compte d’utilisateur d’instantané
    1. `su - azacsnap`
1. Configurez le magasin d’utilisateurs sécurisé HANA
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Modifiez l’emplacement des commandes
   1. `cd /home/azacsnap/bin/`
1. Configurez le fichier de détails du client
   1. `azacsnap -c configure –-configuration new`
1. Testez la connexion au stockage...
   1. `azacsnap -c test –-test storage`
1. Testez la connexion à HANA...
    1. sans SSL
       1. `azacsnap -c test –-test hana`
    1. avec SSL, vous devez choisir l’option SSL appropriée
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Exécutez votre première sauvegarde d’instantané
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

L’étape 2 est obligatoire si l’opération « [Activer la communication avec SAP HANA](#enable-communication-with-sap-hana) » n’a pas été effectuée avant l’installation.

> [!NOTE]
> Les commandes de test doivent s’exécuter correctement. Sinon, les commandes risquent d’échouer.

## <a name="configuring-the-database"></a>Configuration de la base de données

Cette section explique comment configurer la base de données.

### <a name="sap-hana-configuration"></a>Configuration de SAP HANA

Il est recommandé d’appliquer certaines modifications à SAP HANA pour garantir la protection du catalogue et des sauvegardes de journaux. Par défaut, `basepath_logbackup` et `basepath_catalogbackup` envoient leurs fichiers de sortie dans le répertoire `$(DIR_INSTANCE)/backup/log` et il est peu probable que ce chemin d’accès se trouve sur un volume dont `azacsnap` doit prendre un instantané. Ces fichiers ne seront pas protégés avec des instantanés de stockage.

Les exemples de commandes `hdbsql` suivants sont destinés à illustrer la définition des chemins d’accès des journaux et des catalogues situés sur des volumes de stockage dont `azacsnap` peut prendre un instantané. Pensez à vérifier que les valeurs de la ligne de commande correspondent à la configuration locale de SAP HANA.

### <a name="configure-log-backup-location"></a>Configurer l’emplacement de sauvegarde du journal

Dans cet exemple, la modification est apportée au paramètre `basepath_logbackup`.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Configurer l’emplacement de sauvegarde du catalogue

Dans cet exemple, la modification est apportée au paramètre `basepath_catalogbackup`. Commencez par vérifier que le chemin d’accès `basepath_catalogbackup` existe sur le système de fichiers. Dans le cas contraire, créez-le avec la même propriété que le répertoire.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Si le chemin d’accès doit être créé, l’exemple suivant le créé et définit la propriété et les autorisations appropriées. Ces commandes doivent être exécutées en tant que racine.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

L’exemple suivant modifie le paramètre SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Vérifier les emplacements de sauvegarde du journal et du catalogue

Après avoir apporté les modifications ci-dessus, vérifiez que les paramètres sont corrects à l’aide de la commande suivante.
Dans cet exemple, les paramètres définis à l’aide des instructions ci-dessus s’affichent en tant que paramètres système.

> Cette requête renvoie également les paramètres par défaut à des fins de comparaison.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Configurer le délai d’expiration de la sauvegarde de journal

Par défaut, SAP HANA effectue une sauvegarde de journal en 900 secondes (15 minutes). Il est recommandé de réduire cette valeur à 300 secondes (c’est-à-dire 5 minutes).  Il est ensuite possible d’exécuter des sauvegardes régulières (par exemple, toutes les 10 minutes) en ajoutant le volume log_backups à la section Autre volume du fichier de configuration.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Vérifier le délai d’expiration de la sauvegarde de journal

Après avoir modifié le délai d’expiration de la sauvegarde de journal, vérifiez qu’il a bien été défini comme suit.
Dans cet exemple, les paramètres définis s’affichent en tant que paramètres système, mais cette requête renvoie également les paramètres par défaut à des fins de comparaison.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer l’outil Azure Application Consistent Snapshot Tool](azacsnap-cmd-ref-configure.md)
