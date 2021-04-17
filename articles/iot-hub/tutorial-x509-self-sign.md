---
title: 'Tutoriel : Utiliser OpenSSL afin de créer des certificats auto-signés pour Azure IoT Hub | Microsoft Docs'
description: 'Tutoriel : Utiliser OpenSSL afin de créer des certificats X.509 auto-signés pour Azure IoT Hub'
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378191"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Tutoriel : Utilisation d’OpenSSL pour créer des certificats auto-signés

Vous pouvez authentifier un appareil auprès de votre hub IoT en utilisant deux certificats d’appareil auto-signés. C’est ce que l’on appelle parfois l’authentification par empreinte, car les certificats contiennent des empreintes (valeurs de hachage) que vous envoyez au hub IoT. Les étapes suivantes vous indiquent comment créer deux certificats auto-signés.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Étape 1 : Créer une clé pour le premier certificat

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Étape 2 : Créer une demande de signature de certificat pour le premier certificat

Veillez à spécifier l’ID de l’appareil quand vous y êtes invité.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Étape 3 : Vérifier la demande de signature de certificat

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Étape 4 : Auto-signer le certificat 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Étape 5 : Créer une clé pour le certificat 2

Quand vous y êtes invité, spécifiez le même ID d’appareil que celui utilisé pour le certificat 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Étape 6 : Auto-signer le certificat 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Étape 7 : Récupérer l’empreinte du certificat 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Étape 8 : Récupérer l’empreinte du certificat 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Étape 9 : Créer un appareil IoT

Accédez à votre hub IoT dans le portail Azure et créez une identité d’appareil IoT avec les caractéristiques suivantes :

* Fournissez l’**ID d’appareil** qui correspond au nom du sujet de vos deux certificats.
* Sélectionnez le type d’authentification **X.509 autosigné**.
* Collez les empreintes des chaînes hexadécimales que vous avez copiées à partir du certificat primaire et du certificat secondaire de votre appareil. Assurez-vous que les chaînes hexadécimales n’ont pas les deux-points comme délimiteurs.

## <a name="next-steps"></a>Étapes suivantes

Accédez à [Test de l’authentification par certificat](tutorial-x509-test-certificate.md) pour déterminer si votre certificat peut authentifier votre appareil auprès de votre hub IoT.
