---
title: Utiliser des profils Wi-Fi avec des appareils Azure Stack Edge Mini R
description: Décrit comment créer des profils Wi-Fi pour les appareils Azure Stack Edge Mini R sur des réseaux personnels et des réseaux d’entreprise à haute sécurité.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050299"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Utiliser des profils Wi-Fi avec des appareils Azure Stack Edge Mini R

Cet article explique comment utiliser des profils réseau sans fil (Wi-Fi) avec vos appareils Azure Stack Edge Mini R.

La façon dont vous préparez le profil Wi-Fi dépend du type de réseau sans fil :

- Sur un réseau personnel Wi-Fi Protected Access 2 (WPA2) tel qu’un réseau domestique ou une zone Wi-Fi réactive ouverte, vous pouvez télécharger et utiliser un profil sans fil existant avec le même mot de passe que celui que vous utilisez pour d’autres appareils.

- Dans un environnement d’entreprise à haute sécurité, vous accédez à votre appareil via un réseau d’entreprise WPA2. Sur ce type de réseau, chaque ordinateur client a un profil Wi-Fi distinct et est authentifié par le biais de certificats. Vous devez collaborer avec votre administrateur réseau pour déterminer la configuration requise.

Nous aborderons les exigences de profil pour les deux types de réseau plus tard.

Dans les deux cas, il est très important de s’assurer que le profil répond aux exigences de sécurité de votre organisation avant de tester ou d’utiliser le profil avec votre appareil.

## <a name="about-wi-fi-profiles"></a>À propos des profils Wi-Fi

Un profil Wi-Fi contient l’identificateur SSID (Service Set identifier, ou **nom de réseau**), la clé de mot de passe et les informations de sécurité nécessaires pour connecter votre appareil Azure Stack Edge Mini R à un réseau sans fil.

L’exemple de code suivant montre les paramètres de base d’un profil à utiliser avec un réseau sans fil classique :

* `SSID` est le nom du réseau.

* `name` est le nom convivial de la connexion Wi-Fi. Il s'agit du nom que voient les utilisateurs lorsqu’ils parcourent les connexions disponibles sur leurs appareils.

* Le profil est configuré pour connecter automatiquement l’ordinateur au réseau sans fil lorsqu’il est dans la plage du réseau (`connectionMode` = `auto`).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Pour plus d’informations sur les paramètres de profil Wi-Fi, consultez **Profil d’entreprise** dans [Ajouter des paramètres Wi-Fi pour les appareils Windows 10 et versions ultérieures](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile), et consultez [Configurer le profil Wi-Fi Cisco](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Pour activer les connexions sans fil sur un appareil Azure Stack Edge Mini R, vous devez configurer le port Wi-Fi sur votre appareil, puis ajouter le ou les profils Wi-Fi à l’appareil. Sur un réseau d’entreprise, vous devez également télécharger des certificats sur l’appareil. Vous pouvez ensuite vous connecter à un réseau sans fil à partir de l’interface utilisateur web locale de l’appareil. Pour plus d’informations, consultez [Gérer la connectivité sans fil de votre appareil Azure Stack Edge Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Profil pour un réseau personnel WPA2

Sur un réseau personnel Wi-Fi Protected Access 2 (WPA2), tel qu’un réseau domestique ou une zone Wi-Fi réactive ouverte, plusieurs appareils peuvent utiliser le même profil et le même mot de passe. Sur votre réseau domestique, votre téléphone mobile et votre ordinateur portable utilisent le même profil et le même mot de passe sans fil pour se connecter au réseau.

Par exemple, un client Windows 10 peut générer un profil d’exécution pour vous. Lorsque vous vous connectez au réseau sans fil, vous êtes invité à entrer le mot de passe Wi-Fi et, une fois que vous avez fourni ce mot de passe, vous êtes connecté. Aucun certificat n’est nécessaire dans cet environnement.

Sur ce type de réseau, vous pouvez exporter un profil Wi-Fi à partir de votre ordinateur portable, puis l’ajouter à votre appareil Azure Stack Edge Mini R. Pour obtenir des instructions, consultez [Exporter un profil Wi-Fi](#export-a-wi-fi-profile), ci-dessous.

> [!IMPORTANT]
> Avant de créer un profil Wi-Fi pour votre appareil Azure Stack Edge Mini R, contactez votre administrateur réseau pour connaître les exigences de sécurité de l’organisation en matière de mise en réseau sans fil. Vous ne devriez pas tester ou utiliser un profil Wi-Fi sur votre appareil tant que vous n’êtes pas sûr que le réseau sans fil répond aux exigences.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profils pour un réseau d’entreprise WPA2

Sur un réseau d’entreprise Wireless Protected Access 2 (WPA2), vous devez collaborer avec votre administrateur réseau pour obtenir le profil Wi-Fi et le certificat requis pour connecter votre appareil Azure Stack Edge Mini R au réseau.

Pour les réseaux hautement sécurisés, l’appareil Azure peut utiliser le protocole PEAP (Protected Extensible Authentication Protocol) avec le protocole d’authentification extensible EAP-TLS (Extensible Authentication Protocol-Transport Layer Security). PEAP avec EAP-TLS utilise l’authentification de la machine : le client et le serveur utilisent des certificats pour vérifier leurs identités.

> [!NOTE]
> * L’authentification des utilisateurs à l’aide du protocole PEAP MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol version 2) n’est pas prise en charge sur les appareils Azure Stack Edge Mini R.
> * L’authentification EAP-TLS est requise pour accéder à la fonctionnalité Azure Stack Edge Mini R. Une connexion sans fil que vous configurez à l’aide de Active Directory ne fonctionnera pas.

L’administrateur réseau génère un profil Wi-Fi unique et un certificat client pour chaque ordinateur. L’administrateur réseau décide s’il faut utiliser un certificat distinct pour chaque appareil ou un certificat partagé.

Si vous travaillez dans plusieurs emplacements physiques sur l’espace de travail, l’administrateur réseau devra peut-être fournir plus d’un profil Wi-Fi spécifique au site et un certificat pour vos connexions sans fil.

Sur un réseau d’entreprise, nous vous recommandons de ne pas modifier les paramètres dans les profils Wi-Fi fournis par votre administrateur réseau. Le seul ajustement que vous pouvez effectuer est celui des paramètres de connexion automatique. Pour plus d’informations, consultez [Profil de base](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) dans les paramètres Wi-Fi pour les appareils Windows 10 et versions ultérieures.

Dans un environnement d’entreprise à haute sécurité, vous pouvez utiliser un profil réseau sans fil existant en tant que modèle :

* Vous pouvez télécharger le profil réseau sans fil d’entreprise à partir de votre ordinateur professionnel. Pour obtenir des instructions, consultez [Exporter un profil Wi-Fi](#export-a-wi-fi-profile), ci-dessous.

* Si d’autres membres de votre organisation se connectent déjà à leurs appareils Azure Stack Edge Mini R sur un réseau sans fil, ils peuvent télécharger le profil Wi-Fi à partir de leur appareil. Pour obtenir des instructions, consultez [Télécharger un profil Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Exporter un profil Wi-Fi

Pour exporter un profil pour l’interface Wi-Fi sur votre ordinateur, procédez comme suit :

1. Pour afficher les profils sans fil sur votre ordinateur, dans le menu **Démarrer**, ouvrez l'**Invite de commandes** (cmd.exe), puis entrez la commande suivante :

   `netsh wlan show profiles`

   Vous obtenez un résultat semblable à ce qui suit :

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Pour exporter un profil, entrez la commande suivante :

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Par exemple, la commande suivante enregistre le profil ContosoFTINET au format XML dans le dossier Téléchargements pour l’utilisateur nommé `gusp`.

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Ajouter un certificat, profil Wi-Fi à l’appareil

Lorsque vous avez les profils Wi-Fi et les certificats dont vous avez besoin, procédez comme suit pour configurer votre appareil Azure Stack Edge Mini R pour les connexions sans fil :

1. Pour un réseau d’entreprise WPA2, chargez les certificats nécessaires sur l’appareil en suivant les instructions dans [Charger des certificats](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Chargez le ou les profils Wi-Fi sur l’appareil Mini R, puis connectez-vous à ce dernier en suivant les instructions dans [Ajouter, se connecter à un profil Wi-Fi](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Configurer le réseau pour Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Découvrez comment [Gérer les Wi-Fi sur votre appareil Azure Stack Edge Mini R](azure-stack-edge-mini-r-manage-wifi.md).
