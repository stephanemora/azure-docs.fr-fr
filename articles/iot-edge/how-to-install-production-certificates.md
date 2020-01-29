---
title: Installer des certificats sur un appareil - Azure IoT Edge | Microsoft Docs
description: Créez des certificats de test et apprenez à les installer sur un appareil Azure IoT Edge pour préparer le déploiement de production.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf073572cd5b371ec484c99f14cbefb4cba75ce7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509901"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Installer des certificats de production sur un appareil IoT Edge

Tous les appareils IoT Edge utilisent des certificats pour créer des connexions sécurisées entre le runtime et les modules en cours d’exécution sur l’appareil.
Les appareils IoT Edge fonctionnant en tant que passerelles utilisent également ces mêmes certificats pour se connecter à leurs appareils en aval.

Lorsque vous installez IoT Edge et approvisionnez votre appareil pour la première fois, l’appareil est configuré avec des certificats temporaires afin que vous puissiez tester le service.
Ces certificats temporaires expirent au bout de 90 jours ou peuvent être réinitialisés en redémarrant votre ordinateur.
Une fois que vous êtes prêt à déplacer vos appareils dans un scénario de production, ou si vous souhaitez créer un scénario de passerelle, vous devez fournir vos propres certificats.
Cet article décrit les étapes à suivre pour installer des certificats sur vos appareils IoT Edge.

Pour en savoir plus sur les différents types de certificats et leurs rôles dans un scénario IoT Edge, consultez [Comprendre comment Azure IoT Edge utilise les certificats](iot-edge-certs.md).

>[!NOTE]
>Le terme « autorité de certification racine » utilisé dans cet article fait référence au certificat public de la plus grande autorité de la chaîne de certificats pour votre solution IoT. Vous n’avez pas besoin d’utiliser la racine du certificat d’une autorité de certification syndiquée ou la racine de l’autorité de certification de votre organisation. La plupart du temps, il s’agit en fait d’un certificat public d’une autorité de certification intermédiaire.

## <a name="prerequisites"></a>Conditions préalables requises

* Un appareil IoT Edge, exécuté sur [Windows](how-to-install-iot-edge-windows.md) ou [Linux](how-to-install-iot-edge-linux.md).
* Avoir un certificat d’autorité de certification (AC) racine, soit auto-signé, soit acheté auprès d’une autorité de certification commerciale approuvée telle que Baltimore, VeriSign, DigiCert ou GlobalSign.

Si vous n’avez pas encore d’autorité de certification racine, mais que vous souhaitez essayer des fonctionnalités IoT Edge qui requièrent des certificats de production (comme des scénarios de passerelle), vous pouvez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Créer des certificats de production

Vous devez utiliser votre propre autorité de certification pour créer les fichiers suivants :

* Autorité de certification racine
* Certificat d’autorité de certification d’appareil
* Clé privée d’autorité de certification d’appareil

Dans cet article, l’*autorité de certification racine* à laquelle nous faisons référence n’est pas l’autorité de certification la plus élevée pour une organisation. Il s’agit de l’autorité de certification la plus élevée pour le scénario IoT Edge, que le module du hub IoT Edge, les modules utilisateur et tous les appareils en aval utilisent pour établir une relation de confiance entre eux.

Pour voir un exemple de ces certificats, passez en revue les scripts qui créent des certificats de démonstration dans [Gestion de certificats d’autorité de certification de test pour des exemples et tutoriels](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Installer des certificats sur l’appareil

Installez votre chaîne de certification sur l’appareil IoT Edge et configurez le runtime IoT Edge de façon à ce qu’il référence les nouveaux certificats.

Par exemple, si vous avez utilisé les exemples de script pour [Créer des certificats de démonstration](how-to-create-test-certificates.md), les trois fichiers que vous devez copier sur votre appareil IoT Edge sont les suivants :

* Certificat d’autorité de certification d’appareil : `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Clé privée d’autorité de certification d’appareil : `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Autorité de certification racine : `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copiez les trois fichiers de certificat et de clé sur votre appareil IoT Edge.

   Vous pouvez utiliser un service comme [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou une fonction comme [SCP (Secure Copy Protocol)](https://www.ssh.com/ssh/scp/) pour déplacer les fichiers de certificats.  Si vous avez généré les certificats directement sur l’appareil IoT Edge, vous pouvez ignorer cette étape et utiliser le chemin du répertoire de travail.

2. Ouvrez le fichier config de démon de sécurité IoT Edge.

   * Windows : `C:\ProgramData\iotedge\config.yaml`
   * Linux : `/etc/iotedge/config.yaml`

3. Définissez les propriétés de **certificat** dans le fichier config.yaml sur le fichier URI des fichiers de certificats et de clés sur l’appareil IoT Edge. Supprimez le caractère `#` devant les propriétés certificates pour décommenter les quatre lignes. Vérifiez que la ligne **certificates:** n’est pas précédée d’un espace et que les éléments imbriqués sont mis en retrait de deux espaces. Par exemple :

   * Windows :

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux :

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Sur les appareils Linux, assurez-vous que l’utilisateur **iotedge** a les autorisations de lecture sur le répertoire contenant les certificats.

## <a name="next-steps"></a>Étapes suivantes

L’installation de certificats sur un appareil IoT Edge est une étape nécessaire avant le déploiement de votre solution en production. Découvrez comment [Préparer le déploiement en production d’une solution IoT Edge](production-checklist.md).
