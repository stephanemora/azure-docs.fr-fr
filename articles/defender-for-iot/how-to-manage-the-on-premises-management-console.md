---
title: Gérer la console de gestion locale
description: Découvrez les options de la console de gestion locale, telles que la sauvegarde et la restauration, la définition du nom d’hôte et la configuration d’un proxy pour les capteurs.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 34efef4a01b58cc26fd1567336184837a703ade2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835567"
---
# <a name="manage-the-on-premises-management-console"></a>Gérer la console de gestion locale

Cet article traite des options de la console de gestion locale, telles que la sauvegarde et la restauration, le téléchargement du fichier d’activation des appareils validés, la mise à jour des certificats et la configuration d’un proxy pour les capteurs.

Vous intégrez la console de gestion locale à partir du portail Azure.

## <a name="upload-an-activation-file"></a>Charger un fichier d’activation

Quand vous vous connectez pour la première fois, un fichier d’activation pour la console de gestion locale est téléchargé. Ce fichier contient l’ensemble des appareils validés qui sont définis pendant le processus d’intégration. La liste comprend les capteurs associés à plusieurs abonnements.

Après l’activation initiale, le nombre d’appareils analysés peut dépasser le nombre d’appareils validés définis lors de l’intégration. Cet événement peut se produire, par exemple, si vous connectez d’autres capteurs à la console de gestion. S’il y a une différence entre le nombre d’appareils analysés et le nombre d’appareils validés, un avertissement s’affiche dans la console de gestion. Si cet événement se produit, vous devez charger un nouveau fichier d’activation.

Pour charger un fichier d’activation :

1. Accédez à la page **Tarification** d’Azure Defender pour IoT.
1. Sélectionnez l’onglet **Télécharger le fichier d’activation de la console de gestion**. Le fichier d’activation est téléchargé.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Télécharger le fichier d’activation.":::

1. Sélectionnez **Paramètres système** à partir de la console de gestion.
1. Sélectionnez **Activation**.
1. Sélectionnez **Choisir un fichier** et sélectionnez le fichier que vous avez enregistré.

## <a name="manage-certificates"></a>Gérer des certificats

Après l’installation de la console de gestion locale, un certificat auto-signé local est généré et utilisé pour accéder à l’application web de la console de gestion. Lorsque les utilisateurs Administrateurs se connectent à la console de gestion pour la première fois, ils sont invités à fournir un certificat SSL/TLS. Pour plus d’informations sur la configuration initiale, consultez [Activation et configuration de la console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md).

Les sections suivantes fournissent des informations sur la mise à jour des certificats, l’utilisation des commandes CLI de certificat et les certificats et paramètres de certificat pris en charge.

### <a name="about-certificates"></a>À propos des certificats

Azure Defender pour IoT utilise des certificats SSL et TLS pour :

- répondre aux exigences de chiffrement et de certificat spécifiques requises par votre organisation en téléchargeant le certificat signé par une autorité de certification ;

- autoriser la validation entre la console de gestion et les capteurs connectés, et entre une console de gestion et une console de gestion haute disponibilité. La validation est évaluée par rapport à une liste de révocation de certificats et à la date d’expiration du certificat. *En cas d’échec de la validation, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console.* Cette option est activée par défaut après l’installation.

Les règles de transfert à des tiers ne sont pas validées. Exemples : informations d’alerte envoyées à Syslog, Splunk ou ServiceNow ; et la communication avec Active Directory.

### <a name="update-certificates"></a>Mettre à jour des certificats

Les utilisateurs Administrateurs de la console de gestion locale peuvent mettre à jour des certificats.

Pour mettre à jour un certificat :  

1. Sélectionnez **Paramètres système**.
1. Sélectionnez **Certificats SSL/TLS**.
1. Supprimez ou modifiez le certificat et ajoutez-en un nouveau.
   
   - Ajoutez un nom de certificat.
   - Chargez un fichier CRT et un fichier de clé, puis entrez une phrase secrète.
   - Chargez un fichier PEM si nécessaire.

Pour modifier le paramètre de validation :

1. Activez ou désactivez le bouton bascule **Activer la validation du certificat**.
1. Sélectionnez **Enregistrer**.

Si cette option est activée et que la validation échoue, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console.

### <a name="certificate-support"></a>Prise en charge des certificats

Les certificats suivants sont pris en charge :

- Infrastructure à clé privée et d’entreprise (PKI privée) 
- Infrastructure à clé publique (PKI publique) 
- Généré localement sur l’appliance (auto-signé localement) 

  > [!IMPORTANT]
  > Nous vous déconseillons d’utiliser des certificats auto-signés. Cette connexion n’est pas sécurisée et doit être utilisée uniquement pour les environnements de test. Le propriétaire du certificat ne peut pas être validé et la sécurité de votre système ne peut pas être assurée. N’utilisez pas de certificat auto-signé pour des réseaux de production.  

Les paramètres suivants sont pris en charge : 

**CRT de certificat**

- Le fichier de certificat principal pour votre nom de domaine
- Algorithme de signature = SHA256RSA
- Algorithme de hachage de signature = SHA256
- Valide à partir du = date passée valide
- Valide jusqu’au = date future valide
- Clé publique = RSA 2048 bits (minimum) ou 4096 bits
- Point de distribution de liste de révocation de certificats = URL vers le fichier .crl
- CN de l’objet = URL, peut être un certificat avec caractères génériques, par exemple, www.contoso.com ou \*.contoso.com
- Pays (C) de l’objet = défini, par exemple, US
- Unité d’organisation (OU) de l’objet = définie, par exemple, Contoso Labs
- Organisation (O) de l’objet = définie, par exemple, Contoso Inc

**Fichier de clé**

- Fichier de clé généré lors de la création du CSR
- RSA 2048 bits (minimum) ou 4096 bits

**Chaîne d’approbation**

- Fichier de certificat intermédiaire (le cas échéant) qui a été fourni par votre autorité de certification.
- Le certificat de l’autorité de certification qui a émis le certificat du serveur doit apparaître en premier dans le fichier, suivi de tous les autres éléments jusqu’à la racine. 
- La chaîne peut inclure des attributs de conteneur.

**Passphrase**

- Une clé est prise en charge.
- Configurez-la lorsque vous importez le certificat.

Les certificats avec d’autres paramètres peuvent fonctionner, mais Microsoft ne les prend pas en charge.

#### <a name="encryption-key-artifacts"></a>Artefacts de clé de chiffrement

**.pem : fichier de conteneur de certificat**

Le nom provient de Privacy Enhanced Mail (PEM), une méthode historique de sécurisation du courrier électronique. Le format de conteneur est une traduction en base64 des clés ASN.1 x509.  

Ce fichier est défini dans les RFC 1421 à 1424 : un format de conteneur qui peut inclure uniquement le certificat public (par exemple, les installations Apache, les fichiers de certificat d’autorité de certification et les certificats ETC SSL). Il peut également inclure une chaîne de certificats entière, comprenant une clé publique, une clé privée et des certificats racines.  

Il peut également encoder un CSR, car le format PKCS10 peut être traduit en PEM.

**.cert .cer .crt : fichier de conteneur de certificat**

Il s’agit d’un fichier formaté en .pem (ou plus rarement .der) avec une extension différente. Explorateur de fichiers Windows le reconnaît comme un certificat. Explorateur de fichiers ne reconnaît pas le fichier .pem.

**.key : fichier de clé privée**

Un fichier de clé est au même format qu’un fichier PEM, mais son extension est différente. 

#### <a name="cli-commands"></a>Commandes CLI

Utilisez la commande CLI `cyberx-xsense-certificate-import` pour importer des certificats. Pour utiliser cet outil, vous devez charger des fichiers de certificat sur l’appareil (à l’aide d’outils tels que WinSCP ou wget).

La commande prend en charge les indicateurs d’entrée suivants :

- `-h` :  Affiche la syntaxe d’aide de la ligne de commande.

- `--crt` :  Chemin d’accès au fichier de certificat (extension .crt).

- `--key`: Fichier \*.key. La longueur de la clé doit être de 2 048 bits minimum.

- `--chain` :  Chemin d’accès à un fichier de chaîne de certificat (facultatif).

- `--pass` :  Phrase secrète utilisée pour chiffrer le certificat (facultatif).

- `--passphrase-set` :  Valeur par défaut = `False`, inutilisé. Définir sur `True` pour utiliser la phrase secrète précédente fournie avec le certificat précédent (facultatif).

Quand vous utilisez la commande CLI :

- Vérifiez que les fichiers de certificat sont lisibles sur l’appliance.

- Vérifiez que le nom de domaine et l’adresse IP du certificat correspondent à la configuration planifiée par le service informatique.

## <a name="define-backup-and-restore-settings"></a>Définir les paramètres de sauvegarde et de restauration

La sauvegarde du système de la console de gestion locale est effectuée automatiquement et quotidiennement. Les données sont enregistrées sur un autre disque. L’emplacement par défaut est `/var/cyberx/backups`. 

Vous pouvez transférer automatiquement ce fichier vers le réseau interne. 

> [!NOTE]
> Vous pouvez effectuer la procédure de sauvegarde et de restauration uniquement sur la même version. 

Pour sauvegarder l’ordinateur de la console de gestion locale : 

- Connectez-vous à un compte d’administrateur et entrez `sudo cyberx-management-backup -full`.

Pour restaurer le dernier fichier de sauvegarde :

- Connectez-vous à un compte d’administrateur et entrez `$ sudo cyberx-management-system-restore`.

Pour enregistrer la sauvegarde sur un serveur SMB externe :

1. Créez un dossier partagé sur le serveur SMB externe.  

   Obtenez le chemin d’accès au dossier, le nom d’utilisateur et le mot de passe requis pour accéder au serveur SMB. 

2. Dans Defender pour IoT, créez un répertoire pour les sauvegardes :

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Modifiez fstab :  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Modifiez ou créez les informations d’identification pour le serveur SMB à partager : 

   - `sudo nano /etc/samba/user` 

5. Ajoutez : 

   - `username=<user name>`

   - `password=<password>` 

6. Montez le répertoire : 

   - `sudo mount -a` 

7. Configurez un répertoire de sauvegarde dans le dossier partagé de la console de gestion locale Defender pour IoT :  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Modifier le nom d’hôte

Pour modifier le nom d’hôte de la console de gestion configuré dans le serveur DNS de l’organisation :

1. Dans le volet gauche de la console de gestion, sélectionnez **Paramètres système**.  

2. Dans la section de mise en réseau de la console, sélectionnez **Réseau**. 

3. Entrez le nom d’hôte configuré dans le serveur DNS de l’organisation. 

4. Sélectionnez **Enregistrer**.

## <a name="define-vlan-names"></a>Définir des noms de VLAN

Les noms de VLAN ne sont pas synchronisés entre le capteur et la console de gestion. Vous devez définir des noms identiques sur les composants.

Dans la zone mise en réseau, sélectionnez **VLAN** et ajoutez des noms aux ID de VLAN détectés. Ensuite, sélectionnez **Enregistrer**.

## <a name="define-a-proxy-to-sensors"></a>Définir un proxy pour les capteurs

Renforcez la sécurité du système en empêchant les utilisateurs de se connecter directement au capteur. Au lieu de cela, utilisez le tunneling de proxy pour permettre aux utilisateurs d’accéder au capteur à partir de la console de gestion locale avec une seule règle de pare-feu. Cette amélioration réduit le risque d’accès non autorisé à l’environnement réseau au-delà du capteur.

Utilisez un proxy dans les environnements où il n’existe aucune connectivité directe aux capteurs.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Utilisateur.":::

La procédure suivante connecte un capteur à la console de gestion locale et active le tunneling sur celle-ci :

1. Connectez-vous à l’interface de commande de l’appliance de console de gestion locale avec les informations d’identification d’administration.

2. Entrez `sudo cyberx-management-tunnel-enable`, puis sélectionnez **Entrée**.

4. Entrez `--port 10000`, puis sélectionnez **Entrée**.

## <a name="adjust-system-properties"></a>Ajuster les propriétés du système

Les propriétés système contrôlent plusieurs opérations et paramètres dans la console de gestion. Leur modification peut mettre à mal le fonctionnement de la console de gestion. Contactez [Support Microsoft](https://support.microsoft.com) avant de modifier vos paramètres.

Pour accéder aux propriétés système : 

1. Connectez-vous à la console de gestion locale ou au capteur.

2. Sélectionnez **Paramètres système**.

3. Sélectionnez **Propriétés système** dans la section **Général**.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Modifier le nom de la console de gestion locale

Vous pouvez modifier le nom de la console de gestion locale. Le nouveau nom apparaît dans le navigateur web de la console, dans les différentes fenêtres de la console et dans les journaux de dépannage. Le nom par défaut est **management console**.

Pour modifier le nom :

1. En bas du volet gauche, sélectionnez le nom actuel.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Capture d’écran de la version de la console de gestion locale.":::

2. Dans la boîte de dialogue **Modifier la configuration de la console de gestion**, entrez le nouveau nom. Le nom ne peut pas dépasser 25 caractères.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Capture d’écran de la modification de la configuration de la plateforme Defender pour IoT.":::

3. Sélectionnez **Enregistrer**. Le nouveau nom est appliqué.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Capture d’écran montrant le nom modifié de la console.":::

## <a name="password-recovery"></a>Récupération de mot de passe

La récupération du mot de passe pour votre console de gestion locale est liée à l’abonnement auquel l’appareil est rattaché. Vous ne pouvez pas récupérer un mot de passe si vous ne savez pas à quel abonnement un appareil est rattaché.

Pour réinitialiser votre mot de passe :

1. Accédez à la page de connexion de la console de gestion locale.
1. Sélectionnez **Récupération du mot de passe**.
1. Copiez l’identificateur unique.
1. Accédez à la page **Sites et capteurs** de Defender pour IoT et sélectionnez l’onglet **Récupérer mon mot de passe**.
1. Entrez l’identificateur unique et sélectionnez **Récupérer**. Le fichier d’activation est téléchargé.
1. Accédez à la page **Récupération du mot de passe** et chargez le fichier d’activation.
1. Sélectionnez **Suivant**.
 
   Vous recevez maintenant votre nom d’utilisateur et un nouveau mot de passe généré par le système.

> [!NOTE]
> Le capteur est lié à l’abonnement auquel il était connecté à l’origine. Vous pouvez récupérer le mot de passe uniquement en utilisant le même abonnement que celui auquel il est rattaché.

## <a name="see-also"></a>Voir aussi

[Gérer les capteurs depuis la console de gestion](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Gérer des capteurs individuels](how-to-manage-individual-sensors.md)
