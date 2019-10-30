---
title: Entrée de texte dans Xamarin. iOS
description: Ce document décrit l’entrée de texte dans une application Xamarin. iOS. Elle traite de l’utilisation de champ UITextField et de UITextVIew à la fois par programmation et dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4833d8a03649341cb5c6d9f2692410b89e6cea4c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021819"
---
# <a name="text-input-in-xamarinios"></a>Entrée de texte dans Xamarin. iOS

L’acceptation de l’entrée de texte de l’utilisateur est effectuée avec la `UITextField` pour les entrées sur une seule ligne et UITextView pour le texte modifiable sur plusieurs lignes. Vous pouvez faire glisser l’un de ces contrôles sur un écran et double-cliquer pour définir le texte initial.

Les captures d’écran ci-dessous montrent les icônes de ces contrôles, situées dans le panneau boîte à outils dans Visual Studio pour Mac :

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Une fois que vous avez nommé la sortie et enregistré le fichier de table de montage séquentiel, Visual Studio pour Mac met à jour la C# classe partielle `.designer.cs` et vous pouvez ajouter du code qui référence le contrôle à votre fichier de classe. Chaque contrôle possède ses propres propriétés et événements uniques qui sont accessibles dans votre C# code.

 <a name="UITextField" />

## <a name="uitextfield"></a>UITextField

Le contrôle `UITextField` est le plus souvent utilisé pour accepter une seule ligne d’entrée de texte, par exemple un nom d’utilisateur ou un mot de passe. Voici quelques-unes des options disponibles pour personnaliser le contrôle :

 [![](text-input-images/image15a.png "UITextField Properties")](text-input-images/image15a.png#lightbox)

Ces contrôles sont expliqués ci-dessous :

- **Espace réservé** : facultatif. S’il est défini, il s’affiche lorsque le champ de texte est vide, généralement pour expliquer à l’utilisateur quelle entrée est attendue.
- **Bouton Effacer** : contrôle le moment où le bouton Effacer standard (le cercle gris avec (X)) apparaît dans le champ de texte, ce qui permet à l’utilisateur d’effacer rapidement le texte. Elle peut être masquée définitivement, visible définitivement ou affichée, selon que le champ est modifié ou non.
- **Taille de police minimale** et **ajuster pour ajuster** : permet d’ajuster automatiquement la taille de police pour qu’elle s’ajuste au texte le plus long et d’empêcher la troncation, mais limitée à une taille inférieure à la taille spécifiée.
- Mise en **majuscules** : indique s’il faut mettre automatiquement en majuscules les mots, les phrases ou toutes les entrées.
- **Correction** : indique si la vérification de l’orthographe et les suggestions sont activées.
- **Clavier** : contrôle le style de clavier affiché pour l’entrée et, par conséquent, les touches disponibles sur le clavier. Cela comprend le pavé numérique, le pavé téléphonique, l’adresse de messagerie et l’URL, ainsi que d’autres options.
- **Apparence** : contrôle le style d’apparence du clavier et est sombre ou clair.
- **Clé de retour** : modifiez l’étiquette sur la clé de retour pour mieux refléter l’action qui sera entreprise. Les valeurs prises en charge sont les suivantes : Go, Join, Next, route, Done et Search.
- **Secure** : indique si l’entrée est masquée (par exemple, pour une entrée de mot de passe).

Si un champ UITextField appelé `textfield1` a été ajouté à un écran avec le concepteur, vous pouvez définir ou modifier ses propriétés dans C# comme suit :

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin. iOS fournit des énumérations, le cas échéant, pour faciliter la sélection des paramètres de votre choix, tels que le `UIKeyboardType` et `UIReturnKeyType` dans l’extrait de code ci-dessus.

### <a name="display-text-programmatically"></a>Afficher du texte par programmation

Si vous ne souhaitez pas concevoir votre écran avec le concepteur ou si vous souhaitez ajouter dynamiquement du texte lors de l’exécution, vous pouvez créer et afficher un champ UITextField par programme dans la méthode `ViewDidLoad` d’un contrôleur d’affichage comme suit :

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />

## <a name="uitextview"></a>UITextView

Le contrôle `UITextView` peut être utilisé pour afficher du texte en lecture seule ou pour accepter une entrée de texte sur plusieurs lignes. Il possède un grand nombre des mêmes options que le `UITextField` (par exemple, la mise en majuscules, la correction, etc.).

 [![](text-input-images/image16a.png "UITextView Properties")](text-input-images/image16a.png#lightbox)

Les propriétés spécifiques sont les suivantes :

- **Comportement** : indique si le texte est modifiable ou en lecture seule.
- **Détection** : détecte et convertit les données entrées en éléments interactives, tels que les numéros de téléphone qui peuvent déclencher un appel, les adresses qui deviennent des liens vers les mappages, les URL qui s’ouvrent dans Safari ou les dates et heures qui deviennent des événements dans le calendrier.

Si un UITextView a été ajouté à un écran avec le concepteur, vous pouvez définir ou modifier ses propriétés comme suit :

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
