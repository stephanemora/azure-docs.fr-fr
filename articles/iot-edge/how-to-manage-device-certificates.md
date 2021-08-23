---
title: Gérer des certificats d’appareils – Azure IoT Edge | Microsoft Docs
description: Créez des certificats de test, installez-les et gérez-les sur un appareil Azure IoT Edge pour préparer le déploiement de production.
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fa643753aa7f916fbd79850af352c37746357693
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562114"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gérer des certificats sur un appareil IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Tous les appareils IoT Edge utilisent des certificats pour créer des connexions sécurisées entre le runtime et les modules en cours d’exécution sur l’appareil. Les appareils IoT Edge fonctionnant en tant que passerelles utilisent également ces mêmes certificats pour se connecter à leurs appareils en aval.

## <a name="install-production-certificates"></a>Installer les certificats de production

Lorsque vous installez IoT Edge et approvisionnez votre appareil pour la première fois, l’appareil est configuré avec des certificats temporaires afin que vous puissiez tester le service.
Ces certificats temporaires expirent au bout de 90 jours ou peuvent être réinitialisés en redémarrant votre ordinateur.
Une fois que vous entrez dans un scénario de production ou que vous souhaitez créer un appareil de passerelle, vous devez fournir vos propres certificats.
Cet article décrit les étapes à suivre pour installer des certificats sur vos appareils IoT Edge.

Pour en savoir plus sur les différents types de certificat et leurs rôles, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

>[!NOTE]
>Le terme « autorité de certification racine » utilisé dans cet article fait référence au certificat public de la plus grande autorité de la chaîne de certificats pour votre solution IoT. Vous n’avez pas besoin d’utiliser la racine du certificat d’une autorité de certification syndiquée ou la racine de l’autorité de certification de votre organisation. La plupart du temps, il s’agit en fait d’un certificat public d’une autorité de certification intermédiaire.

### <a name="prerequisites"></a>Prérequis

* Un appareil IoT Edge.

  Si vous n’avez aucun appareil IoT Edge configuré, vous pouvez en créer un sur une machine virtuelle Azure. Suivez les étapes décrites dans l’un des articles de démarrage rapide pour [Créer un appareil Linux virtuel](quickstart-linux.md) ou [Créer un appareil Windows virtuel](quickstart.md).

* Avoir un certificat d’autorité de certification (AC) racine, soit auto-signé, soit acheté auprès d’une autorité de certification commerciale approuvée telle que Baltimore, VeriSign, DigiCert ou GlobalSign.

  Si vous n’avez pas encore d’autorité de certification racine, mais que vous souhaitez essayer des fonctionnalités IoT Edge qui requièrent des certificats de production (comme des scénarios de passerelle), vous pouvez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Créer des certificats de production

Vous devez utiliser votre propre autorité de certification pour créer les fichiers suivants :

* Autorité de certification racine
* Certificat d’autorité de certification d’appareil
* Clé privée d’autorité de certification d’appareil

Dans cet article, l’*autorité de certification racine* à laquelle nous faisons référence n’est pas l’autorité de certification la plus élevée pour une organisation. Il s’agit de l’autorité de certification la plus élevée pour le scénario IoT Edge, que le module du hub IoT Edge, les modules utilisateur et tous les appareils en aval utilisent pour établir une relation de confiance entre eux.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Il existe actuellement dans libiothsm une limitation empêchant l’utilisation de certificats qui expirent le 1er janvier 2038 ou après cette date.

:::moniker-end

Pour voir un exemple de ces certificats, passez en revue les scripts qui créent des certificats de démonstration dans [Gestion de certificats d’autorité de certification de test pour des exemples et tutoriels](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installer des certificats sur l’appareil

Installez votre chaîne de certification sur l’appareil IoT Edge et configurez le runtime IoT Edge de façon à ce qu’il référence les nouveaux certificats.

Copiez les trois fichiers de certificat et de clé sur votre appareil IoT Edge. Vous pouvez utiliser un service comme [Azure Key Vault](../key-vault/index.yml) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats. Si vous avez généré les certificats directement sur l’appareil IoT Edge, vous pouvez ignorer cette étape et utiliser le chemin du répertoire de travail.

Si vous utilisez IoT Edge pour Linux sur Windows, vous devez utiliser la clé SSH située dans le fichier `id_rsa` Azure IoT Edge pour authentifier les transferts de fichiers entre le système d’exploitation hôte et la machine virtuelle Linux. Vous pouvez faire un SCP authentifié à l’aide de la commande suivante :

   ```powershell-interactive
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

   >[!NOTE]
   >L’adresse IP de la machine virtuelle Linux peut être recherchée via la commande `Get-EflowVmAddr`.

Si vous avez utilisé les exemples de script pour [Créer des certificats de démonstration](how-to-create-test-certificates.md), copiez les fichiers suivants sur votre appareil IoT Edge :

* Certificat d’autorité de certification d’appareil : `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Clé privée d’autorité de certification d’appareil : `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Autorité de certification racine : `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ouvrez le fichier config de démon de sécurité IoT Edge.

   * Linux et IoT Edge pour Linux sur Windows : `/etc/iotedge/config.yaml`

   * Windows utilisant des conteneurs Windows : `C:\ProgramData\iotedge\config.yaml`

1. Définissez les propriétés de **certificat** dans le fichier config.yaml en indiquant le chemin d’URI des fichiers de certificat et de clé sur l’appareil IoT Edge. Supprimez le caractère `#` devant les propriétés certificates pour décommenter les quatre lignes. Vérifiez que la ligne **certificates:** n’est pas précédée d’un espace et que les éléments imbriqués sont mis en retrait de deux espaces. Par exemple :

   * Linux et IoT Edge pour Linux sur Windows :

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

   * Windows utilisant des conteneurs Windows :

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

1. Sur les appareils Linux, assurez-vous que l’utilisateur **iotedge** a les autorisations de lecture sur le répertoire contenant les certificats.

1. Si vous avez déjà utilisé d'autres certificats pour IoT Edge sur l'appareil, supprimez les fichiers dans les deux répertoires suivants avant de démarrer ou de redémarrer IoT Edge :

   * Linux et IoT Edge pour Linux sur Windows :`/var/lib/iotedge/hsm/certs` et `/var/lib/iotedge/hsm/cert_keys`

   * Windows utilisant des conteneurs Windows :`C:\ProgramData\iotedge\hsm\certs` et `C:\ProgramData\iotedge\hsm\cert_keys`

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ouvrez le fichier config du démon de sécurité IoT Edge : `/etc/aziot/config.toml`

1. Recherchez le paramètre `trust_bundle_cert` au début du fichier. Supprimez les marques de commentaire de cette ligne et fournissez l’URI du fichier de certificat d’autorité de certification racine sur votre appareil.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Recherchez la section `[edge_ca]` dans le fichier config.toml. Supprimez les marques de commentaire de cette section et fournissez les chemins d’accès d’URI de fichier pour les fichiers de certificat et de clé sur l’appareil IoT Edge.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Assurez-vous que l’utilisateur **iotedge** dispose d’autorisations d’accès en lecture au répertoire contenant les certificats.

1. Si vous avez déjà utilisé d'autres certificats pour IoT Edge sur l'appareil, supprimez les fichiers dans les deux répertoires suivants avant de démarrer ou de redémarrer IoT Edge :

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Personnaliser la durée de vie du certificat

IoT Edge génère automatiquement des certificats sur l’appareil dans plusieurs cas, notamment :

* Si vous ne fournissez pas vos propres certificats de production lors de l’installation et de la configuration d’IoT Edge, le gestionnaire de sécurité IoT Edge génère automatiquement un **certificat d’autorité de certification d’appareil**. Ce certificat auto-signé est conçu uniquement pour les scénarios de développement et de test, et non pour la production. Il expire au bout de 90 jours.
* Le gestionnaire de sécurité IoT Edge génère également un **certificat d’autorité de certification de charge de travail** signé par le certificat d’autorité de certification de l’appareil.

Pour plus d’informations sur la fonction des différents certificats sur un appareil IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

Pour ces deux certificats générés automatiquement, vous avez la possibilité de définir l’indicateur **auto_generated_ca_lifetime_days** dans le fichier config pour configurer le nombre de jours de durée de vie des certificats.

>[!NOTE]
>Un troisième certificat généré automatiquement, le **certificat de serveur de sécurité IoT Edge**, est créé par le gestionnaire de sécurité IoT Edge. Ce certificat est toujours valable 90 jours, mais il est renouvelé automatiquement avant d’expirer. La valeur **auto_generated_ca_lifetime_days** n’affecte pas ce certificat.

À l’expiration après le nombre de jours spécifié, IoT Edge doit être redémarré pour régénérer le certificat d’autorité de certification d’appareil. Le certificat d’autorité de certification d’appareil n’est pas renouvelé automatiquement.

1. Pour configurer l’expiration du certificat sur une valeur autre que la valeur par défaut de 90 jours, ajoutez la valeur en jours à la section **certificates** du fichier config.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Il existe actuellement dans libiothsm une limitation empêchant l’utilisation de certificats qui expirent le 1er janvier 2038 ou après cette date.

1. Supprimez le contenu du `hsm` dossier pour supprimer tous les certificats générés précédemment.

   * Linux et IoT Edge pour Linux sur Windows :`/var/lib/iotedge/hsm/certs` et `/var/lib/iotedge/hsm/cert_keys`

   * Windows utilisant des conteneurs Windows :`C:\ProgramData\iotedge\hsm\certs` et `C:\ProgramData\iotedge\hsm\cert_keys`

1. Redémarrez le service IoT Edge.

   * Linux et IoT Edge pour Linux sur Windows :

   ```bash
   sudo systemctl restart iotedge
   ```

   * Windows utilisant des conteneurs Windows :

   ```powershell
   Restart-Service iotedge
   ```

1. Confirmez le paramètre de durée de vie.

   * Linux et IoT Edge pour Linux sur Windows :

   ```bash
   sudo iotedge check --verbose
   ```

   * Windows utilisant des conteneurs Windows :

   ```powershell
   iotedge check --verbose
   ```

   Vérifiez la sortie de la vérification de la **préparation de la production : certificats**, qui répertorie le nombre de jours jusqu’à l’expiration des certificats d’autorité de certification d’appareils générés automatiquement.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Étapes suivantes

L’installation de certificats sur un appareil IoT Edge est une étape nécessaire avant le déploiement de votre solution en production. Découvrez comment [Préparer le déploiement en production d’une solution IoT Edge](production-checklist.md).
