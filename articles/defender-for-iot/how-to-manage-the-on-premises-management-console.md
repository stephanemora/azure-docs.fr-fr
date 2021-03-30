---
title: Gérer la console de gestion locale
description: Découvrez les options de la console de gestion locale, telles que la sauvegarde et la restauration, la définition du nom d’hôte et la configuration d’un proxy pour les capteurs.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 871c74eee4b74538a8a09188953916ff7376bc8d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781719"
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

- autoriser la validation entre la console de gestion et les capteurs connectés, et entre une console de gestion et une console de gestion haute disponibilité. La validation est évaluée par rapport à une liste de révocation de certificats et à la date d’expiration du certificat. *En cas d’échec de la validation, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console*. Cette option est activée par défaut après l’installation.

Les règles de transfert à des tiers ne sont pas validées. Exemples : informations d’alerte envoyées à Syslog, Splunk ou ServiceNow ; et la communication avec Active Directory.

#### <a name="ssl-certificates"></a>Certificats SSL

Le capteur Defender pour IoT et la console de gestion locale utilisent SSL et des certificats TLS pour les fonctions suivantes : 

 - sécuriser les communications entre les utilisateurs et la console web de l’appliance ; 
 
 - sécuriser les communications avec l’API REST sur le capteur et la console de gestion locale ;
 
 - sécuriser les communications entre les capteurs et une console de gestion locale. 

Une fois installée, l’appliance génère un certificat auto-signé local pour permettre l’accès préliminaire à la console web. Les certificats SSL et TLS d’entreprise peuvent être installés à l’aide de l’outil de ligne de commande [`cyberx-xsense-certificate-import`](#cli-commands). 

 > [!NOTE]
 > Pour les intégrations et les règles de transfert où l’appliance est le client et l’initiateur de la session, des certificats spécifiques sont utilisés et ne sont pas associés aux certificats du système.  
 >
 >Dans ces cas, les certificats sont généralement reçus du serveur ou utilisent le chiffrement asymétrique lorsqu’un certificat spécifique est fourni pour configurer l’intégration. 

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
  > Nous vous déconseillons d’utiliser des certificats auto-signés. Cette connexion n’est pas sécurisée et doit être utilisée uniquement pour les environnements de test. Étant donné que le propriétaire du certificat ne peut pas être validé et que la sécurité de votre système ne peut pas être maintenue, les certificats auto-signés ne doivent jamais être utilisés pour les réseaux de production.

### <a name="supported-ssl-certificates"></a>Certificats SSL pris en charge 

Les paramètres suivants sont pris en charge : 

**CRT de certificat**

- Le fichier de certificat principal pour votre nom de domaine

- Algorithme de signature = SHA256RSA
- Algorithme de hachage de signature = SHA256
- Valide à partir du = date passée valide
- Valide jusqu’au = date future valide
- Clé publique = RSA 2048 bits (minimum) ou 4096 bits
- Point de distribution de liste de révocation de certificats = URL vers le fichier .crl
- CN de l’objet = URL, peut être un certificat avec caractères génériques, par exemple, Sensor.contoso.<span>com ou *.contoso.<span>com
- Pays (C) de l’objet = défini, par exemple, US
- Unité d’organisation (OU) de l’objet = définie, par exemple, Contoso Labs
- Organisation (O) de l’objet = définie, par exemple, Contoso Inc

**Fichier de clé**

- Fichier de clé généré lors de la création du CSR

- RSA 2048 bits (minimum) ou 4096 bits

 > [!Note]
 > Utilisation d’une longueur de clé de 4096 bits :
 > - L’établissement d'une liaison SSL au début de chaque connexion est plus lente.  
 > - L’utilisation de l’UC augmente pendant l’établissement de liaisons. 

**Chaîne d’approbation**

- Fichier de certificat intermédiaire (le cas échéant) qui a été fourni par votre autorité de certification.

- Le certificat de l’autorité de certification qui a émis le certificat du serveur doit apparaître en premier dans le fichier, suivi de tous les autres éléments jusqu’à la racine. 
- La chaîne peut inclure des attributs de conteneur.

**Phrase secrète**

- Une clé est prise en charge.

- Configurez-la lorsque vous importez le certificat.

Les certificats avec d’autres paramètres peuvent fonctionner, mais Microsoft ne les prend pas en charge.

#### <a name="encryption-key-artifacts"></a>Artefacts de clé de chiffrement

**.pem : fichier de conteneur de certificat**

Les fichiers Privacy Enhanced Mail (PEM) étaient le type de fichier général utilisé pour sécuriser les e-mails. De nos jours, les fichiers PEM sont utilisés avec des certificats et utilisent des clés x509 ASN1.  

Le fichier conteneur est défini dans les normes RFC 1421 à 1424, un format de conteneur qui peut inclure le certificat public uniquement. Par exemple, Apache installe un certificat d’autorité de certification, des fichiers, des ETC, des SSL ou des CERTS. Il peut s’agir d’une chaîne de certificats entière incluant une clé publique, une clé privée et des certificats racines.  

Il peut également encoder un CSR, car le format PKCS10 peut être traduit en PEM.

**.cert .cer .crt : fichier de conteneur de certificat**

Fichier au format `.pem` ou `.der` portant une autre extension. Le fichier est reconnu par l’Explorateur Windows comme un certificat. Le fichier `.pem` n’est pas reconnu par l’Explorateur Windows.

**.key : fichier de clé privée**

Un fichier de clé est au même format qu’un fichier PEM, mais son extension est différente. 

#### <a name="additional-commonly-available-key-artifacts"></a>Autres artefacts de clé couramment disponibles

**.csr : demande de signature de certificat**  

Ce fichier est utilisé pour l’envoi aux autorités de certification. Le format réel est PKCS10, qui est défini dans la norme RFC 2986 et peut inclure tout ou partie des détails de la clé du certificat demandé. Par exemple, l’objet, l’organisation et l’état. Il s’agit de la clé publique du certificat qui est signée par l’autorité de certification et reçoit un certificat en retour.  

Le certificat retourné est le certificat public, qui comprend la clé publique, mais pas la clé privée. 

**.pkcs12 .pfx .p12 : conteneur de mots de passe** 

Initialement définie par RSA dans les normes de chiffrement à clé publique (PKCS), la variante 12 a été améliorée par Microsoft, puis soumise sous la norme RFC 7292.  

Ce format de conteneur requiert un mot de passe qui contient des paires de certificats publics et privés. Contrairement aux fichiers `.pem` , ce conteneur est entièrement chiffré.  

Vous pouvez utiliser OpenSSL pour convertir le fichier en fichier `.pem` avec des clés publiques et privées :  `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`.  

**.der : PEM à codage binaire**

Pour encoder la syntaxe ASN.1 en binaire, vous pouvez utiliser un fichier `.pem` , qui est simplement un fichier `.der` encodé en base64. 

OpenSSL peut convertir ces fichiers en `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem`.  

Windows reconnaîtra ces fichiers comme des fichiers de certificat. Par défaut, Windows exporte les certificats sous forme de fichiers `.der` avec une extension différente.

**.crl : liste de révocation des certificats**  

Les autorités de certification les produisent comme un moyen d’annuler l’autorisation des certificats avant leur expiration. 

#### <a name="cli-commands"></a>Commandes CLI

Utilisez la commande CLI `cyberx-xsense-certificate-import` pour importer des certificats. Pour utiliser cet outil, vous devez charger des fichiers de certificat sur l’appareil à l’aide d’outils tels que WinSCP ou Wget.

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

### <a name="use-openssl-to-manage-certificates"></a>Utiliser OpenSSL pour gérer les certificats

Gérez vos certificats à l’aide des commandes suivantes :

| Description | Commande CLI |
|--|--|
| Générer une nouvelle clé privée et une demande de signature de certificat | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Générer un certificat auto-signé | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Générer une demande de signature de certificat (CSR) pour une clé privée existante | `openssl req -out CSR.csr -key privateKey.key -new` |
| Générer une demande de signature de certificat à partir d’un certificat existant | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Supprimer une phrase secrète d’une clé privée | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Si vous devez vérifier les informations dans un certificat, une CSR ou une clé privée, utilisez ces commandes :

| Description | Commande CLI |
|--|--|
| Vérifier une demande de signature de certificat (CSR) | `openssl req -text -noout -verify -in CSR.csr` |
| Vérifier une clé privée | `openssl rsa -in privateKey.key -check` |
| Vérifier un certificat | `openssl x509 -in certificate.crt -text -noout`  |

Si vous recevez une erreur indiquant que la clé privée ne correspond pas au certificat ou qu’un certificat que vous avez installé sur un site n’est pas approuvé, utilisez ces commandes pour corriger l’erreur :

| Description | Commande CLI |
|--|--|
| Vérifier un hachage MD5 de la clé publique pour s’assurer qu’il correspond à ce qui se trouve dans une CSR ou une clé privée | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Pour convertir des certificats et des clés dans différents formats afin de les rendre compatibles avec des types spécifiques de serveurs ou de logiciels, utilisez ces commandes :

| Description | Commande CLI |
|--|--|
| Convertir un fichier DER (.crt .cer .der) en PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| Convertir un fichier PEM en DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Convertir un fichier PKCS#12 (.pfx .p12) contenant une clé privée et des certificats en PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />Vous pouvez ajouter `-nocerts` pour générer la privée uniquement ou ajouter `-nokeys` pour générer des certificats uniquement. |
| Convertir un fichier de certificat PEM et une clé privée en PKCS#12 (.pfx .p12) | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

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

## <a name="update-the-software-version"></a>Mettre à jour la version du logiciel

La procédure suivante décrit la mise à jour de la version du logiciel de la console de gestion locale. Le processus de mise à jour prend environ 30 minutes.

1. Accédez au [portail Azure](https://portal.azure.com/).

1. Accédez à Defender pour IoT.

1. Accédez à la page **Mises à jour**.

1. Sélectionnez une version dans la section relative à la console de gestion locale.

1. Sélectionnez **Télécharger** et enregistrez le fichier.

1. Connectez-vous à la console de gestion locale et sélectionnez **Paramètres système** dans le menu latéral.

1. Dans le volet **Mise à jour de la version**, sélectionnez **Mettre à jour**.

1. Sélectionnez le fichier que vous avez téléchargé à partir de la page **Mises à jour** de Defender pour IoT.

## <a name="mail-server-settings"></a>Paramètres du serveur de messagerie

Définissez les paramètres du serveur de messagerie SMTP pour la console de gestion locale.

Pour les définir :

1. Connectez-vous à l’interface CLI destinée à la gestion locale avec les informations d’identification d’administration.
1. Tapez ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Sélectionnez Enter (Entrer). Les invites suivantes s’affichent.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. Entrez le nom du serveur SMTP et l’expéditeur, puis sélectionnez Entrée.

## <a name="see-also"></a>Voir aussi

[Gérer les capteurs depuis la console de gestion](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Gérer des capteurs individuels](how-to-manage-individual-sensors.md)
