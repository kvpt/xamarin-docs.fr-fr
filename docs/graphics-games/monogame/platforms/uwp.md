---
title: Création d’un projet MonoGame UWP
description: MonoGame peut être utilisé pour créer des jeux et les applications pour plateforme Windows universelle, ciblage de que plusieurs appareils avec un code base et un ensemble de contenu.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 7db73759cb4a1b1a8d7fe40426b03a163c3ebdc4
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831061"
---
# <a name="creating-a-monogame-uwp-project"></a>Création d’un projet MonoGame UWP

_MonoGame peut être utilisé pour créer des jeux et les applications pour plateforme Windows universelle, ciblage de que plusieurs appareils avec un code base et un ensemble de contenu._

Cette procédure pas à pas couvre la création du projet MonoGame Universal Windows Platform (UWP) et chargement du contenu. Les applications UWP peuvent s’exécuter sur tous les appareils Windows 10, y compris les ordinateurs de bureau, tablettes, les téléphones Windows et Xbox One.

Cette procédure pas à pas crée un projet vide qui affiche un *bleu vif bleu* en arrière-plan (la couleur d’arrière-plan traditionnelle des applications XNA).

## <a name="requirements"></a>Configuration requise

Développement d’applications de MonoGame UWP nécessite :

- Système d’exploitation Windows 10
- N’importe quelle version de Visual Studio 2017
- Outils de développement Windows 10
- Appareil de paramètre pour le mode développeur
- [MonoGame 3.7.1 pour Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) ou une version ultérieure

Pour plus d’informations, consultez ce [page sur la configuration pour le développement Windows 10 UWP](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Jeux Xbox One peuvent être développés sur du matériel de Xbox One de vente au détail. Logiciel supplémentaire est requis sur le développement PC et Xbox One. Pour plus d’informations sur la configuration d’une Xbox One pour le développement de jeux, consultez cette page sur [configuration d’une Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Création d’un modèle vide

Une fois que toutes les ressources requises ont été installés et le mode développeur a été activé sur l’ordinateur Windows 10, nous pouvons créer un nouveau projet MonoGame à l’aide de Visual Studio en procédant comme suit :

1. Sélectionnez **fichier** > **nouveau** > **projet...**
1. Sélectionnez le **installé** > **modèles** > **Visual C#**   >  **MonoGame** catégorie :

    ![](uwp-images/image1.png "Catégorie de MonoGame")

1. Sélectionnez le **MonoGame Windows 10 universel projet** option :

    ![](uwp-images/image2.png "Sélectionnez l’option de projet universelle de MonoGame Windows 10")

1. Entrez un nom pour le nouveau projet et cliquez sur **OK**.
Si Visual Studio affiche toutes les erreurs après avoir cliqué sur OK, vérifiez que les outils de Windows 10 sont installés et que l’appareil est en mode développeur.

Une fois que Visual Studio a terminé la création du modèle, nous pouvons exécuter pour voir le projet vide en cours d’exécution :

![](uwp-images/image3.png "Une fois que Visual Studio a terminé la création du modèle, exécutez-le pour voir le projet vide en cours d’exécution")

Les nombres dans les angles fournissent des informations de diagnostic. Ces informations peuvent être supprimées en supprimant le code dans `App.xaml.cs` dans le `DEBUG` bloquer la `OnLaunched` méthode :


```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>En cours d’exécution sur Xbox One

Les projets UWP peuvent déployer sur n’importe quel appareil Windows 10 à partir du même projet. Après avoir configuré l’ordinateur de développement Windows 10 et Xbox One, les applications UWP peuvent être déployées par la cible de commutation à l’ordinateur distant et en entrant adresse IP de la Xbox One :

![](uwp-images/remote.png "Les applications UWP peuvent être déployées par la cible de commutation à l’ordinateur distant et en entrant l’adresse IP de ceux de Xbox")

Sur Xbox One, la bordure blanche représente la zone de non-safe de téléviseurs. Pour plus d’informations, consultez le [section de zone protégée](#safe-area-on-xbox-one).

![](uwp-images/safearea.png "Sur Xbox One, la bordure blanche représente la zone non sécurisée pour les téléviseurs")

### <a name="safe-area-on-xbox-one"></a>Zone protégée sur Xbox One

Développement de jeux pour les consoles nécessite de tenir compte de la zone protégée, qui est une zone située au centre de l’écran qui doit contenir tous les visuels critiques (par exemple, l’interface utilisateur ou HUD). La zone en dehors de la zone protégée n’est pas garantie pour être visibles sur tous les téléviseurs, visuels placés dans cette zone peuvent être partiellement ou totalement invisibles sur certains écrans.

Le modèle de MonoGame pour Xbox One considère que la zone protégée et rend sous la forme d’une bordure blanche. Cette zone apparaît également lors de l’exécution dans le jeu `Window.ClientBounds` comme indiqué dans cette image de la fenêtre Espion dans Visual Studio. Notez que la hauteur des limites de client est 1016, en dépit de la résolution d’écran de 1920 x 1080 :

![](uwp-images/clientbounds.png "Notez que la hauteur des limites de client est 1016, en dépit de la résolution d’écran de 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Référencement du contenu dans les projets UWP

Contenu dans les projets de MonoGame peut être référencée directement à partir de fichiers ou via le [Pipeline de contenu MonoGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md). Petits projets jeu peuvent bénéficier de la simplicité de chargement à partir du fichier. Projets de grande taille bénéficieront d’à l’aide du pipeline de contenus pour optimiser le contenu pour réduire la taille et le temps de chargement. Contrairement à XNA sur la Xbox 360, le `System.IO.File` classe est disponible sur les applications UWP une Xbox.

Pour plus d’informations sur le chargement de contenu à l’aide du pipeline de contenu, consultez la [Guide de Pipeline de contenu](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

### <a name="loading-content-from-file"></a>Chargement du contenu à partir du fichier

Contrairement à iOS et Android, les projets UWP peuvent référencer des fichiers par rapport à l’exécutable. Des jeux simples permettent ce contenu de charge technique sans avoir besoin de modifier et générer le projet de pipeline de contenu.

Pour charger un `Texture2D` à partir du fichier :

1. Ajouter un fichier .png dans le dossier de contenu dans le projet UWP. Ajout de contenu dans le dossier Content est une convention dans XNA et MonoGame.
1. Avec le bouton droit sur le fichier PNG nouvellement ajouté, puis sélectionnez Propriétés.
1. Modifier le **Copy to Output Directory** à **Copier si plus récent**.
1. Ajoutez le code suivant à la méthode d’initialisation de votre jeu pour charger un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Pour plus d’informations sur l’utilisation d’un `Texture2D`, consultez le [Introduction à MonoGame guide](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Récapitulatif

Ce guide explique comment créer un nouveau projet UWP et les considérations spécifiques à UWP lors du chargement de fichiers. Les développeurs qui souhaitent créer des jeux UWP complètes en savoir plus sur MonoGame dans le [Introduction au Guide de MonoGame](~/graphics-games/monogame/introduction/index.md).
