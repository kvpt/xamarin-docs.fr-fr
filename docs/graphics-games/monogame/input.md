---
title: Référence du boîtier monojeu
description: Ce document décrit le jeu de boîtiers, une classe multiplateforme pour l’accès aux périphériques d’entrée en monojeu. Il explique comment lire les entrées à partir du boîtier de la manette et fournit un exemple de code.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 11b1cfda435e97b09f9d1eded22f11f912d1783d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934028"
---
# <a name="monogame-gamepad-reference"></a>Référence du boîtier monojeu

_Le boîtier de connexion est une classe multiplateforme standard permettant d’accéder aux appareils d’entrée en monojeu._

`GamePad`peut être utilisé pour lire les entrées d’appareils d’entrée sur plusieurs plateformes monojeu. Ce guide montre comment utiliser la classe de boîtier. Étant donné que chaque périphérique d’entrée diffère dans la disposition et le nombre de boutons qu’il fournit, ce guide inclut des diagrammes qui illustrent les différents mappages d’appareils.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>Boîtier en tant que remplacement de Xbox360GamePad

L’API XNA d’origine a fourni la `Xbox360GamePad` classe pour la lecture d’entrée à partir d’un contrôleur de jeu sur la Xbox 360 ou le PC. Le monojeu a été remplacé par une `GamePad` classe, car les contrôleurs Xbox 360 ne peuvent pas être utilisés sur la plupart des plateformes monojeu (comme iOS ou Xbox). Malgré le changement de nom, l’utilisation de la `GamePad` classe est semblable à celle de la `Xbox360GamePad` classe.

## <a name="reading-input-from-gamepad"></a>Lecture de l’entrée à partir du boîtier

La `GamePad` classe fournit un moyen standardisé de lire les entrées sur toute plateforme monojeu. Il fournit des informations par le biais de deux méthodes :

- `GetState`: retourne l’état actuel des boutons du contrôleur, des bâtons analogiques et d-pad.
- `GetCapabilities`: retourne des informations sur les fonctionnalités du matériel, par exemple si le contrôleur a certains boutons ou prend en charge les vibrations.

### <a name="example-moving-a-character"></a>Exemple : déplacement d’un caractère

Le code suivant montre comment vous pouvez utiliser la barre de défilement gauche pour déplacer un caractère en définissant ses `XVelocity` `YVelocity` Propriétés et. Ce code suppose que `characterInstance` est une instance d’un objet qui a les `XVelocity` `YVelocity` Propriétés et :

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Exemple : détection des notifications push

`GamePadState`fournit des informations sur l’état actuel du contrôleur, par exemple si vous appuyez sur un certain bouton. Certaines actions, telles que la création d’un saut de ligne, nécessitent de vérifier si le bouton a fait l’objet d’un push (n’a pas été placé en dernier, mais qu’il est en dessous de ce cadre) ou relâché (était en fin de dernière image, mais pas dans le cadre).

Pour exécuter ce type de logique, les variables locales qui stockent les frames précédents `GamePadState` et les images en cours `GamePadState` doivent être créées. L’exemple suivant montre comment stocker et utiliser le frame précédent `GamePadState` pour implémenter le saut :

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed =
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>Exemple : recherche de boutons

`GetCapabilities`peut être utilisé pour vérifier si un contrôleur possède un certain matériel, tel qu’un bouton ou un bâton analogique particulier. Le code suivant montre comment rechercher les boutons B et Y sur un contrôleur dans un jeu, ce qui nécessite la présence des deux boutons :

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

les applications iOS prennent en charge l’entrée de contrôleur de jeu sans fil.

> [!IMPORTANT]
> Les packages NuGet pour monogame 3,5 n’incluent pas la prise en charge des contrôleurs de jeu sans fil. L’utilisation de la classe de manette sur iOS requiert la création d’un monojeu 3,5 à partir de la source ou l’utilisation de fichiers binaires NuGet à monojeu 3,6.

### <a name="ios-game-controller"></a>Contrôleur de jeu iOS

La `GamePad` classe retourne les propriétés lues à partir des contrôleurs sans fil. Les propriétés dans `GamePad` fournissent une bonne couverture pour le matériel du contrôleur iOS standard, comme indiqué dans le diagramme suivant :

![Les propriétés du boîtier de commande fournissent une bonne couverture pour le matériel du contrôleur iOS standard, comme illustré dans ce diagramme](input-images/image1.png)

## <a name="apple-tv"></a>TV Apple

Les jeux Apple TV peuvent utiliser les contrôleurs de jeu distants ou sans fil Siri pour l’entrée.

### <a name="siri-remote"></a>Siri à distance

*Siri Remote* est l’appareil d’entrée natif pour Apple TV. Bien que les valeurs de la Siri distante puissent être lues via des événements (comme indiqué dans le [Guide des contrôleurs distants et Bluetooth Siri](~/ios/tvos/platform/remote-bluetooth.md)), la `GamePad` classe peut retourner des valeurs à partir de la télécommande Siri.

Notez que `GamePad` peut lire uniquement les entrées du bouton de lecture et de la surface tactile :

![Notez que le boîtier d’accès peut uniquement lire les entrées du bouton de lecture et de la surface tactile](input-images/image2.png)

Étant donné que le déplacement de la surface tactile est lu via la `DPad` propriété, les valeurs de mouvement sont signalées à l’aide de la `ButtonState` classe. En d’autres termes, les valeurs sont uniquement disponibles en tant que `ButtonState.Pressed` ou `ButtonState.Released` , par opposition aux valeurs numériques ou aux gestes.

### <a name="apple-tv-game-controller"></a>Contrôleur de jeu Apple TV

Les contrôleurs de jeu pour Apple TV se comportent de la même façon que les contrôleurs de jeu pour les applications iOS. Pour plus d’informations, consultez la section [contrôleurs de jeu iOS](#ios-game-controller) . 

## <a name="xbox-one"></a>Xbox One

La console Xbox One prend en charge la lecture d’entrées à partir d’un contrôleur de jeu Xbox One.

### <a name="xbox-one-game-controller"></a>Contrôleur de jeu Xbox One

Le contrôleur de jeu Xbox One est le périphérique d’entrée le plus courant pour Xbox One. La `GamePad` classe fournit des valeurs d’entrée à partir du matériel du contrôleur de jeu.

![La classe de boîtier de commande fournit des valeurs d’entrée à partir du matériel du contrôleur de jeu](input-images/image3.png)

## <a name="summary"></a>Résumé

Ce guide a fourni une vue d’ensemble de la classe de monojeu `GamePad` , comment implémenter une logique de lecture d’entrée et des diagrammes d' `GamePad` implémentations courantes.

## <a name="related-links"></a>Liens associés

- [Boîtier monojeu](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
