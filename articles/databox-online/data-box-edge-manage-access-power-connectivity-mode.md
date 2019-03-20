---
title: Accès des appareils Microsoft Azure données boîte Edge, de puissance et de mode de connectivité | Microsoft Docs
description: Décrit comment gérer l’accès, de puissance et de mode de connectivité pour l’appareil de périphérie de zone de données Azure que vous aide à transférer des données vers Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e0aa3468bda9f904d62e9e20545ac5f990cef521
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905312"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge-preview"></a>Gérer l’accès, de puissance et de mode de connectivité pour votre zone de données Azure Edge (version préliminaire)

Cet article décrit comment gérer le mode d’accès, de puissance et de connectivité pour votre zone de données Azure Edge. Ces opérations sont effectuées sur l’interface utilisateur web locale ou le Portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Gérer l’accès aux appareils
> * Gérer le mode de connectivité
> * Gérer la puissance

> [!IMPORTANT]
> Data Box Edge est en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution.

## <a name="manage-device-access"></a>Gérer l’accès aux appareils

L’accès à votre appareil Edge de zone de données est contrôlé par l’utilisation d’un mot de passe d’administrateur. Ce mot de passe est modifiable sur l’interface utilisateur web locale. Vous pouvez également le réinitialiser sur le Portail Azure.

### <a name="change-device-administrator-password"></a>Modifier le mot de passe d’administrateur de l’appareil

Suivez ces étapes sur l’interface utilisateur locale pour modifier le mot de passe d’administrateur de l’appareil.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Modification du mot de passe**.
2. Entrez le mot de passe actuel, puis le nouveau. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Confirmez le nouveau mot de passe.

    ![Modifier le mot de passe](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Sélectionnez **Modifier le mot de passe**.
 
### <a name="reset-device-administrator-password"></a>Réinitialiser le mot de passe d'administrateur de l'appareil

Le workflow de réinitialisation n’implique pas de se rappeler l’ancien mot de passe, ce qui est utile en cas de perte. Il s’effectue sur le Portail Azure.

1. Sur le Portail Azure, accédez à **Vue d’ensemble > Réinitialiser le mot de passe d’administrateur**.

    ![Réinitialiser le mot de passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Entrez le nouveau mot de passe, puis confirmez-le. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Sélectionnez **réinitialiser**.

    ![Réinitialiser le mot de passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gérer le mode de connectivité

Mis à part le mode totalement connecté par défaut, votre appareil peut également exécuter en mode partiellement connecté ou déconnecté entièrement. :

- **Entièrement connecté** -c’est le mode normal par défaut dans lequel l’appareil fonctionne. Le téléchargement de cloud et le téléchargement de données est activée dans ce mode. Vous pouvez utiliser le portail Azure ou l’interface utilisateur web locale pour gérer l’appareil.

- **Partiellement déconnecté** – Dans ce mode, l’appareil ne peut pas charger de données sur les partages. Il est en revanche possible de le gérer sur le Portail Azure.

    Ce mode est généralement utilisé sur un réseau satellite limité, dans l’objectif de réduire la consommation de bande passante réseau. Une consommation réseau minimale peut subsister pour les opérations de monitoring des appareils.

- **Déconnecté** – Dans ce mode, l’appareil est entièrement déconnecté du cloud ; les chargements et téléchargements cloud sont désactivés. L’appareil n’est gérable que sur l’interface utilisateur web locale.

    Ce mode est généralement utilisé pour mettre l’appareil hors connexion.

Pour modifier le mode de l’appareil, suivez ces étapes :

1. Sur l’interface utilisateur web locale de votre appareil, accédez à **Configuration > Paramètres cloud**.
2. Dans la liste déroulante, sélectionnez le mode que vous souhaitez faire fonctionner le périphérique dans. Vous pouvez sélectionner à partir de **entièrement connectée**, **partiellement connecté**, et **entièrement déconnecté**. Pour exécuter l’appareil en mode partiellement déconnecté, activez **Gestion sur le Portail Azure**.

    ![Mode de connectivité](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gérer la puissance

Vous pouvez arrêter ou redémarrer votre appareil physique à l’aide de l’interface utilisateur web locale. Nous recommandons d’avant de redémarrer, mettre hors connexion les partages sur le serveur de données, puis sur l’appareil. Cette action réduit les risques d’altération des données.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Paramètres d'alimentation**.
2. Sélectionnez **arrêt** ou **redémarrer** selon ce que vous voulez faire.

    ![Paramètres d’alimentation](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Lorsque vous êtes invité à confirmer l’opération, sélectionnez **Oui** pour continuer.

> [!NOTE]
> Si vous arrêtez l’appareil physique, vous devez actionner le bouton d’alimentation sur l’appareil pour l’activer.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer les partages](data-box-edge-manage-shares.md).