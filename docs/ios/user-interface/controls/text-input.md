---
title: Entrée de texte dans Xamarin.iOS
description: Ce document décrit l’entrée de texte dans une application Xamarin.iOS. Il aborde l’utilisation de champ UITextField et UITextVIew par programmation et dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a4d23984d4fcfd0776fd6b3537d5a257f70e7837
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827127"
---
# <a name="text-input-in-xamarinios"></a>Entrée de texte dans Xamarin.iOS

Accepter une entrée de texte utilisateur s’effectue avec la `UITextField` pour les entrées de ligne et de UITextView pour le texte modifiable multiligne. Vous pouvez faire glisser un de ces contrôles sur un écran et double-cliquez dessus pour définir le texte initial.

Les captures d’écran ci-dessous montrent les icônes de ces contrôles, situés dans le panneau boîte à outils dans Visual Studio pour Mac :

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Une fois que vous avez nommé l’outlet et enregistré le fichier de Storyboard, Visual Studio pour Mac met à jour le `.designer.cs` classe partielle et vous pouvez ajouter C# le code qui référence le contrôle à votre fichier de classe. Chaque contrôle possède ses propres propriétés uniques et les événements qui sont accessibles dans votre C# code.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

Le `UITextField` contrôle est plus souvent utilisé pour accepter une seule ligne d’entrée de texte tel qu’un nom d’utilisateur ou le mot de passe. Certaines des options disponibles pour la personnalisation du contrôle sont présentées ici :

 [![](text-input-images/image15a.png "Propriétés de champ UITextField")](text-input-images/image15a.png#lightbox)

Ces contrôles sont expliquées ci-dessous :

-  **Espace réservé** : cette étape est facultative. Si la valeur, il est affiché lorsque le champ de texte est vide, généralement à expliquer à l’utilisateur quelle entrée est attendue.
-  **Bouton Effacer** – ce paramètre contrôle lorsque le bouton Effacer standard (le cercle gris avec (X)) s’affiche dans le champ de texte, comme un moyen de l’utilisateur afin d’effacer le texte rapidement. Il peut être masquées définitivement, définitivement visibles ou affichées, selon si le champ est en cours de modification.
-  **Taille de police min** et **ajuster pour ajuster** – permet la taille de police être ajustée automatiquement pour ajuster au texte plus longue et empêcher la troncation, mais limitée à aucun inférieure à la taille spécifiée.
-  **Mise en majuscules** – s’il faut mettre automatiquement en majuscule mots, phrases ou toutes les entrées.
-  **Correction** : indique si la vérification orthographique et les suggestions sont activées.
-  **Clavier** : contrôles le style de clavier affiché pour l’entrée, et par conséquent, les clés sont disponibles sur le clavier. Cela inclut le pavé numérique, remplissage du téléphone, E-mail, URL, ainsi que d’autres options.
-  **Apparence** : contrôle le style d’apparence du clavier et seront soit sombre ou clair à thème.
-  **Touche retour** : modifier l’étiquette sur la touche Retour afin de mieux refléter les mesures. Valeurs prises en charge incluent Go, Join, Next, itinéraire, terminé et recherche.
-  **Sécuriser** – indique si l’entrée est masquée (par exemple pour une entrée de mot de passe).


Si elle est appelée un champ UITextField `textfield1` a été ajouté à un écran avec le concepteur, vous pouvez définir ou modifier ses propriétés dans C# comme suit :

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS fournit des énumérations approprié pour le rendre facile de sélectionner les paramètres que vous voulez, comme le `UIKeyboardType` et `UIReturnKeyType` dans l’extrait de code ci-dessus.

### <a name="display-text-programmatically"></a>Afficher le texte par programmation

Si vous ne souhaitez pas concevoir votre écran avec le concepteur ou si vous souhaitez ajouter du texte de façon dynamique lors de l’exécution, vous pouvez créer et afficher un champ UITextField par programmation dans le `ViewDidLoad` (méthode) d’un contrôleur d’affichage comme suit :

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

Le `UITextView` contrôle peut être utilisé pour afficher le texte en lecture seule ou d’accepter l’entrée de texte multiligne. Il possède de nombreuses options mêmes comme le `UITextField` (telle que de la mise en majuscules, Correction, etc.).

 [![](text-input-images/image16a.png "Propriétés de UITextView")](text-input-images/image16a.png#lightbox)

Propriétés spécifiques sont les suivantes :

-  **Comportement** : indique si le texte est modifiable ou en lecture seule.
-  **Détection** – détecte et convertit les données entrées dans les éléments interactifs tels que les numéros de téléphone qui peuvent déclencher un appel, qui deviennent des adresses des liens vers les cartes, les URL qui s’ouvrent dans Safari ou dates et heures qui devienne des événements dans le calendrier.


Si un UITextView a été ajouté à un écran avec le concepteur, vous pouvez définir ou modifier ses propriétés comme suit :

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
