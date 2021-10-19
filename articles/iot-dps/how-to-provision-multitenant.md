---
title: Comment approvisionner des appareils pour une mutualisation dans le service IoT Hub Device Provisioning Azure
description: Guide pratique pour provisionner des appareils multilocataires avec votre instance DPS (Device Provisioning Service)
author: anastasia-ms
ms.author: v-stharr
ms.topic: how-to
ms.date: 10/02/2021
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 595bd473bb70f4c23554998b7240e4ef0ed1183d
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670283"
---
# <a name="how-to-provision-for-multitenancy"></a>Comment provisionner des appareils multilocataires 

Cet article explique comment provisionner de façon sécurisée plusieurs appareils simulés dotés de clés symétriques sur un groupe de hubs IoT à l’aide d’une [stratégie d’allocation](concepts-service.md#allocation-policy). Les stratégies d’allocation définies par le service Device Provisioning permettent de gérer divers scénarios d’allocation. Voici deux scénarios courants :

* **Géolocalisation et géolatence** : quand un appareil change souvent d’emplacement, il doit être provisionné dans le hub IoT le plus proche de son emplacement pour améliorer la latence du réseau. Dans ce scénario, un groupe de hubs IoT couvrant plusieurs régions sont sélectionnés pour les inscriptions. La stratégie d’allocation **Latence la plus faible** est sélectionnée pour ces inscriptions. Avec cette stratégie, le service Device Provisioning évalue la latence des appareils et détermine quel hub IoT dans le groupe de hubs IoT est le plus proche.

* **Multilocation** : les appareils utilisés dans une solution IoT doivent parfois être assignés à un hub IoT ou un groupe de hubs IoT spécifique. La solution peut nécessiter que tous les appareils d’un locataire particulier communiquent avec un groupe spécifique de hubs IoT. Dans certains cas, un locataire peut avoir ses propres hubs IoT et exiger que les appareils soient assignés à ses hubs IoT.

Il est courant de combiner ces deux scénarios. Par exemple, une solution IoT multilocataire assigne généralement ses appareils locataires dans un groupe de hubs IoT répartis entre différentes régions. Ces appareils locataires peuvent être assignés au hub IoT qui, dans ce groupe, a la latence la plus faible par rapport à l’emplacement géographique.

Cet article utilise un exemple d’appareil simulé du [SDK Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) afin de montrer comment provisionner des appareils dans un scénario de solution multilocataire entre plusieurs régions. Dans cet article, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Utiliser Azure CLI pour créer deux hubs IoT régionaux (**USA Ouest 2** et **USA Est**)
> * Créer une inscription multilocataire
> * Utiliser Azure CLI pour créer deux machines virtuelles Linux régionales jouant le rôle d’appareils dans les mêmes régions (**USA Ouest 2** et **USA Est**)
> * Configurer l’environnement de développement nécessaire au SDK Azure IoT pour C sur les deux machines virtuelles Linux
> * Simuler les appareils afin de vérifier qu’ils sont provisionnés pour le même locataire dans la région la plus proche

>[!IMPORTANT]
> Certaines régions peuvent, de temps en temps, imposer des restrictions sur la création de machines virtuelles. Au moment de la rédaction de ce guide, les régions *westus2* et *eastus* permettaient la création de machines virtuelles. Si vous ne parvenez pas à créer dans l’une de ces régions, vous pouvez essayer une autre région. Pour en savoir plus sur le choix des régions géographiques Azure lors de la création de machines virtuelles, consultez [Régions pour machines virtuelles dans Azure](../virtual-machines/regions.md).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

* Effectuez les étapes décrites dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Créer deux hubs IoT régionaux

Dans cette section, vous allez créer un groupe de ressources Azure et deux nouvelles ressources régionales IoT Hub pour un locataire. Un hub IoT est destiné à la région **USA Ouest 2** et l’autre à la région **USA Est**.

>[!IMPORTANT]
>Il est recommandé d’utiliser le même groupe de ressources pour toutes les ressources créées dans cet article. Cela facilitera la suppression des ressources quand vous aurez terminé l’article.

1. Dans Azure Cloud Shell, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create) suivante :

    ```azurecli-interactive
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Créez un hub IoT à l’emplacement *eastus*, puis ajoutez-le au groupe de ressources que vous avez créé à l’aide de la commande [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) suivante (remplacez `{unique-hub-name}` par votre propre nom unique) :

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Cette commande peut prendre plusieurs minutes.

3. Maintenant, créez un hub IoT à l’emplacement *westus2*, puis ajoutez-le au groupe de ressources que vous avez créé à l’aide de la commande [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) suivante (remplacez `{unique-hub-name}` par votre propre nom unique) :

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location westus2 --sku S1
    ```

    Cette commande peut prendre plusieurs minutes.

## <a name="create-the-multitenant-enrollment"></a>Créer l’inscription multilocataire

Dans cette section, vous allez créer un groupe d’inscriptions pour les appareils locataires.  

Par souci de simplicité, cet article utilise [l’attestation de clé symétrique](concepts-symmetric-key-attestation.md) avec l’inscription. Pour sécuriser votre solution, utilisez plutôt [l’attestation de certificat X.509](concepts-x509-attestation.md) avec une chaîne d’approbation.

1. Dans le portail Azure, sélectionnez votre service Device Provisioning.

2. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

3. Sélectionnez **+ Ajouter un groupe d’inscriptions**.

4. Dans la page **Ajouter un groupe d’inscriptions**, entrez les informations suivantes :

    **Nom du groupe** : entrez *contoso-us-devices*.

    **Type d’attestation** : sélectionnez *Clé symétrique*.

    **Générer automatiquement les clés** : cette case est normalement déjà cochée.

    **Sélectionner le mode d’affectation des appareils aux hubs** : sélectionnez *Latence la plus faible*.

5. Sélectionnez **Lier un nouveau hub IoT**.

    :::image type="content" source="./media/how-to-provision-multitenant/create-multitenant-enrollment.png" alt-text="Ajoutez un groupe d’inscriptions multilocataire pour l’attestation de clé symétrique":::.

6. Dans la page **Ajouter un lien au hub IoT**, entrez les informations suivantes :

    **Abonnement** : si vous avez plusieurs abonnements, sélectionnez celui où vous avez créé les hubs IoT régionaux.

    **IoT Hub** : sélectionnez le hub IoT que vous avez créé pour l’emplacement *eastus*.

    **Stratégie d’accès** : sélectionnez *iothubowner*.

    :::image type="content" source="./media/how-to-provision-multitenant/link-regional-hubs.png" alt-text="Liez les hubs IoT régionaux au service de provisionnement":::.

7. Sélectionnez **Enregistrer**.

8. Répétez les étapes 5 à 7 pour le deuxième hub IoT que vous avez créé pour l’emplacement *westus*.

9. Sélectionnez les deux hubs IoT que vous avez créés dans la liste déroulante **Sélectionner les hubs IoT auxquels ce groupe peut être attribué**.

    :::image type="content" source="./media/how-to-provision-multitenant/enrollment-regional-hub-group.png" alt-text="Sélectionnez les hubs IoT liés.":::

10. Sélectionnez **Enregistrer**.

11. Sélectionnez *contoso-us-devices* dans la liste des groupes d’inscriptions.

12. Copiez la *clé primaire*. Cette clé primaire servira ultérieurement à générer des clés uniques pour les deux appareils simulés.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-primary-key.png" alt-text="Copiez la clé primaire.":::

## <a name="create-regional-linux-vms"></a>Créer des machines virtuelles Linux régionales

Dans cette section, vous allez créer deux machines virtuelles Linux régionales. Ces machines virtuelles exécutent un exemple de simulation d’appareils de deux régions afin d’illustrer le provisionnement d’appareils locataires dans chaque région.

Pour faciliter la suppression des ressources à la fin de l’article, nous ajoutons ces machines virtuelles au même groupe de ressources où vous avez créé les hubs IoT, à savoir *contoso-us-resource-group*. Toutefois, nous exécutons les machines virtuelles dans des régions distinctes (**USA Ouest 2** et **USA Est**).

1. Dans Azure Cloud Shell, exécutez la commande ci-dessous pour créer une machine virtuelle dans la région **USA Est**, après avoir modifié les paramètres de la commande de la façon suivante :

    **--name** : entrez un nom unique pour la machine virtuelle de la région **USA Est**.

    **--admin-username** : utilisez votre propre nom d’utilisateur administrateur.

    **--admin-password** : utilisez votre propre mot de passe d’administrateur.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    Cette commande prend plusieurs minutes. 

2. Une fois la commande exécutée, copiez la valeur **publicIpAddress** de la machine virtuelle de la région USA Est.

3. Dans Azure Cloud Shell, exécutez la commande ci-dessous pour créer une machine virtuelle dans la région **USA Ouest 2**, après avoir modifié les paramètres de la commande de la façon suivante :

    **--name** : entrez un nom unique pour la machine virtuelle de la région **USA Ouest 2**. 

    **--admin-username** : utilisez votre propre nom d’utilisateur administrateur.

    **--admin-password** : utilisez votre propre mot de passe d’administrateur.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest2 \
    --location westus2 \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    Cette commande prend plusieurs minutes.

4. Une fois la commande exécutée, copiez la valeur **publicIpAddress** de la machine virtuelle de la région USA Ouest 2.

5. Ouvrez deux interpréteurs en ligne de commande.

6. Connectez-vous à l’une des machines virtuelles régionales dans chaque interpréteur à l’aide de SSH.

    Dans SSH, entrez votre nom d’utilisateur administrateur ainsi que l’adresse IP publique que vous avez copiée comme paramètres dans SSH. Entrez le mot de passe d’administrateur lorsque vous y êtes invité.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Préparer l’environnement de développement nécessaire au SDK Azure IoT pour C

Dans cette section, vous allez cloner le SDK Azure IoT pour C sur chaque machine virtuelle. Le SDK contient un exemple qui simule le provisionnement d’appareils locataires dans chaque région.

Pour chaque machine virtuelle :

1. Installez **CMake**, **g++** , **gcc** et [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) à l’aide des commandes suivantes :

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

2. Recherchez et copiez le nom d’étiquette de la [version la plus récente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) du SDK.

3. Clonez le [SDK Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) sur les deux machines virtuelles.  Utilisez l’étiquette obtenue à l’étape précédente comme valeur pour le paramètre `-b` :

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Attendez-vous à ce que cette opération prenne plusieurs minutes.

4. Créez un dossier **cmake** dans le référentiel et accédez à ce dossier.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

5. Exécutez la commande suivante qui génère une version du SDK propre à votre plateforme cliente de développement :

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

6. Une fois la génération terminée, les dernières lignes de sortie doivent ressembler à la sortie suivante :

    ```bash
    -- IoT Client SDK Version = 1.7.0
    -- Provisioning SDK Version = 1.7.0
    -- Looking for include file stdint.h
    -- Looking for include file stdint.h - found
    -- Looking for include file stdbool.h
    -- Looking for include file stdbool.h - found
    -- target architecture: x86_64
    -- Performing Test CXX_FLAG_CXX11
    -- Performing Test CXX_FLAG_CXX11 - Success
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so (found version "7.58.0")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/azure-iot-sdk-c
    ```

## <a name="derive-unique-device-keys"></a>Dériver les clés d’appareil uniques

Quand vous utilisez l’attestation de clé symétrique avec des inscriptions de groupe, vous ne vous servez pas directement des clés du groupe d’inscriptions. Au lieu de cela, vous dérivez une clé unique à partir de la clé de groupe d’inscriptions pour chaque appareil. Pour plus d’informations, consultez [Inscriptions de groupe avec des clés symétriques](concepts-symmetric-key-attestation.md#group-enrollments).

Dans cette partie du tutoriel, vous allez générer une clé d’appareil à partir de la clé principale de votre groupe pour calculer un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription unique de l’appareil. Le résultat est ensuite converti au format Base64.

>[!IMPORTANT]
>N’incluez pas votre clé principale de groupe dans le code de l’appareil.

Pour **les** appareils *eastus* et *westus 2* :

1. Générez votre clé unique à l’aide d’**openssl**. Vous allez utiliser le script d’interpréteur de commandes Bash suivant (remplacez `{primary-key}` par la **clé primaire** du groupe d’inscriptions que vous avez copiée précédemment et remplacez `{contoso-simdevice-east}` par votre propre ID d’inscription unique pour chaque appareil. Utilisez des caractères alphanumériques en minuscules et des tirets (« - ») pour définir les deux ID) :

    ```bash
    KEY={primary-key}
    REG_ID={contoso-simdevice}
    
    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
    ```

2. Le script va générer la clé suivante :

    ```bash
    p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
    ```

3. Maintenant, chaque appareil de locataire a sa propre clé d’appareil dérivée et un ID d’inscription unique pour effectuer l’attestation de clé symétrique avec le groupe d’inscriptions lors du processus de provisionnement.

## <a name="simulate-the-devices-from-each-region"></a>Simuler les appareils de chaque région

Dans cette section, vous allez mettre à jour un exemple de provisionnement du SDK Azure IoT pour C sur les deux machines virtuelles régionales. 

L’exemple de code simule une séquence de démarrage d’un appareil qui envoie la demande de provisionnement à votre instance du service Device Provisioning. La séquence de démarrage entraîne la reconnaissance de l’appareil et son attribution au hub IoT le plus proche en fonction de la latence.

1. Dans le portail Azure, sélectionnez l’onglet **Vue d’ensemble** de votre service Device Provisioning et notez la valeur **_Étendue de l’ID_**.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-id-scope.png" alt-text="Extraction des informations de point de terminaison du service Device Provisioning à partir du panneau du Portail":::

2. Sur les deux machines virtuelles à modifier, ouvrez **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c**.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

3. Sur les deux machines virtuelles, recherchez la constante `id_scope` et remplacez la valeur par la valeur **Étendue de l’ID** que vous avez copiée précédemment. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Sur les deux machines virtuelles, recherchez la définition de la fonction `main()` dans le même fichier. Assurez-vous que la variable `hsm_type` est définie sur `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, comme dans l’exemple ci-dessous, pour correspondre à la méthode d’attestation du groupe d’inscription. 

    Enregistrez vos modifications dans les fichiers sur les deux machines virtuelles.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Sur les deux machines virtuelles, recherchez l’appel à `prov_dev_set_symmetric_key_info()` dans **prov\_dev\_client\_sample.c**, qui est placé en commentaire.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Décommentez l’appel de fonction et remplacez les valeurs des espaces réservés (y compris les crochets) par les ID d’inscription uniques et les clés d’appareil dérivées pour chaque appareil issues de la section précédente. Les clés montrées ci-dessous sont données seulement à des fins d’exemple. Utilisez les clés que vous avez générées précédemment.

    USA Est :
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA Ouest :
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

6. Sur les deux machines virtuelles, enregistrez le fichier.

7. Sur les deux machines virtuelles, accédez au dossier d’exemple ci-dessous et générez l’exemple.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

8. Une fois l’exemple généré, exécutez **prov\_dev\_client\_sample.exe** sur les deux machines virtuelles pour simuler un appareil locataire de chaque région. Notez que chaque appareil est alloué à l’hub IoT locataire le plus proche des régions de l’appareil simulé.

    Exécutez la simulation :
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Exemple de sortie de la machine virtuelle USA Est :

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Exemple de sortie de la machine virtuelle USA Ouest :
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de continuer à utiliser les ressources créées dans cet article, vous pouvez les conserver. Sinon, effectuez les étapes suivantes pour supprimer toutes les ressources qui ont été créées dans le cadre de cet article, et ainsi éviter des frais inutiles.

Les étapes décrites supposent que vous avez créé toutes les ressources utilisées dans cet article dans le même groupe de ressources nommé **contoso-us-resource-group**, comme nous vous l’avions demandé.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé l’IoT Hub à l’intérieur d’un groupe de ressources existant qui concerne des ressources que vous souhaitez conserver, supprimer uniquement la ressource de l’ IoT Hub au lieu de supprimer le groupe de ressources.
>

Pour supprimer le groupe de ressources par nom :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **Groupes de ressources**.

3. Dans la zone de texte **Filtrer par nom...**, tapez le nom du groupe de ressources contenant vos ressources (ici, **contoso-us-resource-group**).

4. À droite de votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

5. Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez à nouveau le nom de votre groupe de ressources pour confirmer, puis sélectionnez **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le réapprovisionnement, consultez :

> [!div class="nextstepaction"]
> [Concepts du reprovisionnement d’appareils IoT Hub](concepts-device-reprovision.md)

* Pour en savoir plus sur la suppression des privilèges d'accès, consultez
> [!div class="nextstepaction"]
> [Guide pratique pour déprovisionner des appareils auparavant approvisionnés automatiquement](how-to-unprovision-devices.md)