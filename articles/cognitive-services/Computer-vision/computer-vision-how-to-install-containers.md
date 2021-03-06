---
title: Guide pratique pour installer et exécuter des conteneurs
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Vision par ordinateur.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 2ba7039fe42e3b5638b99161e12e9888bc852f87
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634903"
---
# <a name="install-and-run-containers"></a>Installer et exécuter des conteneurs

La mise en conteneur est une méthode de distribution de logiciels dans laquelle une application ou un service est packagé en tant qu’image conteneur. La configuration et les dépendances pour l’application ou le service sont inclus dans l’image conteneur. L’image conteneur peut ensuite être déployée sur un hôte conteneur avec peu ou pas de modifications. Les conteneurs sont isolés les uns des autres et du système d’exploitation sous-jacent, avec une empreinte inférieure à celle d’une machine virtuelle. Vous pouvez instancier des conteneurs à partir d’images conteneurs pour les tâches à court terme et les supprimer quand vous n’en avez plus besoin.

Le composant Reconnaître le texte de Vision par ordinateur est également disponible en tant que conteneur Docker. Il permet de détecter et d’extraire un texte imprimé à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches et des cartes de visite.  
> [!IMPORTANT]
> Le conteneur Reconnaître le texte ne fonctionne qu’en anglais pour le moment.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="preparation"></a>Préparation

L’utilisation du conteneur Reconnaître le texte est soumise aux prérequis suivants :

**Moteur docker** : le moteur Docker doit être installé localement. Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) et [Windows](https://docs.docker.com/docker-for-windows/). Sur Windows, vous devez configurer Docker pour prendre en charge les conteneurs Linux. Les conteneurs docker peuvent également être déployés directement sur [Azure Kubernetes Service](/azure/aks/), sur [Azure Container Instances](/azure/container-instances/) ou sur un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](/azure/azure-stack/). Pour plus d’informations sur le déploiement de Kubernetes sur Azure Stack, consultez [Déployer Kubernetes sur Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation.

**Connaissance de Microsoft Container Registry et de Docker** : vous devez avoir une compréhension élémentaire des concepts de Microsoft Container Registry et de Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.  

Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Exigences et recommandations relatives au serveur

Le conteneur Reconnaître le texte nécessite au minimum 1 cœur de processeur d’au moins 2,6 gigahertz (GHz) et 8 gigaoctets (Go) de mémoire allouée, mais nous vous recommandons d’utiliser au moins 2 cœurs de processeur et 8 Go de mémoire allouée.

## <a name="request-access-to-the-private-container-registry"></a>Demander l’accès au registre de conteneurs privé

Vous devez d’abord remplir et envoyer le [formulaire de demande de conteneurs Vision Cognitive Services ](https://aka.ms/VisionContainersPreview) pour demander l’accès au conteneur Reconnaître le texte. Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. À la réception du formulaire, l’équipe Azure Cognitive Services vérifie que vous remplissez bien les critères d’accès au registre de conteneurs privé.

> [!IMPORTANT]
> Vous devez utiliser une adresse e-mail associée à un compte Microsoft (MSA) ou à un compte Azure Active Directory (Azure AD) dans le formulaire.

Si votre demande est approuvée, vous recevez un e-mail contenant des instructions relatives à l’obtention de vos informations d’identification et à l’accès au registre de conteneurs privé.

## <a name="create-a-computer-vision-resource-on-azure"></a>Créer une ressource Vision par ordinateur sur Azure

Vous devez créer une ressource Vision par ordinateur sur Azure pour utiliser le conteneur Reconnaître le texte. Après avoir créé la ressource, utilisez la clé d’abonnement et l’URL de point de terminaison de la ressource pour instancier le conteneur. Pour plus d’informations sur l’instanciation d’un conteneur, consultez [Instancier un conteneur à partir d’une image conteneur téléchargée](#instantiate-a-container-from-a-downloaded-container-image).

Pour créer et récupérer des informations à partir d’une ressource Azure, effectuez les étapes suivantes :

1. Créez une ressource Azure dans le portail Azure.  
   Si vous souhaitez utiliser le conteneur Reconnaître le texte, vous devez d’abord créer une ressource Vision par ordinateur correspondante dans le portail Azure. Pour plus d’informations, consultez [Démarrage rapide : créer un compte Cognitive Services dans le portail Azure](../cognitive-services-apis-create-account.md).

   > [!IMPORTANT]
   > La ressource Vision par ordinateur doit utiliser le niveau tarifaire F0.

1. Obtenez l’URL de point de terminaison et la clé d’abonnement pour la ressource Azure.  
   Une fois la ressource Azure créée, vous devez utiliser l’URL de point de terminaison et la clé d’abonnement de cette ressource pour instancier le conteneur Reconnaître le texte correspondant. Vous pouvez copier l’URL de point de terminaison et la clé d’abonnement, respectivement dans les pages Démarrage rapide et Clés de la ressource Vision par ordinateur sur le portail Azure.

## <a name="log-in-to-the-private-container-registry"></a>Se connecter au registre de conteneurs privé

Il y a plusieurs façons de s’authentifier auprès du registre de conteneurs privé pour les conteneurs Cognitive Services, mais la méthode recommandée à partir de la ligne de commande est l’utilisation de la [CLI Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Utilisez la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/), comme indiqué dans l’exemple suivant, pour vous connecter à `containerpreview.azurecr.io`, le registre de conteneurs privé pour les conteneurs Cognitive Services. Remplacez *\<username\>* par le nom de l’utilisateur et *\<password\>* par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Si vous avez sécurisé vos informations d’identification dans un fichier texte, vous pouvez utiliser la commande `cat` pour concaténer son contenu à la commande `docker login`, comme indiqué dans l’exemple suivant. Remplacez *\<passwordFile\>* par le chemin et le nom du fichier texte contenant le mot de passe, et *\<username\>* par le nom d’utilisateur fourni dans vos informations d’identification.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Télécharger des images conteneurs à partir du registre de conteneurs privé

L’image conteneur pour le conteneur Reconnaître le texte est disponible à partir d’un registre de conteneurs Docker privé, nommé `containerpreview.azurecr.io`, dans Azure Container Registry. L’image conteneur pour le conteneur Reconnaître le texte doit être téléchargée à partir du référentiel pour exécuter le conteneur localement.

Utilisez la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du référentiel. Par exemple, pour télécharger la dernière image conteneur Reconnaître le texte à partir du référentiel, utilisez la commande suivante :

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

Pour obtenir une description complète des balises disponibles pour le conteneur Reconnaître le texte, consultez [Reconnaître le texte](https://go.microsoft.com/fwlink/?linkid=2018655) sur Docker Hub.

> [!TIP]
> Vous pouvez utiliser la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour lister vos images conteneurs téléchargées. Par exemple, la commande suivante liste l’ID, le référentiel et la balise de chaque image conteneur téléchargée dans un tableau :
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Instancier un conteneur à partir d’une image conteneur téléchargée

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour instancier un conteneur à partir d’une image conteneur téléchargée. Par exemple, la commande suivante :

* Instancie un conteneur à partir de l’image conteneur Reconnaître le texte
* Alloue deux cœurs de processeur et 8 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

Une fois l’instanciation effectuée, vous pouvez appeler des opérations à partir du conteneur à l’aide de l’URI hôte du conteneur. Par exemple, l’URI hôte suivant représente le conteneur Reconnaître le texte qui a été instancié dans l’exemple précédent :

```http
http://localhost:5000/
```

> [!IMPORTANT]
> Vous pouvez accéder à la [spécification OpenAPI](https://swagger.io/docs/specification/about/) (anciennement Swagger), qui décrit les opérations prises en charge par un conteneur instancié à partir de l’URI relatif `/swagger` pour ce conteneur. Par exemple, l’URI suivant permet d’accéder à la spécification OpenAPI pour le conteneur Reconnaître le texte instancié dans l’exemple précédent :
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Vous pouvez soit [appeler les opérations d’API REST](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) disponibles à partir de votre conteneur pour reconnaître le texte de façon asynchrone ou synchrone, soit utiliser la bibliothèque de client [SDK Vision par ordinateur Azure Cognitive Services](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) pour appeler ces opérations.  
> [!IMPORTANT]
> Vous devez disposer du SDK Vision par ordinateur Azure Cognitive Services version 3.2.0 ou ultérieure pour utiliser la bibliothèque de client avec votre conteneur.

### <a name="asynchronous-text-recognition"></a>Reconnaissance de texte asynchrone

Vous pouvez utiliser conjointement les opérations `POST /vision/v2.0/recognizeText` et `GET /vision/v2.0/textOperations/*{id}*` pour reconnaître de façon asynchrone le texte imprimé dans une image, ce qui est similaire à la façon dont le service Vision par ordinateur utilise ces opérations REST correspondantes. Le conteneur Reconnaître le texte reconnaît uniquement le texte imprimé pour le moment. Le texte manuscrit n’étant pas reconnu, le paramètre `mode` normalement spécifié pour l’opération de service Vision par ordinateur est ignoré par le conteneur Reconnaître le texte.

### <a name="synchronous-text-recognition"></a>Reconnaissance de texte synchrone

Vous pouvez utiliser l’opération `POST /vision/v2.0/recognizeTextDirect` pour reconnaître de façon synchrone le texte imprimé dans une image. Étant donné que cette opération est synchrone, le corps de la demande pour cette opération est identique à celui de l’opération `POST /vision/v2.0/recognizeText`. Toutefois, le corps de la demande pour cette opération est identique à celui retourné par l’opération `GET /vision/v2.0/textOperations/*{id}*`.

### <a name="billing"></a>Facturation

Le conteneur Reconnaître le texte envoie des informations de facturation à Azure à l’aide d’une ressource Vision par ordinateur correspondante sur votre compte Azure. Les options suivantes sont utilisées par le conteneur Reconnaître le texte à des fins de facturation :

| Option | Description |
|--------|-------------|
| `ApiKey` | Clé d’API de la ressource Vision par ordinateur servant à faire le suivi des informations de facturation.<br/>La valeur de cette option doit être une clé API pour la ressource Azure Vision par ordinateur provisionnée, spécifiée dans `Billing`. |
| `Billing` | Point de terminaison de la ressource Vision par ordinateur servant à faire le suivi des informations de facturation.<br/>La valeur de cette option doit être l’URI de point de terminaison d’une ressource Azure Vision par ordinateur provisionnée.|
| `Eula` | Indique que vous avez accepté la licence pour le conteneur.<br/>La valeur de cette option doit être `accept`. |

> [!IMPORTANT]
> Les trois options doivent être spécifiées avec des valeurs valides ; sinon, le conteneur ne démarre pas.

Pour plus d’informations sur ces options, consultez [Configurer des conteneurs](computer-vision-resource-container-config.md).

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Vision par ordinateur. En résumé :

* Vision par ordinateur fournit un conteneur Linux pour Docker afin de détecter et d’extraire le texte imprimé.
* Les images conteneurs sont téléchargées à partir d’un registre de conteneurs privé dans Azure.
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le SDK pour appeler des opérations dans des conteneurs Vision par ordinateur en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.
* ** Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services n’envoient pas les données des clients (p. ex., l’image ou le texte analysés) à Microsoft.  

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](computer-vision-resource-container-config.md).
* Pour en savoir plus sur la reconnaissance du texte imprimé et manuscrit, passez en revue [Vue d’ensemble de Vision par ordinateur](Home.md).  
* Pour plus d’informations sur les méthodes prises en charge par le conteneur, reportez-vous à l’[API Vision par ordinateur](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Pour résoudre les problèmes liés à la fonctionnalité Vision par ordinateur, reportez-vous au [Forum aux questions (FAQ)](FAQ.md).
