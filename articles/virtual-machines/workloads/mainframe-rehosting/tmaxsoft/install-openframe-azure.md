---
title: Installer TmaxSoft OpenFrame sur des Machines virtuelles
description: Ré-héberger votre IBM z/OS mainframe les charges de travail à l’aide de TmaxSoft OpenFrame environnement sur Machines virtuelles (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: b69ded2591478a477cd142decb39218841c9ac62
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410121"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installer TmaxSoft OpenFrame sur Azure

Découvrez comment configurer un environnement OpenFrame sur Azure approprié pour le développement, de démonstrations, de test ou de charges de travail de production. Ce didacticiel vous guide tout au long de chaque étape.

OpenFrame comprend plusieurs composants de l’environnement d’émulation mainframe sur Azure. Par exemple, les services en ligne OpenFrame remplacement le macroordinateur intergiciel (middleware) tels que IBM CICS Customer Information contrôle System (), et OpenFrame Batch, avec son composant TJES, remplace travail entrée sous-système (JES du macroordinateur IBM).

OpenFrame fonctionne avec une base de données relationnel, y compris la base de données Oracle, Microsoft SQL Server, IBM Db2 et MySQL. Cette installation de OpenFrame utilise la base de données relationnelle TmaxSoft Tibero. OpenFrame et Tibero s’exécutent sur un système d’exploitation Linux. Ce didacticiel installe CentOS 7.3, bien que vous pouvez utiliser les autres distributions Linux prises en charge. Le serveur d’applications OpenFrame et la base de données Tibero sont installés sur une machine virtuelle (VM).

Le didacticiel vous guide tout au long de l’installation des composants OpenFrame suite. Certains doivent être installés séparément.

Composants de la main OpenFrame :

- Packages d’installation requis.
- Tibero de base de données.
- Open Database Connectivity (ODBC) est utilisé par les applications dans OpenFrame pour communiquer avec la base de données Tibero.
- Base de OpenFrame, l’intergiciel (middleware) qui gère l’ensemble du système.
- Lot OpenFrame, la solution qui remplace les systèmes de traitement par lots du macroordinateur.
- TACF, il s’agit d’un module de service qui contrôle l’accès utilisateur aux systèmes et aux ressources.
- ProSort, il s’agit d’un outil de tri pour les transactions par lots.
- OFCOBOL, un compilateur qui interprète les programmes COBOL du macroordinateur.
- OFASM, un compilateur qui interprète les programmes d’assembleur du macroordinateur.
- OpenFrame Server Type C (OSC), la solution qui remplace le macroordinateur intergiciel (middleware) et IBM CICS.
- Java entreprise utilisateur Solution (JEUS), un serveur d’applications web qui est certifié pour Java Enterprise Edition 6.
- OFGW, le composant passerelle OpenFrame qui fournit un écouteur 3270.
- OFManager, il s’agit d’une solution qui fournit des fonctions de gestion et d’opération du OpenFrame dans l’environnement web.

Autres composants de OpenFrame de requis :

- OSI, la solution qui remplace le macroordinateur intergiciel (middleware) et le contrôleur de domaine IMS.
- TJES, la solution qui fournit l’environnement de JES du macroordinateur.
- OFTSAM, la solution qui permet de fichiers (V) SAM à utiliser dans le système ouvert.
- OFHiDB, la solution qui remplace le macroordinateur 's IMS DB.
- OFPLI, un compilateur qui interprète le macroordinateur de PL / je programmes.
- PROTRIEVE, il s’agit d’une solution qui s’exécute à la langue de macroordinateur Easytrieve d’autorité de certification.
- OFMiner, il s’agit d’une solution qui analyse les ressources de macroordinateurs et avant de les migre vers Azure.

## <a name="architecture"></a>Architecture

La figure suivante fournit une vue d’ensemble des composants architecturaux OpenFrame 7.0 installé dans ce didacticiel :

![Composants OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Configuration requise Azure

Le tableau suivant répertorie la configuration requise pour l’installation sur Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Prérequis</th><th>Description </th></tr>
</thead>
<tbody>
<tr><td>Distributions Linux prises en charge sur Azure
</td>
<td>
Linux x86 2.6 (32 bits, 64 bits)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Matériel
</td>
<td>Cœurs : 2 (minimum)<br/>
Mémoire : 4 Go (minimum)<br/>
L’espace d’échange : 1 Go (minimum)<br/>
Disque dur : 100 Go (minimum)<br/>
</td>
</tr>
<tr><td>Logiciel facultatif pour les utilisateurs de Windows
</td>
<td>PuTTY : Utilisé dans ce guide pour configurer les fonctionnalités de machine virtuelle<br/>
WinSCP : Un client SFTP populaire et le client FTP, vous pouvez utiliser<br/>
Eclipse pour Windows : Une plate-forme de développement pris en charge par TmaxSoft<br/>
(Microsoft Visual Studio n’est pas prise en charge pour l’instant)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Conditions préalables

Dans vous devez consacrer quelques jours pour assembler tous les logiciels requis et de terminer tous les processus manuels.

Avant de commencer, procédez comme suit :

- Obtenir le support d’installation OpenFrame de TmaxSoft. Si vous êtes un client TmaxSoft existant, contactez votre représentant TmaxSoft pour une copie sous licence. Sinon, demandez une version d’essai [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Demander la documentation OpenFrame en envoyant un e-mail à <support@tmaxsoft.com>.

- Obtenir un abonnement Azure si vous n’en avez pas déjà. Vous pouvez également créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- facultatif. Configurer un tunnel VPN de site à site ou une jumpbox, qui limite l’accès à la machine virtuelle Azure pour les utilisateurs autorisés de votre organisation. Cette étape n’est pas obligatoire, mais il est recommandé.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurer une machine virtuelle sur Azure pour OpenFrame et Tibero

Vous pouvez configurer l’environnement OpenFrame à l’aide de divers modèles de déploiement, mais la procédure suivante montre comment déployer le serveur d’applications OpenFrame et la base de données Tibero sur une machine virtuelle. Dans les environnements de grande taille et pour les charges de travail importants, une bonne pratique consiste à déployer la base de données séparément sur sa propre machine virtuelle pour de meilleures performances.

**Pour créer une machine virtuelle**

1. Accédez au portail Azure à <https://portal.azure.com> et connectez-vous à votre compte.

2. Cliquez sur **Machines virtuelles**.

    ![Liste des ressources dans le portail Azure](media/vm-01.png)

3. Cliquez sur **Add**.

    ![Ajoutez l’option dans le portail Azure](media/vm-02.png)

4. À droite de **systèmes d’exploitation**, cliquez sur **plus**.

     ![Plus d’option dans le portail Azure](media/vm-03.png)

5. Cliquez sur **basé sur CentOS 7.3** à suivre cette procédure pas à pas exactement, ou vous pouvez choisir une autre prise en charge la distribution Linux.

     ![Options de système d’exploitation dans le portail Azure](media/vm-04.png)

6. Dans le **notions de base** paramètres, entrez **nom**, **nom d’utilisateur**, **type d’authentification**, **abonnement** (Paiement à l’utilisation est le style AWS de paiement), et **groupe de ressources** (utilisez-en un existant ou créez un groupe TmaxSoft).

7. Lorsque vous avez terminé (y compris la paire de clés publique/privée pour **type d’authentification**), cliquez sur **envoyer**.

> [!NOTE]
> Si vous utilisez une clé publique SSH pour **type d’authentification**, consultez les étapes décrites dans la section suivante pour générer la paire de clés publique/privée, puis reprenez les étapes décrites ici.

### <a name="generate-a-publicprivate-key-pair"></a>Générer une paire de clés publique/privée

Si vous utilisez un système d’exploitation de Windows, vous devez PuTTYgen pour générer une paire de clés publique/privée.

La clé publique peut être librement partagée, mais la clé privée doit être divulguée entièrement et ne doit jamais être partagée avec un autre tiers. Après avoir généré les clés, vous devez coller le **clé publique SSH** dans la configuration, en effet, le chargement à la VM Linux. Il est stocké à l’intérieur autorisé\_clés au sein la \~directory /.ssh de du compte d’utilisateur répertoire de base. La VM Linux est alors en mesure de reconnaître et de valider la connexion une fois que vous fournissez associé **clé privée SSH** dans le client SSH (dans notre cas, il s’agit de PuTTY).

Lors de l’octroi de nouvelles personnes accéder à la machine virtuelle : 

- Chaque nouvel individu génère leurs propres clés publique/privée à l’aide de PuTTYgen.
- Les personnes stockent leurs propres clés privées séparément et envoyer les informations de clé publiques à l’administrateur de la machine virtuelle.
- L’administrateur colle le contenu de la clé publique pour le \~/.ssh/authorized\_fichier de clés.
- Ce dernier se connecte via PuTTY.

**Pour générer une paire de clés publique/privée**

1.  Téléchargez PuTTYgen à partir de <https://www.putty.org/> et installez-le à l’aide de tous les paramètres par défaut.

2.  Pour ouvrir PuTTYgen, recherchez le répertoire d’installation PuTTY dans C:\\Program Files\\PuTTY.

    ![Interface de puTTY](media/puttygen-01.png)

3.  Cliquez sur **Générer**.

    ![Boîte de dialogue Générateur de clé puTTY](media/puttygen-02.png)

4.  Après la génération, enregistrez la clé publique et la clé privée. Collez le contenu de la clé publique dans le **clé publique SSH** section de la **créer la machine virtuelle \> notions de base** volet (indiqué dans les étapes 6 et 7 dans la section précédente).

    ![Boîte de dialogue Générateur de clé puTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurer les fonctionnalités de machine virtuelle

1. Dans le portail Azure, dans le **choisir une taille** panneau, choisissez les paramètres de matériel machine Linux souhaité. Le *minimale* requises pour l’installation Tibero et OpenFrame sont 2 processeurs et 4 Go de RAM, comme indiqué dans cette installation de l’exemple :

    ![Créer des machines virtuelles : principes de base](media/create-vm-01.png)

2. Cliquez sur **3 paramètres** et utiliser les paramètres par défaut pour configurer les fonctionnalités facultatives.
3. Passez en revue les détails de votre paiement.

    ![Créer une machine virtuelle - achat](media/create-vm-02.png)

4. Envoyez vos sélections. Azure commence à déployer la machine virtuelle. Ce processus prend généralement quelques minutes.

5. Lorsque la machine virtuelle est déployée, son tableau de bord s’affiche, indiquant tous les paramètres qui ont été sélectionnés lors de la configuration. Prenez note de la **adresse IP publique**.

    ![tMax sur le tableau de bord Azure](media/create-vm-03.png)

6. Ouvrez PuTTY.

7. Pour **nom d’hôte**, tapez votre nom d’utilisateur et l’adresse IP publique vous copié. Par exemple, **nom d’utilisateur\@adresse IP publique**.

    ![Boîte de dialogue de Configuration puTTY](media/putty-01.png)

8. Dans le **catégorie** , cliquez sur **connexion \> SSH \> Auth**. Fournir le chemin d’accès à votre **clé privée** fichier.

    ![Boîte de dialogue de Configuration puTTY](media/putty-02.png)

9. Cliquez sur **Open** pour lancer la fenêtre PuTTY. Si l’opération réussit, vous êtes connecté à votre machine virtuelle CentOS s’exécutant sur Azure.

10. Pour vous connecter en tant qu’utilisateur racine, tapez **sudo bash**.

    ![Ouverture de session utilisateur racine dans la fenêtre de commande](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurer l’environnement et les packages

Maintenant que la machine virtuelle est créée et vous êtes connecté, vous devez effectuer quelques étapes d’installation et installer les packages de préinstallation requis.

1. Mapper le nom **ofdemo** à l’adresse IP locale à l’aide de vi pour modifier le fichier hosts (`vi /etc/hosts`). En supposant que notre IP est 192.168.96.148 ofdemo, il s’agit d’avant la modification :

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Il s’agit après la modification :

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Créer des groupes et des utilisateurs :

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Modifier le mot de passe utilisateur oframe7 :

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Mettre à jour les paramètres de noyau dans /etc/sysctl.conf :

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Actualiser les paramètres de noyau dynamiquement sans redémarrage :

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Obtenir les packages requis : Assurez-vous que le serveur est connecté à Internet, téléchargez les packages suivants, puis de les installer :

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Après avoir installé le package ncurses, créez les liens symboliques suivants :
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. En cas d’installation de Java RPM, procédez comme suit :

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Installer la base de données Tibero

Tibero fournit plusieurs fonctions clés dans l’environnement OpenFrame sur Azure :

- Tibero est utilisé comme magasin de données interne OpenFrame pour diverses fonctions système.
- Fichiers VSAM, y compris KSDS RRDS et ESDS, utilisent la base de données Tibero en interne pour stocker des données.
- Le référentiel de données TACF est stocké dans Tibero.
- Les informations de catalogue OpenFrame sont stockées dans Tibero.
- La base de données Tibero peut être utilisé comme un remplacement pour IBM Db2 pour stocker les données d’application.

**Pour installer Tibero**

1. Vérifiez que le fichier de programme d’installation binaire Tibero est présent et passez en revue le numéro de version.
2. Copiez le logiciel Tibero au compte d’utilisateur Tibero (oframe). Exemple :

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Ouvrez .bash\_profil VI (`vi .bash_profile`) et collez le texte suivant dans celui-ci :

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Pour exécuter le profil de l’interpréteur de commandes, à l’invite de commandes, tapez :

    ```
    source .bash_profile
    ```

5. Générer le fichier de l’info-bulle (un fichier de configuration pour Tibero), puis ouvrez-le dans vi. Exemple :

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modifier \$to\_HOME/client/config/tbdsn.tbr et le placer à la place de 127.0.0.1 oflocalhost comme indiqué :

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Créer la base de données. La sortie suivante s’affiche :

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Pour recycler Tibero, tout d’abord arrêter à l’aide de la `tbdown` commande. Exemple :

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Désormais démarrer à l’aide de Tibero `tbboot`. Exemple :

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Pour créer un espace disque logique, accéder à la base de données à l’aide de SYS utilisateur (sys/tmax), puis créez l’espace de stockage nécessaire pour le volume par défaut et TACF :

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Présent, tapez les commandes SQL suivantes :

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Démarrage Tibero et vérifiez que les processus Tibero sont en cours d’exécution :

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Sortie :

![Sortie de Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Installer ODBC

Applications dans OpenFrame communiquent avec la base de données Tibero à l’aide de l’API ODBC fourni par le projet open source unixODBC.

Pour installer ODBC :

1. Vérifiez que le fichier de programme d’installation unixODBC-2.3.4.tar.gz est présent, ou utiliser le `wget unixODBC-2.3.4.tar.gz` commande. Exemple :

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Décompressez le fichier binaire. Exemple :

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Accédez au répertoire d’unixODBC-2.3.4 et générer le Makefile à l’aide des informations sur la vérification de la machine. Exemple :

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Par défaut, unixODBC est installé dans/usr /local, par conséquent, `--prefix` transmet une valeur pour modifier l’emplacement. De même, les fichiers de configuration sont installés dans /etc par défaut, par conséquent, `--sysconfdir` transmet la valeur de l’emplacement souhaité.

4. Exécutez Makefile : `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copiez le fichier exécutable dans le répertoire du programme après la compilation. Exemple :

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Utilisez vi pour modifier le profil de l’interpréteur de commandes (`vi ~/.bash_profile`) et ajoutez le code suivant :

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Appliquer le ODBC. Modifier les fichiers suivants en conséquence. Exemple :

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Créer un lien symbolique et valider la connexion de base de données Tibero :

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

La sortie suivante s’affiche :

![Sortie de ODBC connecté à SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installer la Base de OpenFrame

Le serveur d’applications de Base est installé avant les services individuels OpenFrame utilise pour gérer le système sur Azure, y compris la gestion des processus du serveur de transaction.

**Pour installer la Base de OpenFrame**

1. Assurez-vous que l’installation de Tibero a réussi, puis vérifiez que le suivant OpenFrame\_Base7\_0\_Linux\_x86\_fichier de programme d’installation 64. bin et le fichier de configuration base.properties sont présents.

2. Mettre à jour le profil de l’interpréteur de commandes avec les informations spécifiques à Tibero suivantes :

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Exécuter le profil de l’interpréteur de commandes :`[oframe7@ofdemo ~]$ . .bash_profile`
4. Assurez-vous que les processus Tibero sont en cours d’exécution. Exemple :

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Assurez-vous que vous démarrez Tibero avant l’installation.

5. Générer la licence à l’adresse [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) et placez la Base de OpenFrame, le lot, TACF, OSC licences dans le dossier approprié :

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Téléchargez les fichiers de Base de OpenFrame binaire et base.properties :

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Exécutez le programme d’installation à l’aide du fichier base.properties. Exemple :

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Lorsque vous avez terminé, le message Installation terminée s’affiche.

8. Vérifiez la structure de répertoire de Base de OpenFrame à l’aide du `ls -ltr` commande. Exemple :

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Démarrer la Base de OpenFrame :

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![sortie de la commande tmboot](media/base-02.png)

10. Vérifiez que l’état du processus est prêt à l’aide de la commande tmadmin dans si. RDY s’affiche dans le **état** colonne pour chacun des processus :

     ![sortie de la commande tmadmin](media/base-03.png)

11. Arrêter la Base de OpenFrame :

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Installer OpenFrame Batch

OpenFrame Batch se compose de plusieurs composants qui simulent des environnements de traitement par lots de macroordinateur et est utilisé pour exécuter des traitements par lots sur Azure.

**Pour installer le lot**

1. Assurez-vous que l’installation de base a réussi, puis vérifiez que le OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_fichier de programme d’installation 64. bin et fichier de configuration batch.Properties sont présents :

2. À l’invite de commandes, tapez `vi batch.properties` pour modifier le fichier batch.properties à l’aide de vi.

3. Modifiez les paramètres comme suit :

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Pour exécuter le programme d’installation batch, à l’invite de commandes, tapez :

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Une fois l’installation terminée, démarrez les suites OpenFrame installés en tapant `tmboot` à l’invite de commandes.

    ![sortie de tmboot](media/tmboot-01.png)

6. Type `tmadmin` à l’invite de commandes pour vérifier le processus OpenFrame.

    ![Écran tMax Admin](media/tmadmin-01.png)

7. Exécutez les commandes suivantes :

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Utilisez le `tmdown` commande pour démarrer et arrêter le traitement par lots :

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Installer TACF

TACF Manager est un module de service OpenFrame qui contrôle l’accès utilisateur aux systèmes et aux ressources via la sécurité RACF.

**Pour installer TACF**

1. Vérifiez que le OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_fichier de programme d’installation 64. bin et le fichier de configuration tacf.properties sont présents.
2. Assurez-vous que l’installation de traitement par lots a réussi, puis utilisez vi pour ouvrir le fichier tacf.properties (`vi tacf.properties`).
3. Modifiez les paramètres TACF :

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Après avoir terminé le programme d’installation TACF, appliquez les variables d’environnement TACF.  À l’invite de commandes, tapez :

     ```
     source \~/.bash\_profile
     ```

5. Exécutez le programme d’installation TACF.  À l’invite de commandes, tapez :

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     La sortie ressemble à ceci :

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. À l’invite de commandes, tapez `tmboot` OpenFrame de redémarrer. La sortie ressemble à ceci :

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Vérifiez que l’état du processus est prêt à l’aide de `tmadmin` dans le `si` commande. Exemple :

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Dans le **état** colonne, RDY apparaît :

    ![RDY dans la colonne d’état](media/tmboot-02.png)

8. Exécutez les commandes suivantes :

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Arrêter le serveur à l’aide du `tmdown` commande. La sortie ressemble à ceci :

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Installation ProSort

ProSort est un utilitaire permettant de tri des données dans les transactions par lots.

**Pour installer ProSort**

1. Assurez-vous que l’installation de traitement par lots a réussi et puis vérifiez que le **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** fichier du programme d’installation n’est présent.

2. Exécutez le programme d’installation à l’aide du fichier de propriétés.  À l’invite de commandes, tapez :

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Déplacez le répertoire prosort vers l’emplacement d’origine.  À l’invite de commandes, tapez :

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Créez un sous-répertoire de licence, puis copiez le fichier de licence il. Exemple :

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Ouvrez bash.profile VI (`vi .bash_profile`) et mettre à jour comme suit :

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Pour exécuter le profil de l’interpréteur de commandes, à l’invite de commandes, tapez : `. .bash_profile`

7. Créer le fichier de configuration. Exemple :

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Créer le lien symbolique. Exemple :

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Vérifier l’installation ProSort en exécutant la `prosort -h` commande. Exemple :

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Installer OFCOBOL

OFCOBOL est le compilateur OpenFrame qui interprète les programmes COBOL du macroordinateur. 

**Pour installer OFCOBOL**

1. Assurez-vous que l’installation de lot/en ligne a réussi, puis vérifiez que le OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64. bin programme d’installation est présent.

2. Pour exécuter le programme d’installation OFCOBOL, à l’invite de commandes, tapez :

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Lisez le contrat de licence et appuyez sur ENTRÉE pour continuer.

4. Acceptez le contrat de licence. Une fois l’installation terminée, le message suivant s’affiche :

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Ouvrez le profil de bash dans vi (`vi .bash_profile`) et vérifier qui est mis à jour avec des variables OFCOBOL.
6. Exécuter le profil de l’interpréteur de commandes.  À l’invite de commandes, tapez :

     ```
      source ~/.bash_profile
     ```

7. Copiez la licence OFCOBOL dans le dossier installé. Exemple :
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Accédez au fichier de configuration tjclrun.conf OpenFrame et ouvrez-le dans vi. Exemple :
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Voici la section SYSLIB avant la modification :
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Voici la section SYSLIB après la modification :
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Passez en revue la OpenFrame\_COBOL\_InstallLog.log VI et vérifier qu’il n’y a aucune erreur. Exemple :
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Utilisez le `ofcob --version` de commandes et passez en revue le numéro de version pour vérifier l’installation. Exemple :

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Redémarrer à l’aide de OpenFrame le `tmdown/tmboot` commande.

## <a name="install-ofasm"></a>Installer OFASM

OFASM est le compilateur OpenFrame qui interprète les programmes d’assembleur du macroordinateur.

**Pour installer OFASM**

1. Assurez-vous que l’installation de lot/en ligne a réussi, puis vérifiez que le OpenFrame\_ASM3\_0\_Linux\_x86\_64. bin programme d’installation est présent.

2. Exécutez le programme d’installation. Exemple :

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Lisez le contrat de licence et appuyez sur ENTRÉE pour continuer.
4. Acceptez le contrat de licence.
5. Vérifiez que le profil bash est mis à jour avec des variables OFASM. Exemple :

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Ouvrez le fichier de configuration de tjclrun.conf OpenFrame dans vi et modifiez-la comme suit :

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Voici la section [SYSLIB] *avant* la modification :

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Voici la section [SYSLIB] *après* la modification :

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Ouvrez le OpenFrame\_ASM\_InstallLog.log VI et vérifier qu’il n’y a aucune erreur. Exemple :

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Redémarrez OpenFrame en émettant l’une des commandes suivantes :

     ```
     tmdown / tmboot
     ```

     — ou —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installer OSC

OSC est l’environnement OpenFrame similaire à IBM CICS qui prend en charge des transactions OLTP à haut débit et autres fonctions de gestion.

**Pour installer OSC**

1. Assurez-vous que l’installation de base a réussi, puis vérifiez que le OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_fichier de programme d’installation 64. bin et le fichier de configuration osc.properties sont présent.
2. Modifier les paramètres suivants dans le fichier osc.properties :
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Exécutez le programme d’installation en utilisant le fichier de propriétés comme indiqué :

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Lorsque vous avez terminé, le message « Installation terminée » s’affiche.

4. Vérifiez que le profil bash est mis à jour avec des variables OSC.
5. Passez en revue la OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log fichier. Le résultat suivant doit s’afficher :

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Utilisez vi pour ouvrir le fichier de configuration ofsys.seq. Exemple :

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Dans le \#BASE et \#par lots de sections, modifiez les paramètres comme indiqué.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Copiez le fichier de licence. Exemple :

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Pour démarrer et arrêter OSC, initialisez la mémoire de la région partagée CICS en tapant `osctdlinit OSCOIVP1` à l’invite de commandes.

10. Exécutez `oscboot` à OSC de démarrage. La sortie ressemble à ceci :

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Pour vérifier que l’état du processus est prêt, utilisez le `tmadmin` commande dans si. Tous les processus doivent afficher RDY dans le **état** colonne.

    ![Processus d’affichage RDY](media/tmadmin-02.png)

12. Arrêter OSC à l’aide du `oscdown` commande.

## <a name="install-jeus"></a>Installer JEUS

JEUS (Solution de Java Enterprise utilisateur) fournit la couche de présentation du serveur d’applications web OpenFrame.

Avant d’installer JEUS, installez le package Apache Ant, qui fournit les bibliothèques et les outils de ligne de commande nécessaires à l’installation JEUS.

**Pour installer Apache Ant**

1. Téléchargement Ant binaire via la `wget` commande. Exemple :

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Utilisez la `tar` utilitaire pour extraire le fichier binaire et déplacez-le vers un emplacement approprié. Exemple :

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Pour plus d’efficacité, créez un lien symbolique :

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Ouvrez le profil de bash dans vi (`vi .bash_profile`) et de mises à jour les variables suivantes :

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Appliquer la variable d’environnement modifiée. Exemple :

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Pour installer JEUS**

1. Développez le programme d’installation à l’aide de la `tar` utilitaire. Exemple :

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Créer un **jeus** dossier (`mkdir jeus7`) et décompressez le fichier binaire.
3. Remplacez par le **le programme d’installation** répertoire (ou utilisez le paramètre JEUS pour votre propre environnement). Exemple :

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Exécutez `ant clean-all` avant d’effectuer la build. La sortie ressemble à ceci :

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Effectuez une sauvegarde du fichier config-domaine-template.properties. Exemple :

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Ouvrez le fichier de domaine-config-template.properties VI :

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Remplacez `jeus.password=jeusadmin nodename=Tmaxsoft` par `jeus.password=tmax1234 nodename=ofdemo` :

8. Exécuter le `ant install` commande pour générer JEUS.
9.  Mettre à jour le .bash\_fichier de profil avec les variables JEUS comme indiqué :

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Exécuter le profil de l’interpréteur de commandes. Exemple :

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Facultative*. Créer un alias pour le simple arrêt et démarrage des composants JEUS :

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Pour vérifier l’installation, démarrez le serveur d’administration de domaine comme indiqué :

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Vérifiez en utilisant la syntaxe d’ouverture de session web :

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Par exemple, <http://192.168.92.133:9736/webadmin/login.> l’écran d’ouverture de session s’affiche :
    
     ![Écran d’ouverture de session JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Si vous rencontrez des problèmes avec la sécurité de port, ouvrez le port 9736 MSSQLServer ou désactiver le pare-feu (`systemctl stop firewall`).

14. Pour modifier le nom d’hôte pour Serveur1, cliquez sur **verrouiller & modifier**, puis cliquez sur **server1**. Dans la fenêtre du serveur, modifiez le nom d’hôte comme suit :

    1.  Modification **Nodename** à **ofdemo**.
    2.  Cliquez sur **OK** sur le côté droit de la fenêtre.
    3.  Cliquez sur **appliquer les modifications** sur la partie inférieure gauche de la fenêtre et pour la description, entrez *changement de nom d’hôte*.

    ![Écran de JEUS WebAdmin](media/jeus-02.png)

15. Vérifiez que la configuration a réussi dans l’écran de confirmation.

    ![écran de serveur jeus_domain](media/jeus-03.png)

16. Démarrez le processus de serveur géré « server1 » à l’aide de la commande suivante :

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installer OFGW

OFGW est la passerelle OpenFrame qui prend en charge la communication entre l’émulateur de terminal 3270 et la base OSI et gère les sessions entre l’émulateur de terminal et OSI.

**Pour installer OFGW**

1. Assurez-vous que JEUS a été installé avec succès, puis vérifiez que le OFGW7\_0\_1\_fichier du programme d’installation Generic.bin est présent.
2. Exécutez le programme d’installation. Exemple :

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Pour les invites correspondantes, utilisez les emplacements suivants :
     -   Répertoire de base du JEUS
     -   Nom de domaine JEUS
     -   Nom du serveur JEUS
     -   Pilote de Tibero
     -   ID de nœud tMax ofdemo

4. Acceptez le reste des valeurs par défaut, puis appuyez sur ENTRÉE pour quitter le programme d’installation.

5. Vérifiez que l’URL pour OFGW fonctionne comme prévu :

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     L’écran suivant apparaît :

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installer OFManager

OFManager fournit opération et les fonctions de gestion pour OpenFrame dans l’environnement web.

**Pour installer OFManager**

1. Vérifiez que le OFManager7\_fichier du programme d’installation Generic.bin est présent.
2. Exécutez le programme d’installation. Exemple :

     ```
     OFManager7_Generic.bin
     ```

3.  Appuyez sur ENTRÉE pour continuer, puis acceptez le contrat de licence.
4.  Choisissez le dossier d’installation.
5.  Acceptez les valeurs par défaut.
6.  Choisissez Tibero en tant que la base de données.
7.  Appuyez sur ENTRÉE pour quitter le programme d’installation.
8.  Vérifiez que l’URL pour OFManager fonctionne comme prévu :

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

L’écran d’accueil s’affiche :

![Écran d’ouverture de session tMax OpenFrame Manager](media/ofmanager-01.png)

L’installation des composants OpenFrame se termine.

## <a name="next-steps"></a>Étapes suivantes

Si vous envisagez une migration mainframe, notre écosystème de partenaires en expansion est disponible pour vous aider. Pour obtenir des conseils détaillés sur le choix d’une solution de partenaire, consultez [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Prise en main d’Azure](https://docs.microsoft.com/azure/)
-   [Documentation Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guide de Lift-and-Shift de centre de données virtuel Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
