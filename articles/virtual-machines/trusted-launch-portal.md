---
title: 'Préversion : déployer une machine virtuelle de lancement fiable'
description: Déployez une machine virtuelle qui utilise le lancement fiable.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 15386baa369b18678ef84ebed8144d358fa8177f
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839362"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Déployer une machine virtuelle avec le lancement fiable activé (préversion)

Le [lancement fiable](trusted-launch.md) est un moyen d’améliorer la sécurité des machines virtuelles de [génération 2](generation-2.md). Le lancement fiable protège contre les techniques d’attaque avancées et persistantes en combinant des technologies d’infrastructure comme vTPM et l’amorçage sécurisé.

> [!IMPORTANT]
> Le lancement fiable est actuellement disponible en préversion publique.
>
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
>
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Procéder à un déploiement à l’aide du portail

Créez une machine virtuelle avec le lancement fiable activé.

1. Connectez-vous au [portail Azure](https://aka.ms/TL_preview).
   > [!NOTE]
   > Le lien du portail est propre la préversion de lancement fiable.
   >
2. Recherchez **Machines virtuelles**.
3. Sous **Services**, sélectionnez **Machines virtuelles**.
4. Dans la page **Machines virtuelles**, sélectionnez **Ajouter**, puis **Machine virtuelle**.
5. Sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné.
6. Sous **Groupe de ressources**, sélectionnez **Créer** et spécifiez un nom pour votre groupe de ressources ou sélectionnez un groupe de ressources existant dans la liste déroulante.
7. Sous **Détails de l’instance**, spécifiez un nom pour la machine virtuelle et choisissez une région qui prend en charge le [lancement fiable](trusted-launch.md#public-preview-limitations).
8. Sous **Image**, sélectionnez une [image de génération 2 qui prend en charge le lancement fiable](trusted-launch.md#public-preview-limitations). Vérifiez que le message suivant s’affiche : **This image supports trusted launch preview. Configure in the Advanced tab** (Cette image prend en charge la préversion du lancement fiable. Configurez-la sous l’onglet Avancé).
   > [!TIP]
   > Si vous ne voyez pas la version génération 2 de l’image que vous souhaitez dans la liste déroulante, sélectionnez **Afficher toutes les images** , puis modifiez le filtre de **génération de machine virtuelle** pour afficher uniquement les images de la génération 2. Recherchez l’image dans la liste, puis utilisez la liste déroulante **Sélectionner** pour sélectionner la version génération 2.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Capture d’écran montrant le message qui confirme qu’il s’agit d’une image de génération 2 (Gen2) qui prend en charge le lancement fiable.":::

13. Sélectionnez une taille de machine virtuelle qui prend en charge le lancement fiable. Consultez la liste des [tailles prises en charge](trusted-launch.md#public-preview-limitations).
14. Renseignez les informations du **Compte d’administrateur**, puis les **Règles de port entrant**.
1. Basculez vers l’onglet **Avancé** en le sélectionnant en haut de la page.
1. Descendez jusqu’à la section **Génération de machine virtuelle**. Assurez-vous que **Génération 2** est sélectionné.
1. Toujours sous l’onglet **Avancé**, faites défiler jusqu’à **Lancement fiable**, puis cochez la case **Lancement fiable**. Deux options supplémentaires s’affichent : Amorçage sécurisé et vTPM. Sélectionnez les options appropriées pour votre déploiement.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Capture d’écran montrant les options pour le lancement fiable.":::

15. Au bas de la page, sélectionnez **Vérifier + créer**
16. Sur la page **Créer une machine virtuelle**, vous pouvez voir les détails de la machine virtuelle que vous allez déployer. Lorsque vous êtes prêt, sélectionnez **Créer**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Capture d’écran montrant la page de validation, indiquant que les options de lancement fiable sont incluses.":::


Quelques minutes sont nécessaires pour le déploiement de votre machine virtuelle.

## <a name="deploy-using-a-template"></a>Déployer à l’aide d’un modèle

Vous pouvez déployer des machines virtuelles de lancement fiable à l’aide d’un modèle de démarrage rapide :

**Linux** : [![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows** : [![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Afficher et mettre à jour

Vous pouvez afficher la configuration de lancement fiable pour une machine virtuelle existante en visitant la page **Vue d’ensemble** de la machine virtuelle dans le portail.

Pour modifier la configuration de lancement fiable, dans le menu de gauche, sélectionnez **Configuration** dans la section **Paramètres**. Vous pouvez activer ou désactiver l’amorçage sécurisé et vTPM dans la section **Lancement fiable**. Lorsque vous avez terminé, sélectionnez **Enregistrer** en haut de la page.

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Capture d’écran montrant comment modifier la configuration de lancement fiable.":::

Si la machine virtuelle est en cours d’exécution, vous recevrez un message indiquant que la machine virtuelle sera redémarrée pour appliquer la configuration de lancement fiable modifiée. Sélectionnez **Oui**, puis attendez que la machine virtuelle redémarre pour que les modifications prennent effet.


## <a name="verify-secure-boot-and-vtpm"></a>Vérifier l’amorçage sécurisé et vTPM

Vous pouvez vérifier que l’amorçage sécurisé et vTPM sont activés sur la machine virtuelle.

### <a name="linux-validate-if-secure-boot-is-running"></a>Linux : vérifier que l’amorçage sécurisé est en cours d’exécution

Connectez-vous avec SSH à la machine virtuelle et exécutez la commande suivante :

```bash
mokutil --sb-state
```

Si l’amorçage sécurisé est activé, la commande retourne :

```bash
SecureBoot enabled
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux : vérifier que vTPM est activé

Connectez-vous avec SSH à votre machine virtuelle. Vérifiez si l’appareil tpm0 est présent :

```bash
ls /dev/tpm0
```

Si vTPM est activé, la commande retourne :

```output
/dev/tpm0
```

Si vTPM est désactivé, la commande retourne :

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows : vérifier que l’amorçage sécurisé est en cours d’exécution

Connectez-vous à la machine virtuelle à l’aide du Bureau à distance, puis exécutez `msinfo32.exe`.

Dans le volet de droite, vérifiez que l’état de l’amorçage sécurisé est **Activé**.

## <a name="enable-the-azure-security-center-experience"></a>Activer l’expérience Azure Security Center

Pour permettre à Azure Security Center d’afficher des informations sur vos machines virtuelles de lancement fiable, vous devez activer plusieurs stratégies. Le moyen le plus simple d’activer des stratégies consiste à déployer ce [modèle Resource Manager](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) dans votre abonnement.

Sélectionnez le bouton ci-dessous pour déployer les stratégies dans votre abonnement :

[![Déployer sur Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Le modèle ne doit être déployé qu’une seule fois par abonnement. Il installe automatiquement les extensions `GuestAttestation` et `AzureSecurity` sur toutes les machines virtuelles prises en charge. Si vous recevez des erreurs, essayez de redéployer le modèle.

Pour obtenir des recommandations sur vTPM et l’amorçage sécurisé pour les machines virtuelles de lancement fiable, consultez [Ajouter une initiative personnalisée à votre abonnement](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription).

## <a name="sign-things-for-secure-boot-on-linux"></a>Signer des éléments pour l’amorçage sécurisé sur Linux

Dans certains cas, vous devrez peut-être signer des éléments pour l’amorçage sécurisé UEFI.  Par exemple, vous devrez peut-être suivre [la procédure de signature d’éléments pour l’amorçage sécurisé](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) pour Ubuntu. Dans ce cas, vous devez entrer les clés d’inscription de l’utilitaire MOK pour votre machine virtuelle. Pour ce faire, vous devez utiliser la console série Azure pour accéder à l’utilitaire MOK.

1. Activez la console série Azure pour Linux. Pour plus d’informations, consultez [Console série pour Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez **Machines virtuelles** et sélectionnez votre machine virtuelle dans la liste.
1. Dans le menu de gauche, sous **Support + dépannage**, sélectionnez **Console série**. Une page s’ouvre à droite, avec la console série.
1. Connectez-vous à la machine virtuelle à l’aide de la console série Azure. Pour **connexion**, entrez le nom d’utilisateur que vous avez utilisé lors de la création de la machine virtuelle. Par exemple, *azureuser*. Lorsque vous y êtes invité, entrez le mot de passe associé au nom d’utilisateur.
1. Une fois que vous êtes connecté, utilisez `mokutil` pour importer le fichier `.der` de clé publique.

    ```bash
    sudo mokutil –import <path to public key.der>
    ```
1. Redémarrez la machine à partir de la console série Azure en tapant `sudo reboot`. Un compte à rebours de 10 secondes démarre.
1. Appuyez sur la touche vers le haut ou le bas pour interrompre le compte à rebours et attendre en mode console UEFI. Si le minuteur n’est pas interrompu, le processus d’amorçage continue et toutes les modifications MOK sont perdues.
1. Sélectionnez l’action appropriée dans le menu de l’utilitaire MOK.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Capture d’écran montrant les options disponibles dans le menu gestion MOK de la console série.":::


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [lancement fiable](trusted-launch.md) et les machines virtuelles [Gen 2](generation-2.md).
