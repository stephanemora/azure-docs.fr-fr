---
title: Gérer des certificats d’appareils – Azure IoT Edge | Microsoft Docs
description: Créez des certificats de test, installez-les et gérez-les sur un appareil Azure IoT Edge pour préparer le déploiement de production.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79541501"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gérer des certificats sur un appareil IoT Edge

Tous les appareils IoT Edge utilisent des certificats pour créer des connexions sécurisées entre le runtime et les modules en cours d’exécution sur l’appareil. Les appareils IoT Edge fonctionnant en tant que passerelles utilisent également ces mêmes certificats pour se connecter à leurs appareils en aval.

## <a name="install-production-certificates"></a>Installer les certificats de production

Lorsque vous installez IoT Edge et approvisionnez votre appareil pour la première fois, l’appareil est configuré avec des certificats temporaires afin que vous puissiez tester le service.
Ces certificats temporaires expirent au bout de 90 jours ou peuvent être réinitialisés en redémarrant votre ordinateur.
Une fois que vous êtes prêt à déplacer vos appareils dans un scénario de production, ou si vous souhaitez créer un scénario de passerelle, vous devez fournir vos propres certificats.
Cet article décrit les étapes à suivre pour installer des certificats sur vos appareils IoT Edge.

Pour en savoir plus sur les différents types de certificats et leurs rôles dans un scénario IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

>[!NOTE]
>Le terme « autorité de certification racine » utilisé dans cet article fait référence au certificat public de la plus grande autorité de la chaîne de certificats pour votre solution IoT. Vous n’avez pas besoin d’utiliser la racine du certificat d’une autorité de certification syndiquée ou la racine de l’autorité de certification de votre organisation. La plupart du temps, il s’agit en fait d’un certificat public d’une autorité de certification intermédiaire.

### <a name="prerequisites"></a>Prérequis

* Un appareil IoT Edge, exécuté sur [Windows](how-to-install-iot-edge-windows.md) ou [Linux](how-to-install-iot-edge-linux.md).
* Avoir un certificat d’autorité de certification (AC) racine, soit auto-signé, soit acheté auprès d’une autorité de certification commerciale approuvée telle que Baltimore, VeriSign, DigiCert ou GlobalSign.

Si vous n’avez pas encore d’autorité de certification racine, mais que vous souhaitez essayer des fonctionnalités IoT Edge qui requièrent des certificats de production (comme des scénarios de passerelle), vous pouvez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Créer des certificats de production

Vous devez utiliser votre propre autorité de certification pour créer les fichiers suivants :

* Autorité de certification racine
* Certificat d’autorité de certification d’appareil
* Clé privée d’autorité de certification d’appareil

Dans cet article, l’*autorité de certification racine* à laquelle nous faisons référence n’est pas l’autorité de certification la plus élevée pour une organisation. Il s’agit de l’autorité de certification la plus élevée pour le scénario IoT Edge, que le module du hub IoT Edge, les modules utilisateur et tous les appareils en aval utilisent pour établir une relation de confiance entre eux.

Pour voir un exemple de ces certificats, passez en revue les scripts qui créent des certificats de démonstration dans [Gestion de certificats d’autorité de certification de test pour des exemples et tutoriels](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installer des certificats sur l’appareil

Installez votre chaîne de certification sur l’appareil IoT Edge et configurez le runtime IoT Edge de façon à ce qu’il référence les nouveaux certificats.

Par exemple, si vous avez utilisé les exemples de script pour [Créer des certificats de démonstration](how-to-create-test-certificates.md), copiez les fichiers suivants sur votre appareil IoT Edge :

* Certificat d’autorité de certification d’appareil : `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Clé privée d’autorité de certification d’appareil : `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Autorité de certification racine : `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copiez les trois fichiers de certificat et de clé sur votre appareil IoT Edge.

   Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.  Si vous avez généré les certificats directement sur l’appareil IoT Edge, vous pouvez ignorer cette étape et utiliser le chemin du répertoire de travail.

1. Ouvrez le fichier config de démon de sécurité IoT Edge.

   * Windows : `C:\ProgramData\iotedge\config.yaml`
   * Linux : `/etc/iotedge/config.yaml`

1. Définissez les propriétés **certificates** dans le fichier config.yaml sur le chemin complet où vous avez placé les fichiers de certificats et de clés sur l’appareil IoT Edge. Supprimez le caractère `#` devant les propriétés certificates pour décommenter les quatre lignes. Vérifiez que la ligne **certificates:** n’est pas précédée d’un espace et que les éléments imbriqués sont mis en retrait de deux espaces. Par exemple :

   * Windows :

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux :

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Sur les appareils Linux, assurez-vous que l’utilisateur **iotedge** a les autorisations de lecture sur le répertoire contenant les certificats.

1. Si vous avez déjà utilisé d'autres certificats pour IoT Edge sur l'appareil, supprimez les fichiers dans les deux répertoires suivants avant de démarrer ou de redémarrer IoT Edge :

   * Windows : `C:\ProgramData\iotedge\hsm\certs` et `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux : `/var/lib/iotedge/hsm/certs` et `/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Personnaliser la durée de vie du certificat

IoT Edge génère automatiquement des certificats sur l’appareil dans plusieurs cas, notamment :

* Si vous ne fournissez pas vos propres certificats de production lors de l’installation et de la configuration d’IoT Edge, le gestionnaire de sécurité IoT Edge génère automatiquement un **certificat d’autorité de certification d’appareil**. Ce certificat auto-signé est conçu uniquement pour les scénarios de développement et de test, et non pour la production. Il expire au bout de 90 jours.
* Le gestionnaire de sécurité IoT Edge génère également un **certificat d’autorité de certification de charge de travail** signé par le certificat d’autorité de certification de l’appareil.

Pour plus d’informations sur la fonction des différents certificats sur un appareil IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

Pour ces deux certificats générés automatiquement, vous avez la possibilité de définir l’indicateur **auto_generated_ca_lifetime_days** dans config.yamlpour configurer le nombre de jours de la durée de vie des certificats.

>[!NOTE]
>Un troisième certificat généré automatiquement, le **certificat de serveur de sécurité IoT Edge**, est créé par le gestionnaire de sécurité IoT Edge. Ce certificat est toujours valable 90 jours, mais il est renouvelé automatiquement avant d’expirer. La valeur **auto_generated_ca_lifetime_days** n’affecte pas ce certificat.

Pour configurer l’expiration du certificat sur une valeur autre que la valeur par défaut de 90 jours, ajoutez la valeur en jours à la section **certificats** du fichier config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Si vous avez fourni vos propres certificats d’autorité de certification d’appareil, cette valeur s’applique toujours au certificat de l’autorité de certification de la charge de travail, à condition que la valeur de durée de vie définie par vos soins soit inférieure à la durée de vie du certificat de l’autorité de certification.

Une fois que vous avez spécifié l’indicateur dans le fichier config.yaml, procédez comme suit :

1. Supprimez les contenus du répertoire `hsm`.

   Windows : `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux : `/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Redémarrez le service IoT Edge.

   Windows :

   ```powershell
   Restart-Service iotedge
   ```

   Linux :

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confirmez le paramètre de durée de vie.

   Windows :

   ```powershell
   iotedge check --verbose
   ```

   Linux :

   ```bash
   sudo iotedge check --verbose
   ```

   Vérifiez la sortie de la vérification de la **préparation de la production : certificats**, qui répertorie le nombre de jours jusqu’à l’expiration des certificats d’autorité de certification d’appareils générés automatiquement.

## <a name="next-steps"></a>Étapes suivantes

L’installation de certificats sur un appareil IoT Edge est une étape nécessaire avant le déploiement de votre solution en production. Découvrez comment [Préparer le déploiement en production d’une solution IoT Edge](production-checklist.md).
