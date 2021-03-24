---
title: Gérer des capteurs individuels
description: Découvrez comment gérer des capteurs individuels, y compris comment gérer les fichiers d’activation, exécuter des sauvegardes et mettre à jour un capteur autonome.
ms.date: 02/02/2021
ms.topic: how-to
ms.openlocfilehash: df1d1dd6ca2085fb3ab12c104723c63e32249403
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781957"
---
# <a name="manage-individual-sensors"></a>Gérer des capteurs individuels

Cet article explique comment gérer des capteurs individuels. Les tâches incluent la gestion des fichiers d’activation, l’exécution de sauvegardes et la mise à jour d’un capteur autonome.

Vous pouvez également effectuer certaines tâches de gestion de capteur à partir de la console de gestion locale, sur laquelle vous pouvez gérer plusieurs capteurs simultanément.

Le portail Azure est utilisé pour l’intégration et l’inscription du capteur.

## <a name="manage-sensor-activation-files"></a>Gérer les fichiers d’activation de capteur

Votre capteur a été intégré à Azure Defender pour IoT à partir du portail Azure. Chaque capteur a été intégré en tant que capteur connecté localement ou en tant que capteur connecté au cloud.

Un fichier d’activation unique est chargé sur chaque capteur que vous déployez. Pour plus d’informations sur le moment auquel utiliser un nouveau fichier et la manière de le faire, consultez [Télécharger de nouveaux fichiers d’activation](#upload-new-activation-files). Si vous ne pouvez pas charger le fichier, consultez [Résoudre les problèmes de chargement des fichiers d’activation](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>À propos des fichiers d’activation pour les capteurs connectés localement

Les capteurs connectés localement sont associés à un abonnement Azure. Le fichier d’activation de vos capteurs connectés localement contient une date d’expiration. Un mois avant cette date, un message d’avertissement s’affiche en haut de la console de capteur. L’avertissement reste jusqu’à ce que vous ayez mis à jour le fichier d’activation.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Capture d’écran des paramètres du système.":::

Vous pouvez continuer à utiliser les fonctionnalités Defender pour IoT même si le fichier d’activation a expiré. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>À propos des fichiers d’activation pour les capteurs connectés au cloud

Les capteurs connectés au cloud sont associés au hub Defender pour IoT. Ces capteurs ne sont pas limités par des périodes de validité du fichier d’activation. Le fichier d’activation pour les capteurs connectés au cloud est utilisé pour garantir la connexion au hub Defender pour IoT.

### <a name="upload-new-activation-files"></a>Charger de nouveaux fichiers d’activation

Dans les cas suivants, il se peut que vous deviez charger un nouveau fichier d’activation pour un capteur intégré :

- Un fichier d’activation expire sur un capteur connecté localement. 

- Vous souhaitez utiliser un autre mode de gestion de capteur. 

- Vous souhaitez affecter un nouveau hub Defender pour IoT à un capteur connecté au cloud.

Pour ajouter un nouveau fichier d’activation :

1. Accédez à la page **Gestion du capteur**.

2. Sélectionnez le capteur pour lequel vous souhaitez télécharger un nouveau fichier d’activation.

3. Supprimez-la.

4. Intégrez à nouveau le capteur à partir de la page **Intégration** dans le nouveau mode ou avec un nouveau hub Defender pour IoT.

5. Téléchargez le fichier d’activation à partir de la page **Télécharger le fichier d’activation**.

6. Enregistrez le fichier .

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Téléchargez le fichier d’activation à partir du hub Defender pour IoT.":::

7. Connectez-vous à la console de capteur Defender pour IoT.

8. Dans la console du capteur, sélectionnez **Paramètres système** > **Réactivation**.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Sélectionnez la réactivation dans l’écran Paramètres système.":::

9. Sélectionnez **Charger** et choisissez le fichier que vous avez enregistré.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Chargez le fichier que vous avez enregistré.":::

10. Sélectionnez **Activer**.

### <a name="troubleshoot-activation-file-upload"></a>Résoudre les problèmes de chargement des fichiers d’activation

Vous recevrez un message d’erreur si le chargement du fichier d’activation a échoué. Les événements suivants peuvent s’être produits :

- **Pour les capteurs connectés localement** : Le fichier d’activation n’est pas valide. Si le fichier n’est pas valide, accédez au portail Defender pour IoT. Sur la page **Gestion du capteur**, sélectionnez le capteur contenant le fichier non valide, puis téléchargez un nouveau fichier d’activation.

- **Pour les capteurs connectés au cloud** : Le capteur ne peut pas se connecter à Internet. Vérifiez la configuration du réseau du capteur. Si votre capteur doit se connecter par le biais d’un proxy web pour accéder à Internet, vérifiez que votre serveur proxy est correctement configuré sur l’écran **Configuration réseau du capteur**. Vérifiez que \*.azure-devices.net:443 est autorisé dans le pare-feu et/ou le proxy. Si les caractères génériques ne sont pas pris en charge ou si vous souhaitez davantage de contrôle, le nom de domaine complet pour votre hub Defender pour IoT spécifique doit être ouvert dans votre pare-feu et/ou proxy. Pour plus d’informations, consultez [Référence - Points de terminaison IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md).  

- **Pour les capteurs connectés au cloud** : Le fichier d’activation est valide mais Defender pour IoT l’a rejeté. Si vous ne parvenez pas à résoudre ce problème, vous pouvez télécharger un autre fichier d’activation à partir de la page Sites et capteurs du portail Defender pour IoT. Si cela ne fonctionne pas, contactez le support Microsoft.

## <a name="manage-certificates"></a>Gérer des certificats

Après l’installation du capteur, un certificat auto-signé local est généré et utilisé pour accéder à l’application web du capteur. Lorsqu’ils se connectent au capteur pour la première fois, les utilisateurs administrateurs sont invités à fournir un certificat SSL/TLS.  Pour plus d’informations sur la première configuration, consultez [Se connecter et activer un capteur](how-to-activate-and-set-up-your-sensor.md).

Cet article fournit des informations sur la mise à jour des certificats, l’utilisation des commandes CLI de certificat et les certificats et paramètres de certificat pris en charge.

### <a name="about-certificates"></a>À propos des certificats

Azure Defender pour IoT utilise des certificats SSL/TLS pour :

- répondre aux exigences de chiffrement et de certificat spécifiques requises par votre organisation en téléchargeant le certificat signé par une autorité de certification ;

- autoriser la validation entre la console de gestion et les capteurs connectés, et entre une console de gestion et une console de gestion haute disponibilité. Les validations sont évaluées par rapport à une liste de révocation de certificats, ainsi que par rapport à la date d’expiration du certificat. *En cas d’échec de la validation, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console*. Cette option est activée par défaut après l’installation.

 Les règles de transfert tierces, par exemple les informations d’alerte envoyées à SYSLOG, Splunk ou ServiceNow, ou les communications avec Active Directory ne sont pas validées.

#### <a name="ssl-certificates"></a>Certificats SSL

Le capteur Defender pour IoT et la console de gestion locale utilisent SSL et des certificats TLS pour les fonctions suivantes : 

 - sécuriser les communications entre les utilisateurs et la console web de l’appliance ; 
 
 - sécuriser les communications avec l’API REST sur le capteur et la console de gestion locale ;
 
 - sécuriser les communications entre les capteurs et une console de gestion locale. 

Une fois installée, l’appliance génère un certificat auto-signé local pour permettre un accès préliminaire à la console web. Les certificats SSL et TLS d’entreprise peuvent être installés à l’aide de l’outil de ligne de commande [`cyberx-xsense-certificate-import`](#cli-commands).

 > [!NOTE]
 > Pour les intégrations et les règles de transfert où l’appliance est le client et l’initiateur de la session, des certificats spécifiques sont utilisés et ne sont pas associés aux certificats du système.  
 >
 >Dans ces cas, les certificats sont généralement reçus du serveur ou utilisent le chiffrement asymétrique lorsqu’un certificat spécifique est fourni pour configurer l’intégration.

Les appliances peuvent utiliser des fichiers de certificat uniques. Si vous devez remplacer un certificat que vous avez chargé :

- À partir de la version 10.0, vous pouvez remplacer le certificat à partir du menu Paramètres système.

- Pour les versions antérieures à la version 10.0, vous pouvez remplacer le certificat SSL à l’aide de l’outil en ligne de commande.

### <a name="update-certificates"></a>Mettre à jour des certificats

Les utilisateurs administrateurs des capteurs peuvent mettre à jour les certificats.

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

Les paramètres suivants sont pris en charge. 

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

**Fichier de clé**.

- Fichier de clé généré lors de la création du CSR

- RSA 2 048 bits (minimum) ou 4 096 bits

 > [!Note]
 > Utilisation d’une longueur de clé de 4 096bits :
 > - L’établissement d'une liaison SSL au début de chaque connexion est plus lente.  
 > - L’utilisation de l’UC augmente pendant l’établissement de liaisons. 

**Chaîne de certificats**

- Fichier de certificat intermédiaire (le cas échéant) qui a été fourni par votre autorité de certification

- Le certificat de l’autorité de certification qui a émis le certificat du serveur doit apparaître en premier dans le fichier, suivi de tous les autres éléments jusqu’à la racine. 
- Peut inclure des attributs de conteneur.

**Phrase secrète**

- Une clé prise en charge

- Configurez-la lorsque vous importez le certificat.

Les certificats avec d’autres paramètres peuvent fonctionner, mais Microsoft ne les prend pas en charge.

#### <a name="encryption-key-artifacts"></a>Artefacts de clé de chiffrement

**.pem : fichier de conteneur de certificat**

Les fichiers Privacy Enhanced Mail (PEM) étaient le type de fichier général utilisé pour sécuriser les e-mails. De nos jours, les fichiers PEM sont utilisés avec des certificats et utilisent des clés x509 ASN1.  

Le fichier conteneur est défini dans les normes RFC 1421 à 1424, un format de conteneur qui peut inclure le certificat public uniquement. Par exemple, Apache installe un certificat d’autorité de certification, des fichiers, des ETC, des SSL ou des CERTS. Il peut s’agir d’une chaîne de certificats entière incluant une clé publique, une clé privée et des certificats racines.  

Il peut également encoder un CSR, car le format PKCS10 peut être traduit en PEM.

**.cert .cer .crt : fichier de conteneur de certificat**

Fichier au format `.pem` ou `.der` portant une autre extension. Le fichier est reconnu par l’Explorateur Windows comme un certificat. Le fichier `.pem` n’est pas reconnu par l’Explorateur Windows.

**.key : fichier de clé privée**

Un fichier de clé est au même format qu’un fichier PEM, mais son extension est différente.

#### <a name="additional-commonly-available-key-artifacts"></a>Autres artefacts de clé couramment disponibles

**.csr : demande de signature de certificat**  

Ce fichier est utilisé pour l’envoi aux autorités de certification. Le format réel est PKCS10, qui est défini dans la norme RFC 2986 et peut inclure tout ou partie des détails de la clé du certificat demandé. Par exemple, l’objet, l’organisation et l’état. Il s’agit de la clé publique du certificat qui est signée par l’autorité de certification et reçoit un certificat en retour.  

Le certificat retourné est le certificat public, qui comprend la clé publique, mais pas la clé privée. 

**.pkcs12 .pfx .p12 : conteneur de mots de passe** 

Initialement définie par RSA dans les normes de chiffrement à clé publique (PKCS), la variante 12 a été améliorée par Microsoft, puis soumise sous la norme RFC 7292.  

Ce format de conteneur requiert un mot de passe qui contient des paires de certificats publics et privés. Contrairement aux fichiers `.pem` , ce conteneur est entièrement chiffré.  

Vous pouvez utiliser OpenSSL pour convertir ceci en fichier `.pem` avec des clés publiques et privées :  `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`.  

**.der : PEM à codage binaire**

Pour encoder la syntaxe ASN.1 en binaire, vous pouvez utiliser un fichier `.pem` , qui est simplement un fichier `.der` encodé en base64. 

OpenSSL peut convertir ces fichiers en `.pem` :  `openssl x509 -inform der -in to-convert.der -out converted.pem`.  

Windows reconnaîtra ces fichiers comme des fichiers de certificat. Par défaut, Windows exporte les certificats sous forme de fichiers `.der` avec une extension différente.  

**.crl : liste de révocation des certificats**  
Les autorités de certification produisent ces fichiers comme un moyen d’annuler l’autorisation des certificats avant leur expiration.
 
##### <a name="cli-commands"></a>Commandes CLI

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

## <a name="connect-a-sensor-to-the-management-console"></a>Connecter un capteur à la console de gestion

Cette section décrit comment garantir la connexion entre le capteur et la console de gestion locale. Vous devez effectuer cette opération si vous travaillez sur un réseau déconnecté et souhaitez envoyer des informations sur les appareils et les alertes à la console de gestion à partir du capteur. Cette connexion permet également à la console de gestion d’envoyer les paramètres système au capteur et d’effectuer d’autres tâches de gestion sur le capteur.

Pour se connecter :

1. Connectez-vous à la console de gestion locale.

2. Sélectionnez **Paramètres système**.

3. Dans la section **Configuration du capteur - Chaîne de connexion**, copiez la chaîne de connexion générée automatiquement.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Copiez la chaîne de connexion à partir de cet écran."::: 

4. Connectez-vous à la console du capteur.

5. Dans le volet gauche, sélectionnez **Paramètres du système**.

6. Sélectionnez **Connexion à la console de gestion**.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Capture d’écran de la boîte de dialogue Connexion à la console de gestion.":::

7. Collez la chaîne de connexion interne dans la zone **Chaîne de connexion**, puis sélectionnez **Se connecter**.

8. Dans la console de gestion locale, dans la fenêtre de **Gestion de site**, affectez le capteur à une zone.

## <a name="change-the-name-of-a-sensor"></a>Modifier le nom d'un capteur

Vous pouvez changer le nom de votre console de capteur. Le nouveau nom apparaît dans le navigateur web de la console, dans les différentes fenêtres de la console et dans les journaux de dépannage.

Le processus de modification du nom des capteurs n’est pas le même pour les capteurs connectés localement et pour les capteurs connectés au cloud. Le nom par défaut est **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Modifier le nom d’un capteur connecté localement

Pour modifier le nom :

1. En bas du volet gauche de la console, sélectionnez l’étiquette du capteur actuel.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Capture d’écran montrant l’étiquette du capteur.":::

1. Dans la boîte de dialogue **Modifier le nom du capteur**, saisissez un nom.

1. Sélectionnez **Enregistrer**. Le nouveau nom est appliqué.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Modifier le nom d’un capteur connecté au cloud

Si votre capteur a été inscrit en tant que capteur connecté au cloud, le nom du capteur est défini par le nom attribué lors de l’inscription. Le nom est inclus dans le fichier d’activation que vous avez chargé lors de votre première connexion. Pour modifier le nom du capteur, vous devez charger un nouveau fichier d’activation.

Pour modifier le nom :

1. Dans le portail Azure Defender pour IoT, accédez à la page Sites et capteurs.

1. Supprimez le capteur de la page Sites et capteurs.

1. Inscrivez-vous avec le nouveau nom en sélectionnant **Capteur intégré** dans la page Prise en main.

1. Téléchargez le nouveau fichier d’activation.

1. Connectez-vous à la console de capteur Defender pour IoT.

1. Dans la console du capteur, sélectionnez **Paramètres système** puis **Réactivation**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Chargez votre fichier d’activation pour réactiver le capteur.":::

1. Sélectionnez **Télécharger** et sélectionnez le fichier que vous avez enregistré.

1. Sélectionnez **Activer**.

## <a name="update-the-sensor-network-configuration"></a>Mettre à jour la configuration réseau du capteur

La configuration réseau du capteur a été définie lors de son installation. Vous pouvez modifier les paramètres de configuration. Vous pouvez également définir une configuration de proxy.

Si vous créez une nouvelle adresse IP, vous devrez peut-être vous reconnecter.

Pour modifier la configuration :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans la fenêtre **Paramètre système**, sélectionnez **Réseau**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Configurez vos paramètres réseau.":::

3. Définissez les paramètres :

    | Paramètre | Description |
    |--|--|
    | Adresse IP | Adresse IP du capteur |
    | Masque de sous-réseau | Adresse du masque |
    | Passerelle par défaut | Adresse de la passerelle par défaut |
    | DNS | Adresse du serveur DNS |
    | HostName | Nom d’hôte du capteur |
    | Proxy | Nom de port et d’hôte proxy |

4. Sélectionnez **Enregistrer**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Synchroniser les fuseaux horaires sur le capteur

Vous pouvez configurer l’heure et la région du capteur afin que tous les utilisateurs voient la même heure et la même région.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Configurez l’heure et la région.":::

| Paramètre | Description |
|--|--|
| Fuseau horaire | Définition du fuseau horaire pour :<br />- les alertes<br />- les widgets de tendances et de statistiques<br />- les rapports d’exploration de données<br />   \- les rapports d’évaluation des risques<br />- les vecteurs d’attaque |
| Format de la date | Sélectionnez l’une des options de format suivantes :<br />- jj/MM/aaaa HH:mm:ss<br />- MM/jj/aaaa HH:mm:ss<br />- aaaa/mm/jj HH:mm:ss |
| Date et heure | Affiche la date et l’heure locale actuelles au format que vous avez sélectionné.<br />Par exemple, si votre emplacement réel est États-Unis et plus précisément New York, mais que le fuseau horaire est défini sur Europe et Berlin, l’heure est affichée en fonction de l’heure locale de Berlin. |

Pour configurer l’heure du capteur :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans la fenêtre **Paramètre système**, sélectionnez **Heure et région**.

3. Définissez les paramètres, puis sélectionnez **Enregistrer**.

## <a name="set-up-backup-and-restore-files"></a>Configurer des fichiers de sauvegarde et de restauration

La sauvegarde du système est effectuée automatiquement chaque jour à 3 h du matin. Les données sont enregistrées sur un autre disque du capteur. L’emplacement par défaut est `/var/cyberx/backups`.

Vous pouvez transférer automatiquement ce fichier vers le réseau interne.

> [!NOTE]
> - La procédure de sauvegarde et de restauration ne peut être effectuée qu’entre deux versions identiques.
> - Dans certaines architectures, la sauvegarde est désactivée. Vous pouvez l’activer dans le fichier `/var/cyberx/properties/backup.properties`.

Lorsque vous contrôlez un capteur à l’aide de la console de gestion locale, vous pouvez utiliser la planification de sauvegarde du capteur pour collecter ces sauvegardes et les stocker dans la console de gestion ou sur un serveur de sauvegarde externe.

**Éléments sauvegardés :** Configurations et données.

**Éléments non sauvegardés :** Fichiers PCAP et journaux. Vous pouvez sauvegarder et restaurer manuellement les fichiers PCAP et les journaux.

Les fichiers de sauvegarde du capteur sont automatiquement nommés au format suivant : `<sensor name>-backup-version-<version>-<date>.tar`. par exemple `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Pour configurer la sauvegarde :

- Connectez-vous à un compte d’administrateur et entrez `$ sudo cyberx-xsense-system-backup`.

Pour restaurer le dernier fichier de sauvegarde :

- Connectez-vous à un compte d’administrateur et entrez `$ sudo cyberx-xsense-system-restore`.

Pour enregistrer la sauvegarde sur un serveur SMB externe :

1. Créez un dossier partagé sur le serveur SMB externe.

    Obtenez le chemin d’accès au dossier, le nom d’utilisateur et le mot de passe requis pour accéder au serveur SMB.

2. Dans le capteur, créez un répertoire pour les sauvegardes :

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Modifiez `fstab`:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Modifiez et créez les informations d’identification à partager pour le serveur SMB :

    `sudo nano /etc/samba/user` 

5. Ajoutez :

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Montez le répertoire :

    `sudo mount -a`

7. Configurez un répertoire de sauvegarde dans le dossier partagé sur le capteur Defender pour IoT :  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Restaurer les capteurs

Vous pouvez restaurer des sauvegardes à partir de la console du capteur et à l’aide de l’interface de ligne de commande.

**Pour effectuer une restauration à partir de la console :**

- Sélectionnez **Restaurer l’image** dans la fenêtre **Paramètres système** du capteur.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Restaurez votre image en sélectionnant le bouton.":::

La console affiche les échecs de restauration.

**Pour effectuer une restauration à l’aide de l’interface de ligne de commande :**

- Connectez-vous à un compte d’administrateur et entrez `$ sudo cyberx-management-system-restore`.

## <a name="update-a-standalone-sensor-version"></a>Mettre à jour une version de capteur autonome

La procédure suivante décrit comment mettre à jour un capteur autonome à l’aide de la console du capteur. Le processus de mise à jour prend environ 30 minutes.

1. Accédez au [portail Azure](https://portal.azure.com/).

2. Accédez à Defender pour IoT.

3. Accédez à la page **Mises à jour**.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Capture d’écran de la page Mises à jour de Defender pour IoT.":::

4. Sélectionnez **Télécharger** dans la section **Capteurs** et enregistrez le fichier.

5. Dans la barre latérale de la console du capteur, sélectionnez **Paramètres système**.

6. Dans le volet **Mise à niveau de la version**, sélectionnez **Mettre à niveau**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Capture d’écran du volet de mise à niveau.":::

7. Sélectionnez le fichier que vous avez téléchargé à partir de la page **Mises à jour** de Defender pour IoT.

8. Le processus de mise à jour démarre. Au cours de celle-ci, le système redémarre à deux reprises. Après le premier redémarrage (avant la fin du processus de mise à jour), le système s’ouvre sur la fenêtre de connexion. Une fois que vous êtes connecté, la version de mise à niveau s’affiche dans la partie inférieure gauche de la barre latérale.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Capture d’écran de la version de mise à niveau qui s’affiche lorsque vous êtes connecté.":::

## <a name="forward-sensor-failure-alerts"></a>Transférer des alertes de défaillance du capteur

Vous pouvez transférer des alertes à des tiers pour fournir des détails sur :

- les capteurs déconnectés

- les échecs de sauvegarde à distance

Ces informations sont envoyées lorsque vous créez une règle de transfert pour les notifications système.

> [!NOTE]
> Les administrateurs peuvent envoyer des notifications système.

Pour envoyer des notifications :

1. Connectez-vous à la console de gestion locale.
1. Sélectionnez **Transfert** dans le menu latéral.
1. Créez une règle de transfert.
1. Sélectionnez **Signaler les notifications système**.

Pour plus d’informations sur les règles de transfert, consultez [Transférer les informations d’alerte](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Ajuster les propriétés du système

Les propriétés système contrôlent plusieurs opérations et paramètres dans le capteur. Leur modification peut mettre à mal le fonctionnement de la console du capteur.

Contactez le [support Microsoft](https://support.microsoft.com/) avant de modifier vos paramètres.

Pour accéder aux propriétés système :

1. Connectez-vous à la console de gestion locale ou au capteur.

2. Sélectionnez **Paramètres système**.

3. Sélectionnez **Propriétés système** dans la section **Général**.

## <a name="see-also"></a>Voir aussi

[Recherche et packages de renseignement sur les menaces](how-to-work-with-threat-intelligence-packages.md)

[Gérer les capteurs depuis la console de gestion](how-to-manage-sensors-from-the-on-premises-management-console.md)