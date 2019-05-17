---
title: Configurer votre environnement de développement Azure Red Hat OpenShift | Microsoft Docs
description: Voici les conditions préalables pour travailler avec Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: définie le programme d’installation de Red hat openshift
author: TylerMSFT
ms.author: twhitney
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 6e859f57f9b5f24ea2f0172f5aa35a60d9769f19
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551690"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurer votre environnement de développement Azure Red Hat OpenShift

Pour générer et exécuter des applications de Microsoft Azure Red Hat OpenShift, vous devrez :

* Acheter des instances de machine virtuelle réservée.
* Installez la version 2.0.65 (ou version ultérieure) de Azure CLI (ou d’utilisation Azure Cloud Shell).
* Inscrivez-vous à la `openshiftmanagedcluster` fonctionnalité et les fournisseurs de ressources associé.
* Créer un locataire Azure Active Directory (Azure AD).
* Créer un objet d’application Azure AD.
* Créer un utilisateur Azure AD.

Les instructions suivantes vous guidera à tous ces composants requis.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Acheter des instances de machine virtuelle réservée

Avant de pouvoir utiliser Azure Red Hat OpenShift, vous devez acheter des instances de machine virtuelle réservée.

Si vous êtes un client Azure, ici façon [instances réservées de machine virtuelle d’achat](https://aka.ms/openshift/buy). Une réservation permet de réduire vos dépenses en prépayant des services Azure entièrement gérés. Reportez-vous à [ *quelles sont les réservations Azure* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) pour en savoir plus sur les réservations et comment ils économiser de l’argent.

Si vous n’êtes pas un client Azure, [contacter le service commercial](https://aka.ms/openshift/contact-sales) et remplissez le formulaire de vente en bas de la page pour démarrer le processus.

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure

Azure Red Hat OpenShift requiert la version 2.0.65 ou une version ultérieure de l’interface CLI Azure. Si vous avez déjà installé l’interface CLI, vous pouvez vérifier la version que vous avez en exécutant :

```bash
az --version
```

La première ligne de sortie auront la version CLI, par exemple `azure-cli (2.0.65)`.

Voici des instructions pour [l’installation de l’interface CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) si vous avez besoin d’une nouvelle installation ou une mise à niveau.

Vous pouvez également utiliser le [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Lorsque vous utilisez Azure Cloud Shell, veillez à sélectionner le **Bash** environnement si vous envisagez de suivre le [créer et gérer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) série de didacticiels.

## <a name="register-providers-and-features"></a>Inscrire des fournisseurs et des fonctionnalités

Le `Microsoft.ContainerService openshiftmanagedcluster` fonctionnalité, `Microsoft.Solutions`, et `Microsoft.Network` fournisseurs doivent être inscrit à votre abonnement manuellement avant de déployer votre premier cluster Azure Red Hat OpenShift.

Pour enregistrer manuellement ces fonctionnalités et les fournisseurs, utilisez les instructions suivantes à partir d’un interpréteur de commandes Bash, si vous avez installé l’interface CLI, ou à partir de la session Azure Cloud Shell (Bash) dans votre portail Azure :

1. Si vous avez plusieurs abonnements Azure, spécifiez l’ID de l’abonnement approprié :

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Inscrire la fonctionnalité d’openshiftmanagedcluster Microsoft.ContainerService :

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Inscrivez le fournisseur Microsoft.Solutions :

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Inscrivez le fournisseur Microsoft.Network :

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Inscrivez le fournisseur Microsoft.KeyVault :

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

6. Actualiser l’inscription du fournisseur de ressources Microsoft.ContainerService :

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Créer un locataire Azure Active Directory (Azure AD)

Le service Azure Red Hat OpenShift nécessite un locataire Azure Active Directory (Azure AD) associé qui représente votre organisation et sa relation à Microsoft. Votre client Azure AD vous permet d’enregistrer, générer et gérer les applications, comme et utiliser d’autres services Azure.

Si vous n’avez pas un compte Azure AD à utiliser en tant que le locataire pour votre cluster Azure Red Hat OpenShift, ou si vous souhaitez créer un locataire de test, suivez les instructions de [créer un locataire Azure AD pour votre cluster Azure Red Hat OpenShift](howto-create-tenant.md) avant continuer avec ce guide.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Créer un utilisateur Azure AD, groupe de sécurité et application objet

Azure Red Hat OpenShift exige des autorisations pour effectuer des tâches sur votre cluster, telles que la configuration de stockage. Ces autorisations sont représentées par un [principal du service](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Vous allez également créer un utilisateur Active Directory pour tester des applications en cours d’exécution sur votre cluster Azure Red Hat OpenShift.

Suivez les instructions de [créer un objet d’application Azure AD et un utilisateur](howto-aad-app-configuration.md) pour créer un principal de service, générer une URL client secret et l’authentification de rappel pour votre application et créer un nouveau groupe de sécurité Azure AD et l’utilisateur à accéder à la Grappe.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à utiliser Azure Red Hat OpenShift !

Essayez le didacticiel :
> [!div class="nextstepaction"]
> [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli