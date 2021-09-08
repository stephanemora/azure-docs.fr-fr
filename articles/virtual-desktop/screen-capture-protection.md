---
title: Protection contre la capture d’écran dans Azure Virtual Desktop
titleSuffix: Azure
description: Comment configurer la protection contre la capture d'écran pour Azure Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: denisgun
ms.service: virtual-desktop
ms.openlocfilehash: ddf70a56d3a787387d1364c88f26cd0faa5df6b3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219886"
---
# <a name="screen-capture-protection"></a>Protection contre la capture d’écran

La fonctionnalité de protection contre la capture d’écran empêche la capture d’informations sensibles sur les points de terminaison clients. Lorsque vous activez cette fonctionnalité, le contenu distant est automatiquement bloqué ou masqué dans les captures d’écran et les partages d’écran. En outre, le client Bureau à distance masque le contenu des logiciels malveillants susceptibles d’effectuer une capture de l’écran.

## <a name="prerequisites"></a>Configuration requise

La fonctionnalité de protection contre la capture d’écran est configurée au niveau de l’hôte de session et appliquée sur le client. Seuls les clients qui prennent en charge cette fonctionnalité peuvent se connecter à la session à distance. Actuellement, seul le client Windows Desktop prend en charge la protection contre la capture d’écran. Seuls les ordinateurs de bureau complets sont pris en charge. Supposons que l’utilisateur tente d’utiliser un client non pris en charge pour se connecter à l’hôte de session protégé ou pour accéder à RemoteApp publié sur l’hôte de session protégé. Dans ce cas, la connexion échoue avec l’erreur 0x1151. 

## <a name="configure-screen-capture-protection"></a>Configurer la protection contre la capture d’écran

1. Pour configurer la protection contre la capture d’écran, vous devez installer des modèles d’administration qui ajoutent des règles et des paramètres pour Azure Virtual Desktop. 
2. Téléchargez le [fichier de modèles de stratégie Azure Virtual Desktop](https://aka.ms/avdgpo) (AVDGPTemplate.cab) et extrayez le contenu du fichier cab et de l’archive zip.
3. Copiez le fichier *terminalserver-avd.admx* dans le dossier *%windir%\PolicyDefinitions*.
4. Copiez le fichier *en-us\terminalserver-avd.admx* dans le dossier *%windir%\PolicyDefinitions\en-us*.
5. Pour confirmer que les fichiers ont été copiés correctement, ouvrez l’Éditeur de stratégie de groupe et accédez à **Configuration de l’ordinateur** -> **Modèles d’administration** -> **Composants Windows** -> **Services Bureau à distance** -> **Hôte de session Bureau à distance** -> **Azure Virtual Desktop**.
6. Vous devez voir une ou plusieurs stratégies Azure Virtual Desktop, comme indiqué ci-dessous.

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="Capture d’écran de l’éditeur de stratégie de groupe" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!TIP]
   > Vous pouvez également installer des modèles d’administration dans le magasin central des stratégies de groupe dans votre domaine Active Directory.
   > Pour plus d'informations sur le magasin central des modèles d’administration de stratégie de groupe, consultez [Créer et gérer le magasin central des modèles d'administration des stratégies de groupe sous Windows](/troubleshoot/windows-client/group-policy/create-and-manage-central-store).

7. Ouvrez la stratégie **Activer la protection contre la capture d’écran** et définissez-la sur **Activé**.

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

- Cette fonctionnalité empêche la capture de la fenêtre Bureau à distance via un ensemble spécifique de fonctionnalités de système d’exploitation et d’API publiques. Toutefois, il n’y a aucune garantie que cette fonctionnalité protégera strictement le contenu, par exemple, quand quelqu’un prend une photographie de l’écran.
- Les clients doivent utiliser la fonctionnalité conjointement avec la désactivation de la redirection du presse-papiers, du lecteur et de l’imprimante. La désactivation de la redirection permet d’empêcher l’utilisateur de copier le contenu de l’écran capturé à partir de la session à distance.
- Les utilisateurs ne peuvent pas partager la fenêtre de Bureau à distance à l’aide d’un logiciel de collaboration local, tel que Microsoft Teams, lorsque la fonctionnalité est activée. Si Microsoft Teams est utilisé, l’application Teams locale et l’instance Teams exécutés avec les optimisations des médias ne peuvent pas partager le contenu protégé.
- Lorsque les utilisateurs essaient de se connecter à une ressource protégée avec le client web 1.0.24.14, ils reçoivent une invite d’authentification au lieu du message d’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les meilleures pratiques Azure Virtual Desktop, consultez [Meilleures pratiques de sécurité Azure Virtual Desktop](security-guide.md).
