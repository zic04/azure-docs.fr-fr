---
title: Créer un appareil simulé personnalisé - Azure | Microsoft Docs
description: Dans ce tutoriel, vous utilisez la Simulation d’appareil pour créer un appareil simulé personnalisé à utiliser dans des simulations.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 7ddd1aa9865cb4061212ce77b31efc82ad339283
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165568"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Tutoriel : Créer un appareil simulé personnalisé

Dans ce tutoriel, vous utilisez la Simulation d’appareil pour créer un appareil simulé personnalisé à utiliser dans des simulations. Pour commencer la Simulation d’appareil, vous pouvez utiliser un des exemples d’appareils simulés inclus. Vous pouvez également créer des appareils simulés personnalisés comme décrit dans cet article. Pour plus d’options de personnalisation, consultez [Créer un modèle d’appareil avancé](iot-accelerators-device-simulation-advanced-device.md).

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Afficher une liste de vos modèles d’appareils simulés
> * Créer un appareil simulé personnalisé
> * Cloner un modèle d’appareil
> * Supprimer un modèle d’appareil

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de la simulation d’appareil dans votre abonnement Azure.

Si vous n’avez pas encore déployé la Simulation d’appareil, vous devez terminer le guide de démarrage rapide [Déployer et exécuter une simulation d’appareil IoT dans Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Ouvrir la Simulation d’appareil

Pour exécuter la Simulation d’appareil dans votre navigateur, accédez d’abord aux [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com).

Vous pouvez être invité à vous connecter les informations d’identification de votre abonnement Azure.

Puis cliquez sur **Lancer** sur la vignette de la Simulation d’appareil que vous avez déployée dans le guide de démarrage rapide [Déployer et exécuter une simulation d’appareil IoT dans Azure](quickstart-device-simulation-deploy.md).

## <a name="view-your-device-models"></a>Afficher vos modèles d’appareils

Sélectionnez **Modèles d’appareils** dans la barre de menus. La page **Modèles d’appareils** répertorie tous les modèles d’appareils disponibles dans cette instance de Simulation d’appareil :

![Modèles d’appareils](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Créer un modèle d’appareil

Cliquez sur **+ Ajouter des modèles d’appareils** dans l’angle supérieur droit de la page :

![Ajouter un modèle d’appareil](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

Dans ce tutoriel, vous créez un réfrigérateur simulé qui envoie des données de température et d’humidité.

Renseignez le formulaire avec les informations suivantes :

| Paramètre             | Valeur                                                |
| ------------------- | ---------------------------------------------------- |
| Nom du modèle d’appareil   | Réfrigérateur                                         |
| Description du modèle   | Un réfrigérateur avec des capteurs de température et d’humidité |
| Version             | 1.0                                                  |

> [!NOTE]
> Le nom du modèle d’appareil doit être unique.

Cliquez sur **+ Ajouter un point de données** pour ajouter des points de données de température et d’humidité avec les valeurs suivantes :

| Point de données          | Comportement        | Valeur minimale | Valeur maximale | Unité |
| ------------------- | --------------- | --------- | --------- | ---- |
| Température         | Aléatoire          | \- 50       | 100       | F    |
| Humidité            | Aléatoire          | 0         | 100       | %    |

Cliquez sur **Enregistrer** pour enregistrer le modèle d’appareil.

![Créer un modèle d’appareil](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Votre réfrigérateur est désormais inclus dans la liste des modèles d’appareils. Vous devrez peut-être cliquer sur **Suivant** pour accéder à une autre page afin de voir votre réfrigérateur.

## <a name="clone-a-device-model"></a>Cloner un modèle d’appareil

Le clonage d’un modèle d’appareil vous permet de créer une copie d’un modèle d’appareil existant. Vous pouvez ensuite modifier la copie en fonction de vos besoins spécifiques. Le clonage fait gagner du temps lorsque vous devez créer des modèles d’appareils similaires.

Pour cloner un modèle d’appareil, cochez la case en regard du modèle, puis cliquez sur **Cloner** dans la barre d’action :

![Capture d’écran avec mise en évidence du modèle sélectionné et du bouton Cloner.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Supprimer un modèle d’appareil

Vous pouvez supprimer les modèles d’appareil personnalisés. Pour supprimer un modèle d’appareil, cochez la case en regard du modèle, puis cliquez sur **Supprimer** dans la barre d’action :

![Supprimer un modèle d’appareil](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer, cloner et supprimer des modèles d’appareils personnalisés. Pour en savoir plus sur les modèles d’appareils, consultez l’article de procédure suivant :

> [!div class="nextstepaction"]
> [Créer un modèle d’appareil avancé](iot-accelerators-device-simulation-advanced-device.md)