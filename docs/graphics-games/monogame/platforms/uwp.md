---
title: Création d’un projet UWP monojeu
description: Vous pouvez utiliser un monojeu pour créer des jeux et des applications pour plateforme Windows universelle, ciblant plusieurs appareils avec un code base et un ensemble de contenu.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 367f44104b0d6049b8d65ab6e3b3f38c703cdb3f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198712"
---
# <a name="creating-a-monogame-uwp-project"></a>Création d’un projet UWP monojeu

_Vous pouvez utiliser un monojeu pour créer des jeux et des applications pour plateforme Windows universelle, ciblant plusieurs appareils avec un code base et un ensemble de contenu._

Cette procédure pas à pas aborde la création et le chargement de contenu de plateforme Windows universelle monojeu (UWP). Les applications UWP peuvent s’exécuter sur tous les appareils Windows 10, y compris les ordinateurs de bureau, les tablettes, les téléphones Windows et Xbox One.

Cette procédure pas à pas crée un projet vide qui affiche un arrière-plan *bleu bleuet* (la couleur d’arrière-plan traditionnelle des applications XNA).

## <a name="requirements"></a>Configuration requise

Le développement d’applications UWP monojeu requiert:

- Système d’exploitation Windows 10
- N’importe quelle version de Visual Studio 2017
- Outils de développement Windows 10
- Configuration de l’appareil en mode développeur
- [Monojeu 3.7.1 pour Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) ou une version plus récente

Pour plus d’informations, consultez cette [page sur la configuration du développement UWP Windows 10](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Les jeux Xbox One peuvent être développés sur un matériel Xbox de détail. Des logiciels supplémentaires sont nécessaires sur le PC en développement et sur le Xbox One. Pour plus d’informations sur la configuration de Xbox One pour le développement de jeux, consultez cette page sur la [configuration d’une Xbox](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Création d’un modèle vide

Une fois que toutes les ressources requises ont été installées et que le mode développeur a été activé sur l’ordinateur Windows 10, nous pouvons créer un nouveau projet de monojeu à l’aide de Visual Studio en procédant comme suit:

1. Sélectionnez **fichier** > nouveauprojet >  **...**
1. Sélectionnez les > **modèles**  > **C#** installés catégorie de monojeu visuel: > 

    ![](uwp-images/image1.png "Catégorie monojeu")

1. Sélectionnez l’option de **projet universelle de Windows 10 à jeux** d’éléments:

    ![](uwp-images/image2.png "Sélectionnez l’option de projet universel Windows 10 monojeu")

1. Entrez un nom pour le nouveau projet, puis cliquez sur **OK**.
Si Visual Studio affiche des erreurs après avoir cliqué sur OK, vérifiez que les outils Windows 10 sont installés et que l’appareil est en mode développeur.

Une fois que Visual Studio a terminé la création du modèle, nous pouvons l’exécuter pour voir le projet vide qui s’exécute:

![](uwp-images/image3.png "Une fois que Visual Studio a terminé la création du modèle, exécutez-le pour voir le projet vide en cours d’exécution")

Les nombres dans les angles fournissent des informations de diagnostic. Ces informations peuvent être supprimées en supprimant le `App.xaml.cs` code dans `DEBUG` le bloc de `OnLaunched` la méthode:


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

## <a name="running-on-xbox-one"></a>Exécution sur Xbox One

Les projets UWP peuvent être déployés sur n’importe quel appareil Windows 10 à partir du même projet. Après avoir configuré l’ordinateur de développement Windows 10 et le Xbox One, vous pouvez déployer les applications UWP en basculant la cible sur la machine distante et en entrant l’adresse IP de l’ordinateur Xbox 1:

![](uwp-images/remote.png "Les applications UWP peuvent être déployées en basculant la cible sur la machine distante et en entrant l’adresse IP des Xbox")

Sur Xbox One, la bordure blanche représente la zone non sécurisée pour les téléviseurs. Pour plus d’informations, consultez la [section sécurité](#safe-area-on-xbox-one)de la zone sécurisée.

![](uwp-images/safearea.png "Sur Xbox One, la bordure blanche représente la zone non sécurisée pour les téléviseurs")

### <a name="safe-area-on-xbox-one"></a>Zone sécurisée sur Xbox One

Le développement de jeux pour les consoles nécessite la prise en compte de la zone sécurisée, qui est une zone au centre de l’écran qui doit contenir tous les éléments visuels critiques (tels que l’interface utilisateur ou HUD). Il n’est pas garanti que la zone en dehors de la zone sécurisée soit visible sur tous les téléviseurs. ainsi, les éléments visuels placés dans cette zone peuvent être partiellement ou complètement invisibles sur certains écrans.

Le modèle monojeu pour Xbox One prend en compte la zone sécurisée et l’affiche sous forme de bordure blanche. Cette zone est également reflétée au moment de l’exécution `Window.ClientBounds` dans la propriété du jeu, comme indiqué dans cette image de la fenêtre Espion dans Visual Studio. Notez que la hauteur des limites du client est 1016, malgré la résolution d’affichage de 1920 x 1080:

![](uwp-images/clientbounds.png "Notez que la hauteur des limites du client est 1016, malgré la résolution d’affichage de 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Référencement de contenu dans des projets UWP

Le contenu des projets monojeu peut être référencé directement à partir du fichier ou via le pipeline de [contenu monojeu](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md). Les petits projets de jeu peuvent tirer parti de la simplicité du chargement à partir d’un fichier. Les projets plus volumineux tireront parti de l’utilisation du pipeline de contenu pour optimiser le contenu afin de réduire la taille et les temps de chargement. Contrairement à XNA sur la Xbox 360, `System.IO.File` la classe est disponible sur les applications Xbox One UWP.

Pour plus d’informations sur le chargement de contenu à l’aide du pipeline de contenu, consultez le Guide de pipeline de [contenu](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

### <a name="loading-content-from-file"></a>Chargement du contenu à partir d’un fichier

Contrairement à iOS et Android, les projets UWP peuvent référencer des fichiers relatifs à l’exécutable. Les jeux simples peuvent utiliser cette technique pour charger du contenu sans avoir à modifier et générer le projet de pipeline de contenu.

Pour charger un `Texture2D` à partir d’un fichier:

1. Ajoutez un fichier. png au dossier Content du projet UWP. L’ajout de contenu au dossier Content est une Convention dans XNA et monogame.
1. Cliquez avec le bouton droit sur le PNG nouvellement ajouté et sélectionnez Propriétés.
1. Remplacez la **copie dans le répertoire de sortie** par **copier si plus récent**.
1. Ajoutez le code suivant à la méthode Initialize de votre jeu pour charger un `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Pour plus d’informations sur l' `Texture2D`utilisation d’un, consultez le [Guide introduction au Guide de monojeu](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Récapitulatif

Ce guide explique comment créer un projet UWP et des considérations spécifiques au UWP lors du chargement de fichiers. Les développeurs qui souhaitent créer des jeux UWP complets peuvent en savoir plus sur le monojeu dans le [Guide présentation](~/graphics-games/monogame/introduction/index.md)du monojeu.
