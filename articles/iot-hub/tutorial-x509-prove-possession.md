---
title: "Tutoriel : Prouver que vous êtes propriétaire d'un certificat d'autorité de certification dans Azure IoT Hub | Microsoft Docs"
description: "Tutoriel : Prouver que vous êtes propriétaire d'un certificat d'autorité de certification dans Azure IoT Hub"
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378225"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Tutoriel : Prouver que vous êtes propriétaire d'un certificat d'autorité de certification

Après avoir chargé votre certificat d'autorité de certification (AC) racine ou votre certificat d'AC subordonné sur votre instance d'IoT Hub, vous devez prouver que vous êtes propriétaire de ce certificat :

1. Sur le portail Azure, accédez à votre instance d'IoT Hub et sélectionnez **Paramètres > Certificats**.

2. Sélectionnez **Ajouter** pour ajouter un nouveau certificat d'AC.

3. Entrez un nom d'affichage dans le champ **Nom du certificat**, puis sélectionnez le certificat PEM à ajouter.

4. Sélectionnez **Enregistrer**. Votre certificat apparaît dans la liste des certificats avec l'état **Non vérifié**. Le processus de vérification prouvera que vous êtes propriétaire du certificat.

5. Sélectionnez le certificat pour afficher la boîte de dialogue **Détails du certificat**.

6. Sélectionnez **Générer un code de vérification** dans la boîte de dialogue.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Certificate details dialog}":::

7. Copiez le code de vérification dans le Presse-papiers. Vous devez définir le code de vérification en tant qu'objet du certificat. Par exemple, si le code de vérification est 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, ajoutez-le en tant qu'objet de votre certificat, comme indiqué à l'étape suivante.

8. Un certificat de vérification peut être généré de trois façons :

    * Si vous utilisez le script PowerShell fourni par Microsoft, exécutez `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` pour créer un certificat nommé `VerifyCert4.cer`. Pour plus d'informations, consultez [Utilisation des scripts fournis par Microsoft](tutorial-x509-scripts.md).

    * Si vous utilisez le script Bash fourni par Microsoft, exécutez `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` pour créer un certificat nommé `verification-code.cert.pem`. Pour plus d'informations, consultez [Utilisation des scripts fournis par Microsoft](tutorial-x509-scripts.md).

    * Si vous utilisez OpenSSL pour générer vos certificats, vous devez d'abord générer une clé privée, puis une demande de signature de certificat (CSR) :

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Créez ensuite un certificat à l'aide du fichier de configuration de l'AC racine (voir ci-dessous), ou du fichier de configuration de l'AC subordonnée et de la demande de signature de certificat.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Pour plus d'informations, consultez [Utilisation d'OpenSSL pour créer des certificats de test](tutorial-x509-openssl.md).

10. Sélectionnez le nouveau certificat dans l'affichage **Détails du certificat**.

11. Une fois le certificat chargé, sélectionnez **Vérifier**. L'état du certificat d'AC doit passer à **Vérifié**.
