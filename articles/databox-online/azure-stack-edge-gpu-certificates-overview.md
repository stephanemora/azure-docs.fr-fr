---
title: Vue d’ensemble des certificats Azure Stack Edge Pro GPU, Pro R et Mini R
description: Donne une vue d’ensemble de tous les certificats qui peuvent être utilisés sur un appareil Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 558b31262a74a351ef17e42eb79772645f9a4641
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290376"
---
# <a name="what-are-certificates-on-azure-stack-edge-pro-gpu"></a>Que sont les certificats sur Azure Stack Edge Pro GPU ?

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article décrit les types de certificats qui peuvent être installés sur votre appareil Azure Stack Edge Pro GPU. L’article comprend également les détails de chaque type de certificat.  

## <a name="about-certificates"></a>À propos des certificats

Un certificat fournit un lien entre une **clé publique** et une entité (telle qu’un nom de domaine) qui a été **signée** (vérifiée) par un tiers de confiance (par exemple une **autorité de certification**).  Un certificat offre un moyen pratique de distribuer des clés de chiffrement publiques approuvées. Les certificats garantissent que votre communication est approuvée et que vous envoyez des informations chiffrées au serveur approprié. 

## <a name="deploying-certificates-on-device"></a>Déploiement de certificats sur un appareil

Sur votre appareil Azure Stack Edge, vous pouvez utiliser les certificats auto-signés ou apporter vos propres certificats.

- **Certificats générés par l’appareil** : lors de la configuration initiale de votre appareil, des certificats auto-signés sont générés automatiquement. Si nécessaire, vous pouvez régénérer ces certificats via l’interface utilisateur web locale. Une fois les certificats régénérés, téléchargez et importez les certificats sur les clients utilisés pour accéder à votre appareil.

- **Apporter vos propres certificats** : si vous le souhaitez, vous pouvez apporter vos propres certificats. Vous devez suivre certaines instructions si vous envisagez d’apporter vos propres certificats.

- Commencez par comprendre les types des certificats qui peuvent être utilisés avec votre appareil Azure Stack Edge dans cet article.
- Ensuite, passez en revue les exigences relatives aux [certificats pour chaque type](azure-stack-edge-gpu-certificate-requirements.md).  
- Vous pouvez ensuite [créer vos certificats via Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md) ou [créer vos certificats via l’outil Readiness checker](azure-stack-edge-gpu-create-certificates-tool.md).
- Enfin, [convertissez les certificats au format approprié](azure-stack-edge-gpu-prepare-certificates-device-upload.md) afin qu’ils soient prêts à être téléchargés sur votre appareil.
- [Chargez vos certificats](azure-stack-edge-gpu-manage-certificates.md#upload-certificates-on-your-device) sur l’appareil.
- [Importer les certificats sur les clients](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device) qui accèdent à l’appareil.

## <a name="types-of-certificates"></a>Types de certificats

Les différents types de certificats que vous pouvez apporter à votre appareil sont les suivants : 
- Certificats de signature
    - Autorité de certification racine
    - Intermédiaire

- Certificats de nœud

- Certificats de point de terminaison   
   
    - Certificats Azure Resource Manager
    - Certificats de stockage d’objets Blob

- Certificats de l’interface utilisateur locale
- Certificats d’appareils IoT
    
- Certificats Kubernetes
    
- Certificats Wi-Fi
- Certificats VPN  

- Certificats de chiffrement
    - Certificats de session de support

Chacun de ces certificats est décrit en détail dans les sections suivantes.

## <a name="signing-chain-certificates"></a>Certificats de chaîne de signature

Il s’agit des certificats pour l’autorité qui signe les certificats ou l’autorité de certification de signature. 

### <a name="types"></a>Types

Ces certificats peuvent être des certificats racines ou des certificats intermédiaires. Les certificats racines sont toujours auto-signés (ou signés par eux-mêmes). Les certificats intermédiaires ne sont pas auto-signés et sont signés par l’autorité de signature.

#### <a name="caveats"></a>Mises en garde

- Les certificats racines doivent être des certificats de chaîne de signature.
- Les certificats racines peuvent être téléchargés sur votre appareil au format suivant : 
    - **DER** : disponibles sous la forme d’une extension de fichier `.cer`.
    - **Encodé en Base64** : également disponibles en tant qu’extension de fichier `.cer`.
    - **P7B** : ce format est utilisé uniquement pour les certificats de chaîne de signature qui incluent les certificats racines et intermédiaires.
- Les certificats de chaîne de signature sont toujours téléchargés avant que vous téléchargiez d’autres certificats.


## <a name="node-certificates"></a>Certificats de nœud

<!--Your  device could be a 1-node device or a 4-node device.--> Tous les nœuds dans l’appareil communiquent en permanence entre eux et doivent donc avoir une relation d’approbation. Les certificats de nœud permettent d’établir cette approbation. Les certificats de nœud entrent également en jeu lorsque vous vous connectez au nœud de l’appareil à l’aide d’une session PowerShell à distance via https.

#### <a name="caveats"></a>Mises en garde

- Le certificat de nœud doit être fourni au format `.pfx` avec une clé privée qui peut être exportée. 
- Vous pouvez créer et charger 1 certificat de nœud générique ou 4 certificats de nœuds individuels. 
- Un certificat de nœud doit être modifié si le domaine DNS change mais que le nom de l’appareil n’est pas modifié. Si vous apportez votre propre certificat de nœud, vous ne pouvez pas modifier le numéro de série de l’appareil, vous pouvez uniquement modifier le nom de domaine.
- Utilisez le tableau suivant pour vous guider lors de la création d’un certificat de nœud.
   
    |Type |Nom de l’objet (SN)  |Autre nom de l’objet (SAN)  |Exemple de nom de sujet |
    |---------|---------|---------|---------|
    |Nœud|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certificats de point de terminaison

Pour tous les points de terminaison exposés par l’appareil, un certificat est requis pour la communication approuvée. Les certificats de point de terminaison incluent ceux qui sont requis lors de l’accès à Azure Resource Manager et au stockage d’objets Blob via les API REST. 

Lorsque vous apportez votre propre certificat signé, vous avez également besoin de la chaîne de signature correspondante du certificat. Pour la chaîne de signature, Azure Resource Manager et les certificats Blob sur l’appareil, vous aurez besoin des certificats correspondants sur l’ordinateur client pour vous authentifier et communiquer avec l’appareil.

#### <a name="caveats"></a>Mises en garde

- Les certificats de point de terminaison doivent être au format `.pfx` avec une clé privée. La chaîne de signature doit être au format DER (extension de fichier `.cer`). 
- Lorsque vous apportez vos propres certificats de point de terminaison, il peut s’agir de certificats individuels ou de certificats multidomaines. 
- Si vous importez une chaîne de signature, le certificat de la chaîne de signature doit être téléchargé avant de télécharger un certificat de point de terminaison.
- Ces certificats doivent être modifiés si le nom de l’appareil ou les noms de domaine DNS sont modifiés.
- Vous pouvez utiliser un certificat de point de terminaison générique.
- Les propriétés des certificats de point de terminaison sont similaires à celles d’un certificat SSL standard. 
- Utilisez le tableau suivant lors de la création d’un certificat de point de terminaison :

    |Type |Nom de l’objet (SN)  |Autre nom de l’objet (SAN)  |Exemple de nom de sujet |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Stockage d'objets blob|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificat unique à plusieurs SAN pour les deux points de terminaison|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Certificats de l’interface utilisateur locale

Vous pouvez accéder à l’interface utilisateur web locale de votre appareil via un navigateur. Pour vous assurer que cette communication est sécurisée, vous pouvez télécharger votre propre certificat. 

#### <a name="caveats"></a>Mises en garde

- Le certificat de l’interface utilisateur locale est également téléchargé dans un format `.pfx` avec une clé privée qui peut être exportée.
- Après avoir téléchargé le certificat de l’interface utilisateur locale, vous devrez redémarrer le navigateur et effacer le cache. Reportez-vous aux instructions spécifiques à votre navigateur.

    |Type |Nom de l’objet (SN)  |Autre nom de l’objet (SAN)  |Exemple de nom de sujet |
    |---------|---------|---------|---------|
    |Interface utilisateur locale| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>Certificats d’appareil IoT Edge

Votre appareil est également un appareil IoT avec le calcul activé par un appareil IoT Edge connecté. Pour permettre une connexion sécurisée entre votre appareil IoT Edge et les appareils en aval qui peuvent s’y connecter, vous pouvez également télécharger des certificats IoT Edge. 

L’appareil dispose de certificats auto-signés qui peuvent être utilisés si vous souhaitez utiliser uniquement le scénario de calcul avec l’appareil. Cependant, si l’appareil est connecté à des appareils en aval, vous devez apporter vos propres certificats.

Il y a trois certificats IoT Edge que vous devez installer pour activer cette relation d’approbation :

- **Autorité de certification racine ou autorité de certification propriétaire**
- **Autorité de certification de l’appareil** 
- **Certificat de clé d’appareil**


#### <a name="caveats"></a>Mises en garde

- Les certificats IoT Edge sont téléchargés au format `.pem`. 

Pour plus d’informations sur les certificats IoT Edge, consultez [Détails des certificats Azure IoT Edge](../iot-edge/iot-edge-certs.md#iot-edge-certificates) et [Créer des certificats de production IoT Edge](../iot-edge/how-to-manage-device-certificates.md?preserve-view=true&view=iotedge-2020-11#create-production-certificates).

## <a name="kubernetes-certificates"></a>Certificats Kubernetes

Si votre appareil possède un registre de conteneurs Edge, vous aurez besoin d’un certificat de registre de conteneurs Edge pour la communication sécurisée avec le client qui accède au registre sur l’appareil.

#### <a name="caveats"></a>Mises en garde

- Le certificat de registre de conteneurs Edge doit être chargé au format *.pfx* avec une clé privée.

## <a name="vpn-certificates"></a>Certificats VPN

Si le VPN (point à site) est configuré sur votre appareil, vous pouvez apporter votre propre certificat VPN pour garantir la fiabilité de la communication. Le certificat racine est installé sur la passerelle VPN Azure, et les certificats clients sont installés sur chaque ordinateur client qui se connecte à un réseau virtuel à l’aide d’une connexion point à site.

#### <a name="caveats"></a>Mises en garde

- Le certificat VPN doit être chargé au format *.pfx* avec une clé privée.
- Le certificat VPN ne dépend pas du nom, du numéro de série ou de la configuration de l’appareil. Il nécessite uniquement le nom de domaine complet externe.
- Assurez-vous que l’OID du client est défini.

Pour plus d’informations, consultez [Générer et exporter des certificats pour les connexions point à site à l’aide de PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md#generate-and-export-certificates-for-point-to-site-using-powershell).

## <a name="wi-fi-certificates"></a>Certificats Wi-Fi

Si votre appareil est configuré pour fonctionner sur un réseau sans fil WPA2-Enterprise, vous aurez également besoin d’un certificat Wi-Fi pour toutes les communications qui se produisent sur le réseau sans fil. 

#### <a name="caveats"></a>Mises en garde

- Le certificat VPN doit être chargé au format *.pfx* avec une clé privée.
- Assurez-vous que l’OID du client est défini.

## <a name="support-session-certificates"></a>Certificats de session de support

Si votre appareil rencontre des problèmes, pour résoudre ces derniers, vous pouvez ouvrir une session de support PowerShell à distance sur l’appareil. Pour activer une communication sécurisée et chiffrée sur cette session de support, vous pouvez télécharger un certificat.

#### <a name="caveats"></a>Mises en garde

- Assurez-vous que le certificat `.pfx` correspondant avec la clé privée est installé sur l’ordinateur client à l’aide de l’outil de déchiffrement.
- Vérifiez que le champ **Utilisation de la clé** pour le certificat n’est pas défini sur **Signature du certificat**. Pour vérifier cela, cliquez avec le bouton droit sur le certificat, choisissez **Ouvrir** et dans l’onglet **Détails**, recherchez **Utilisation de la clé**. 

- Le certificat de session de support doit être fourni au format DER avec une extension `.cer`.


## <a name="next-steps"></a>Étapes suivantes

[Examinez les exigences des certificats](azure-stack-edge-gpu-certificate-requirements.md).